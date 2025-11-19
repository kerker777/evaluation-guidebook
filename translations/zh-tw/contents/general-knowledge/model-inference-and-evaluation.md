# 模型推論與評估

## 簡介
當前的大型語言模型運作方式很簡單：給定一些文字作為輸入，它們已經學會預測合理的後續內容。

這個過程分為兩個步驟。
### 分詞
輸入文字（在推論時稱為 *prompt*）首先會被切分成 *token*，這些是文字的小單位（可以是一個或多個字元，最多到詞彙層級），每個都對應一個數字。模型能夠解析的所有 token 範圍稱為其 *詞彙表*。*（要更深入了解這點，請閱讀[分詞](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/general-knowledge/tokenization.md)頁面）*。

### 預測

![](https://github.com/huggingface/evaluation-guidebook/blob/main/assets/llm_tk_1.png?raw=true)

從這個輸入文字開始，LLM 會在整個詞彙表上產生最可能的下一個 token 的機率分布。為了獲得持續的生成，我們可以選擇機率最高的 token（加減一些隨機性以獲得更有趣的輸出）作為下一個 token，然後重複這個操作，將新的 token 作為提示詞的結尾，依此類推。

## 你想要預測什麼？
LLM 評估主要分為兩類：
- 給定提示詞和一個（或多個）答案，我的模型對這些答案的機率是多少？
- 給定提示詞，我的模型會生成什麼文字？
### 對數似然評估
對於對數似然評估，我們想要的是在給定提示詞的情況下一個或多個選項的條件機率——換句話說，給定輸入後得到特定續接內容的可能性有多大？
因此：
- 我們將每個選項與提示詞連接，然後傳遞給 LLM，它會根據前面的 token 輸出每個 token 的 logit
- 我們只保留最後的 logit（與選項 token 相關的），並應用 log softmax 來獲得對數機率（範圍是 `[-inf, 0]` 而不是 `[0-1]`）
- 然後我們將所有個別 token 的對數機率相加，得到整體選項的對數機率
- 最後我們可以根據選項長度進行正規化

![](https://github.com/huggingface/evaluation-guidebook/blob/main/assets/llm_logprob.png?raw=true)

這讓我們能夠應用以下其中一種指標：
- 從多個選項中獲得模型偏好的答案，如上圖所示。（*然而，這可能會對那些在自由生成時會產生其他內容的模型的分數有利，例如圖中的 `Zygote`。*）
- 測試單一選項的機率是否超過 0.5
- 研究模型校準。一個校準良好的模型是指正確答案具有最高機率的模型。
  *（要了解更多關於校準的資訊，可以查看 Anthropic 的[這篇論文](https://arxiv.org/abs/2207.05221)，內容包括什麼是校準、如何檢測以及如何訓練模型使其校準良好，以及[這篇論文](https://arxiv.org/abs/2311.14648)關於校準可能的一些限制）。*

### 生成式評估
對於生成式評估，我們想要的是模型在給定輸入提示詞後生成的文字。

這是以自迴歸的方式獲得的：我們將提示詞傳遞給模型，查看最可能的下一個 token，選擇它作為模型的「第一個選擇 token」，然後重複直到達到生成結束條件（最大長度、停止生成的特殊 token 等）。模型生成的所有 token 被視為它對提示詞的答案。

![](https://github.com/huggingface/evaluation-guidebook/blob/main/assets/llm_gen.png?raw=true)



然後我們可以將這個生成結果與參考答案進行比較，並評分兩者之間的距離（使用簡單的指標如完全匹配、更複雜的指標如 BLEU，或使用模型作為評審）。

### 深入了解
-  ⭐ [關於評估 MMLU 的多種方法的部落格文章](https://huggingface.co/blog/open-llm-leaderboard-mmlu)，由我在 Hugging Face 的團隊撰寫。如果你想深入了解多選題對數似然評估與生成式評估之間的差異，包括這對分數變化的意義，我推薦閱讀這篇文章
	- 上述插圖來自該部落格，由 Thom Wolf 製作
- ⭐ [上述推論方法的優美數學形式化](https://arxiv.org/abs/2405.14782v2)，來自 EleutherAI。可以直接查看附錄。
## 限制模型輸出
在許多情況下，我們希望模型輸出遵循特定格式，例如為了將它們與參考答案進行比較。
### 使用提示詞
最簡單的方法是添加一個任務提示詞，其中包含關於模型應該如何回答的非常具體的指示（`以數字提供數值答案。`、`不要使用縮寫。`等）。

它不一定總是有效，但對於高能力模型應該足夠好。這是我們在 [GAIA](https://huggingface.co/papers/2311.12983) 論文中採用的方法，如果你想要一些靈感，可以在[排行榜](https://huggingface.co/spaces/gaia-benchmark/leaderboard)的 Submission 標籤中找到我們的任務提示詞。
### 少樣本學習與情境學習
另一種方法是透過所謂的「情境學習」來限制模型。透過在提示詞中提供範例（稱為 `few-shot prompting`），模型會隱性地傾向於對實際樣本遵循重複的提示詞格式。

這個方法在 2023 年底之前整體上運作得相當好！然而，指令調整方法的廣泛採用，以及在模型預訓練後期階段（持續預訓練）加入指令資料，似乎已經讓較新的模型偏向特定的輸出格式（[這裡](https://arxiv.org/abs/2407.07890)稱之為 `Training on the test task`，我會稱之為 `過度擬合提示詞格式`）。對於上下文大小較小的舊模型來說，這也是一個受限的方法，因為一些少樣本範例可能無法放入上下文視窗中。
### 結構化文字生成
結構化文字生成會限制輸出遵循給定的路徑，例如由文法或正規表達式定義。`outlines` 函式庫使用有限狀態機來實現這一點，非常巧妙。（還有其他方法，例如使用交錯生成來生成 JSON，但有限狀態機是我的最愛）。

要更深入了解使用結構化生成時會發生什麼，你可以查看我們一起撰寫的[部落格文章](https://huggingface.co/blog/evaluation-structured-outputs)：結構化生成減少了評估中的提示詞變異，並使結果和排名更加穩定。你也可以查看 `outlines` 的整體[部落格](https://blog.dottxt.co/)，以了解與結構化生成相關的有趣實作和觀察。

然而，一些最近的[研究](https://arxiv.org/abs/2408.02442)似乎顯示，結構化生成可能會降低模型在某些任務（如推理）上的性能，因為它將先驗分布移動得離預期機率分布太遠。

### 深入了解
-  ⭐ [理解使用結構化生成時的有限狀態機](https://blog.dottxt.co/coalescence.html)，來自 Outlines。對於他們的方法如何運作的超級清晰指南！
- [outlines 方法論文](https://arxiv.org/abs/2307.09702)，上述內容更學術性的解釋
- [交錯生成](https://github.com/guidance-ai/guidance?tab=readme-ov-file#guidance-acceleration)，另一種針對某些特定輸出格式限制生成的方法
