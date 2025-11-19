(originally posted at https://huggingface.co/blog/2023-in-llms)

# 2023,開源大型語言模型之年
2023 年見證了大眾對大型語言模型(Large Language Models, LLMs)興趣的激增,現在大多數人對它們的功能已有基本了解,關於開源與閉源的公開辯論也觸及了更廣泛的受眾。在 Hugging Face,我們一直密切關注開源模型,因為它們讓研究變得可重現、賦予社群參與 AI 模型開發的能力、使模型的偏見和限制更容易被檢視,並透過促進檢查點的重複使用來降低整體碳排放(以及[許多其他好處](https://huggingface.co/papers/2302.04844))。

那麼讓我們來回顧一下開源大型語言模型這一年的發展!

*為了讓這份文件長度適中,我們不會探討程式碼模型。*

## 🍜 預訓練大型語言模型的配方
首先,如何取得一個大型語言模型?(如果您已經了解,可以略過這一節!)

模型**架構**(其程式碼)描述了其具體實作和數學形式:它是所有參數的列表,以及它們如何與輸入互動。目前,大多數高效能的 LLM 都是「僅解碼器」Transformer 架構的變體(更多細節請見[原始 Transformer 論文](https://huggingface.co/papers/1706.03762))。

**訓練資料集**包含模型訓練時(也就是學習參數時)使用的所有範例和文件,因此決定了學習到的特定模式。大多數情況下,這些文件包含文字,可能是自然語言(例如:法語、英語、中文)、程式語言(例如:Python、C)或任何可以用文字表達的結構化資料(例如:markdown 或 latex 格式的表格、方程式等)。

**分詞器**定義了訓練資料集中的文字如何轉換為數字(因為模型是一個數學函數,因此需要數字作為輸入)。分詞是透過將文字轉換為稱為標記(tokens)的子單位來完成的(根據分詞方法,可以是單詞、子詞或字元)。分詞器的詞彙表大小表示它知道多少不同的標記,通常在 32k 到 200k 之間。資料集的大小通常以它包含的**標記數量**來衡量,這些天從數千億個標記到數兆個標記不等!

**訓練超參數**則定義了模型的訓練方式。參數應該如何調整以適應每個新範例?模型應該以多快的速度更新?

一旦選定這些參數,您只需要 1) 大量的運算能力來訓練模型,以及 2) 有能力(且友善)的人來執行和監控訓練。訓練本身包括實例化架構(在用於訓練的硬體上建立矩陣)並使用上述超參數在訓練資料集上執行訓練演算法。結果是一組模型**權重**。這些是學習後的模型參數,也是大多數人在討論取得開源預訓練模型時所指的內容。這些權重可以用於**推論**,即對新輸入進行預測,例如生成文字。

預訓練的 LLM 也可以在預訓練後針對特定任務進行專門化或調整,特別是當權重被公開發布時。它們會被用作使用案例和應用程式的起點,透過一個稱為**微調**的過程。微調涉及在不同的——通常更專門化且更小的——資料集上對模型進行額外的訓練步驟,以針對特定應用進行最佳化。儘管這個步驟在所需的運算能力方面有成本,但通常比從頭開始訓練模型便宜得多,無論是在財務上還是環境上。這是高品質開源預訓練模型非常有趣的原因之一,因為即使實踐者只能使用有限的運算預算,社群也可以自由使用和建構它們。

## 🗝️ 2022,從規模競賽到資料競賽
在 2023 年之前,社群可以使用哪些開源模型?

直到 2022 年初,機器學習的趨勢是模型越大(即參數越多),效能越好。特別是,似乎當模型超過特定規模閾值時,其能力會有所躍升,這兩個概念被稱為「湧現能力」和「縮放定律」。2022 年發布的預訓練開源模型系列大多遵循這一範式。
1. [BLOOM](https://huggingface.co/papers/2211.05100) (BigScience Large Open-science Open-access Multilingual Language Model)
BLOOM 是 BigScience 發布的[模型](https://huggingface.co/bigscience/bloom)系列,這是一項協作努力,包括來自 60 個國家和 250 個機構的 1000 名研究人員,由 Hugging Face 與法國組織 GENCI 和 IDRIS 合作協調。這些模型使用僅解碼器的 Transformer,並進行了一些小修改(後嵌入層正規化,[^1] 以及使用 ALiBi 位置嵌入 [^2])。該系列中最大的模型是一個 176B 參數的模型,在 46 種人類語言和 13 種程式語言的 350B 標記多語言資料上訓練。大部分訓練資料已發布,其來源、整理和處理的細節也已公開。它是迄今為止最大的開源大規模多語言模型。

2. [OPT](https://huggingface.co/papers/2205.01068) (Open Pre-trained Transformer)
OPT [模型](https://huggingface.co/facebook/opt-66b)系列由 Meta 發布。這些模型使用僅解碼器的 Transformer 架構,遵循 GPT-3 論文的技巧(特定的權重初始化、預正規化),並對注意力機制進行了一些變更(交替使用密集和局部帶狀注意力層)。該系列中最大的模型是一個 175B 參數的模型,在主要來自公開來源(書籍、透過 Reddit 的社群資料、新聞、維基百科和其他各種網路來源)的 180B 標記資料上訓練。該模型系列的效能與 GPT-3 模型相當,使用程式碼最佳化來降低運算密集度。

3. [GLM-130B](https://huggingface.co/papers/2210.02414) (General Language Model)
[GLM-130B](https://huggingface.co/THUDM/glm-roberta-large) 由清華大學和智譜.AI 發布。它使用完整的 Transformer 架構並進行了一些變更(使用 DeepNorm 的層後正規化、旋轉嵌入)。這個 130B 參數的模型在 400B 標記的英語和中文網路資料(The Pile、悟道語料庫和其他中文語料庫)上訓練。它的效能也與 GPT-3 模型相當。

4. 更小或更專門的開源 LLM
也發布了較小的開源模型,主要用於研究目的:Meta 發布了 [Galactica](https://huggingface.co/papers/2211.09085) 系列,最多 [120B](https://huggingface.co/facebook/galactica-120b) 參數的 LLM,在 106B 標記的科學文獻上預訓練,EleutherAI 發布了 [GPT-NeoX-20B](https://huggingface.co/EleutherAI/gpt-neox-20b) 模型,一個完全開源(包括架構、權重、資料)的解碼器 Transformer 模型,在 500B 標記上訓練(使用 RoPE 以及對注意力和初始化的一些變更),為科學研究提供完整的實驗品。

這些巨大的模型令人興奮,但執行起來也非常昂貴!在進行推論(從模型計算預測)時,模型需要載入到記憶體中,但一個 100B 參數的模型通常需要 220GB 的記憶體才能載入(我們將在下面解釋這個過程),這非常龐大,大多數組織和實踐者都無法使用!

然而,在 2022 年 3 月,DeepMind 發表了一篇[新論文](https://huggingface.co/papers/2203.15556),研究在給定運算預算下,標記與模型參數的最佳比例是多少。換句話說,如果您只有 X 金額可以花在模型訓練上,模型和資料的各自規模應該是多少?作者發現,總體而言,對於 LLM 上花費的平均運算預算,模型應該更小,但要在更多的資料上訓練。他們自己的模型 Chinchilla(非開源)是一個 70B 參數的模型(是上述模型規模的三分之一),但在 1.4T 標記的資料上訓練(資料量是 3 到 4 倍)。它的效能與更大的同類模型相似或更好,無論是開源還是閉源。

這種範式轉變雖然可能在閉源實驗室中已經為人所知,但震驚了開放科學社群。

## 🌊 2023,開源發布之年
### 小型大型語言模型的崛起
2023 年見證了一波解碼器風格 Transformer 的興起,每個月都有新的預訓練模型發布,很快變成每週甚至每天:2 月的 LLaMA(Meta)、4 月的 StableLM(StabilityAI)和 Pythia(Eleuther AI)、5 月的 MPT(MosaicML)、6 月的 X-GEN(Salesforce)和 Falcon(TIIUAE)、7 月的 Llama 2(Meta)、8 月的 StableLM v2(StabilityAI)、9 月的 Qwen(阿里巴巴)和 Mistral(Mistral.AI)、11 月的 Yi(01-ai)、12 月的 DeciLM(Deci)、Phi-2 和 SOLAR(Upstage)。

所有這些發布 a) 包含模型權重(在不同程度開放的授權下)和 b) 對於較小規模的模型(3B 到 70B 參數之間)具有良好的效能,因此它們立即被社群採用。幾乎所有這些模型都使用解碼器 Transformer 架構,具有各種調整(ALiBi 或 RoPE、RMS 預正規化、SwiGLU),以及對注意力函數的一些變更(Flash-Attention、GQA、滑動視窗)和不同的程式碼庫實作以最佳化訓練或推論速度。這些調整可能在一定程度上影響效能和訓練速度;然而,由於所有架構都已與權重一起公開發布,剩下的核心差異是訓練資料和模型的授權。

這個系列中的第一個模型系列是 Meta AI 發布的 [LLaMA](https://huggingface.co/papers/2302.13971) 系列。研究人員的明確目標是在給定的運算預算下,訓練一組不同規模、效能盡可能最好的模型。這是首次,研究團隊明確決定不僅考慮訓練預算,還考慮推論成本(對於給定的效能目標,使用模型執行推論的成本是多少)。從這個角度來看,他們決定在比通常更多的資料上訓練更小的模型並進行更多步驟,從而在更小的模型規模下達到更高的效能(代價是訓練運算效率)。Llama 1 系列中最大的模型是一個在 1.4T 標記上訓練的 65B 參數模型,而較小的模型(分別為 6B 和 13B 參數)在 1T 標記上訓練。13B 的小型 LLaMA 模型在大多數基準測試上優於 GPT-3,而最大的 LLaMA 模型在發布時是最先進的。不過,權重是以非商業授權發布的,限制了社群的採用。

[Pythia](https://huggingface.co/papers/2304.01373) 模型由開源非營利實驗室 Eleuther AI 發布,是一[套 LLM](https://huggingface.co/collections/EleutherAI/pythia-scaling-suite-64fb5dfa8c21ebb3db7ad2e1),具有不同的規模,在完全公開的資料上訓練,提供給研究人員以幫助了解 LLM 訓練的不同階段。

幾個月後發布的 [MPT 模型](https://www.mosaicml.com/blog/mpt-7b)由 MosaicML 發布,效能接近但具有允許商業使用的授權,以及訓練混合的細節。第一個 MPT 模型是一個 [7B 模型](https://huggingface.co/mosaicml/mpt-7b),隨後在 6 月推出了 30B 版本,兩者都在 1T 標記的英語和程式碼上訓練(使用來自 C4、CommonCrawl、The Stack、S2ORC 的資料)。

MPT 模型很快被 TIIUAE 發布的 [Falcon 系列](https://huggingface.co/collections/tiiuae/falcon-64fb432660017eeec9837b5a)的 7B 和 30B [模型](https://huggingface.co/tiiuae/falcon-7b)跟進,在 1 到 1.5T 標記的英語和程式碼上訓練(RefinedWeb、Project Gutemberg、Reddit、StackOverflow、Github、arXiv、維基百科等來源) - 年底還發布了一個巨大的 180B 模型。Falcon 模型、資料和訓練過程在技術報告和[後續研究論文](https://huggingface.co/papers/2311.16867)中有詳細說明。

StabilityAI 繼承自 GPT-Neo-X 模型,發布了 [StableLM-Base-Alpha](https://huggingface.co/stabilityai/stablelm-base-alpha-7b) 模型,這是一個小型(3B 和 7B)預訓練系列,使用基於 ThePile 建構的實驗性資料集的 1.5T 標記,隨後是一個 [v2 系列](https://huggingface.co/stabilityai/stablelm-base-alpha-7b-v2),資料混合包括 RefinedWeb、RedPajama、ThePile 和未公開的內部資料集,最後是一個非常小的 3B 模型 [StableLM-3B-4e1T](https://huggingface.co/stabilityai/stablelm-3b-4e1t),配有[詳細的技術報告](https://stability.wandb.io/stability-llm/stable-lm/reports/StableLM-3B-4E1T--VmlldzoyMjU4?accessToken=u3zujipenkx5g7rtcj9qojjgxpconyjktjkli2po09nffrffdhhchq045vp0wyfo)。

之前的模型大多公開了它們的資料,但從那時起,後續發布幾乎沒有提供關於用於訓練模型的資訊,它們的工作無法重現 - 然而,它們透過發布的權重為社群提供了起點。

夏初推出了 Salesforce 的 [X-Gen](https://huggingface.co/papers/2309.03450) [模型](https://huggingface.co/Salesforce/xgen-7b-4k-base),7B 參數的模型在 1.5T 標記的「自然語言和程式碼」上訓練,分多個步驟,遵循資料排程系統(不是所有資料都同時引入模型)。

X-Gen 有點被更引人注目的 Meta 新 [LLaMA-2](https://huggingface.co/papers/2307.09288) 系列所掩蓋,這是一系列 [7 到 70B 的模型](https://huggingface.co/meta-llama/Llama-2-7b),在來自「公開可用來源」的 2T 標記上訓練,具有寬鬆的社群授權和廣泛的從人類偏好微調(RLHF)過程,即所謂的對齊程序。

幾個月後,新創公司 Mistral 的第一個[模型](https://huggingface.co/mistralai/Mistral-7B-v0.1),即所謂的 [Mistral-7B](https://huggingface.co/papers/2310.06825) 發布,在從「開放網路提取」的資料的未公開數量標記上訓練。2023 年底模型發布繁忙,Mistral 推出了第二個更大的模型(Mixtral 8x7B),Deci.AI 推出了第一個令人印象深刻的[模型](https://huggingface.co/Deci/DeciLM-7B),稱為 [DeciLM](https://deci.ai/blog/introducing-DeciLM-7B-the-fastest-and-most-accurate-7b-large-language-model-to-date),以及來自 upstage 的更大的模型合併 [SOLAR](https://huggingface.co/upstage/SOLAR-10.7B-v1.0),也在未公開數量和來源的資料上訓練。所有這些模型在排行榜和開放基準測試上都帶來了穩定的提升。

與此同時,2023 年底的一個顯著事件是在中國訓練並公開發布的模型的效能提升和數量增加。發布了兩個雙語英中模型系列:[Qwen](https://huggingface.co/papers/2309.16609),來自阿里巴巴,7 到 70B 參數的[模型](https://huggingface.co/Qwen/Qwen-72B),在 2.4T 標記上訓練,以及 [Yi](https://huggingface.co/01-ai/Yi-34B),來自 01-AI,6 到 34B 參數的模型,在 3T 標記上訓練。這些模型的效能在開放排行榜(如 [Open LLM leaderboard](https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard))和一些最困難的基準測試(如 [Skill-Mix](https://huggingface.co/papers/2310.17567))上都領先於之前的模型。2023 年底的另一個強勁競爭者是 [DeepSeek AI](https://huggingface.co/deepseek-ai) 的 DeepSeek 程式碼模型,從頭開始在 2T 標記上訓練,組成為 87% 程式碼和 13% 英語和中文的自然語言(主要是程式碼模型)。


### 對話模型無處不在
與 2022 年相比,2023 年發布的幾乎所有預訓練模型都同時提供了預訓練版本和對話微調版本,使用幾種現有方法之一。雖然將模型適應聊天設定的方法在 2022 年及之前就已開發,但這些技術的廣泛採用真正在 2023 年起飛,強調了普通大眾對這些聊天模型的使用日益增長,以及透過與它們聊天來對模型進行人工評估的增長(「氛圍檢查」評估)。我們在這裡詳細介紹將預訓練模型適應聊天的最著名方法,但還存在許多變體!

**基於聊天的微調**是監督式微調的一種變體,其中標註的資料是聊天資料(多輪對話式資料,很像您在社群媒體上找到的內容),您在其上微調模型。您使用與訓練模型時相同的技術:對於解碼器 Transformer,您教模型逐個預測下一個詞(稱為自迴歸方法)。

**指令微調**(IFT)遵循相同的方法,但使用指令資料集,其中包含類似查詢的提示加上答案的集合(如果需要,還有可選的額外輸入)。這些資料集教模型如何遵循指令,可以是人類生成的或 LLM 生成的。
使用大規模模型輸出合成資料集(由模型生成組成的資料集,例如來自 GPT-4 的生成,可能來自指令或來自使用者與該模型的互動)是完成指令和聊天微調的方法之一。這通常被稱為「蒸餾」,因為它涉及從高效能模型中獲取知識來訓練或微調較小的模型。

這兩種方法都相對容易實作:您只需要找到或生成相關資料集,然後使用與訓練時相同的技術微調模型。去年發布了大量的指令資料集,這些資料集提高了模型在類對話設定中的效能。有關此主題的更多資訊,您可以在[這裡](https://huggingface.co/blog/dialog-agents)閱讀入門部落格。然而,模型雖然更好,但仍然無法滿足人類的期望。

**從人類回饋中強化學習**(RLHF)是一種特定的方法,旨在將模型預測的內容與人類最喜歡的內容對齊(取決於特定標準)。它在(年初時)是一種新的微調技術。從給定的提示中,模型生成幾個可能的答案;人類對這些答案進行排名;排名用於訓練所謂的偏好模型(它學會給出反映人類對答案偏好的分數);然後偏好模型用於使用強化學習微調語言模型。有關更詳細的資訊,請參見這篇[部落格文章](https://huggingface.co/blog/rlhf)、[原始 RLHF 論文](https://huggingface.co/papers/1909.08593)或 Anthropic 關於 [RLHF](https://huggingface.co/papers/2204.05862) 的論文。這是一種昂貴的方法(標註/排名 + 訓練新模型 + 微調相當昂貴),主要用於將模型與安全目標對齊。這種方法的一個成本較低的變體已經開發出來,使用高品質的 LLM 而不是人類來對模型輸出進行排名:**從 AI 回饋中強化學習**(RLAIF)。

**直接偏好最佳化**(DPO)是 RLHF 的另一種變體,但不需要訓練和使用單獨的偏好模型 - 該方法需要相同的人類或 AI 排名資料集,但透過查看其原始策略(預測方式)與最佳策略(將預測排名最高的答案)之間的差異來直接使用這些資料更新模型。換句話說,對齊的模型也是偏好模型,這使得最佳化程序簡單得多,同時似乎能提供同等的最終效能。

因此,回到我們來自(主要)私人公司的小型開放權重模型浪潮,其中很多都與微調的對應版本一起發布:MPT-7B 也提供了指令和聊天版本,Falcon 和 XGen 模型的指令調整版本在年底發布,Llama-2、Qwen 和 Yi 與聊天版本一起發布,DeciLM 與指令版本一起發布。Llama-2 的發布特別值得注意,因為它在預訓練和微調模型中都非常注重安全性。

### 社群呢?
雖然聊天模型和指令微調模型通常直接隨新模型發布一起提供,但社群和研究人員並不認為這是理所當然的:一個廣泛而健康的模型微調者社群在這些基礎模型提供的肥沃土壤上蓬勃發展,討論在 Reddit、Discord、Hugging Face Hub 和 Twitter 上自發進行。社群模型的發布很頻繁,與新的有趣資料集的建立並行(也用於微調模型以確定其良好的效能和品質)。

在 2023 年初,已經發布了一些用於指令/聊天微調的資料集。例如,對於人類偏好,OpenAI 的 [WebGPT](https://huggingface.co/datasets/openai/webgpt_comparisons) 資料集、Anthropic 的 [HH-RLHF 資料集](https://github.com/anthropics/hh-rlhf)和 OpenAI 的 [Summarize](https://huggingface.co/datasets/openai/summarize_from_feedback) 在這方面是先驅。指令資料集的範例包括 BigScience 的 [Public Pool of Prompts](https://huggingface.co/datasets/bigscience/P3)、Google 的 [FLAN](https://github.com/google-research/FLAN) 1 和 2、AllenAI 的 [Natural Instructions](https://github.com/allenai/natural-instructions)、來自不同機構研究人員的 [Self Instruct](https://github.com/yizhongw/self-instruct)(一個生成自動指令的框架)、[SuperNatural instructions](https://aclanthology.org/2022.emnlp-main.340/)(一個專家建立的指令基準測試,有時用作微調資料)、特拉維夫大學和 Meta 的 [Unnatural instructions](https://aclanthology.org/2023.acl-long.806.pdf)(一個自動生成的指令資料集)等等。

❄️ 2022/2023 年冬季:今年 1 月,來自中國各機構的研究人員發布了 [Human ChatGPT Instruction corpus](https://huggingface.co/datasets/Hello-SimpleAI/HC3)(HC3),其中包含人類與模型對各種問題的回答。3 月充滿了發布:史丹佛大學開放了 [Alpaca](https://github.com/tatsu-lab/stanford_alpaca) 模型,這是第一個遵循指令的 LLaMA 模型(7B),以及相關的資料集,52K 使用 LLM 生成的指令。LAION(一個非營利開源實驗室)發布了 [Open Instruction Generalist](https://laion.ai/blog/oig-dataset/)(OIG)資料集,4300 萬條指令,既透過資料增強建立,也從其他現有資料來源編譯。同月,LMSYS org(加州大學柏克萊分校)發布了 [Vicuna](https://lmsys.org/blog/2023-03-30-vicuna/),也是一個 LLaMA 微調(13B),這次使用聊天資料:使用者與 ChatGPT 之間的對話,由使用者自己在 [ShareGPT](https://share-gpt.com/) 上公開分享。[Guanaco](https://huggingface.co/datasets/JosephusCheung/GuanacoDataset) 資料集,Alpaca 資料集的擴展(包含額外的 50 萬條更多語言的條目),也被發布,以及相關的 LLaMA-7B 微調。

🌱 春季:4 月,BAIR(柏克萊 AI 研究實驗室)發布了 [Koala](https://bair.berkeley.edu/blog/2023/04/03/koala/),一個聊天調整的 LLaMA 模型,使用前面的幾個資料集(Alpaca、HH-RLHF、WebGPT、ShareGPT),DataBricks 發布了 [Dolly](https://huggingface.co/datasets/databricks/databricks-dolly-15k) 資料集,1.5 萬條人工生成指令的偉大人力工作,以及相關模型,一個 Pythia 微調。5 月,清華大學發布了 [UltraChat](https://arxiv.org/abs/2305.14233),一個包含 150 萬條對話的指令資料集,以及 UltraLLaMA,在該資料集上的微調。Microsoft 隨後發布了 [GPT4-LLM](https://github.com/Instruction-Tuning-with-GPT-4/GPT-4-LLM) 資料集/框架,用 GPT4 生成指令,6 月,Microsoft 研究分享了一種新方法 [Orca](https://arxiv.org/pdf/2306.02707.pdf),透過使用更大模型的推理追蹤(解釋其逐步推理)來建構指令資料集 - 它很快被社群(特別是 Alignmentlab.ai)複製,他們建立了 [Open Orca](https://huggingface.co/Open-Orca) 資料集,數百萬條條目,然後用於微調許多模型(Llama、Mistral 等)。5 月和 6 月,[Camel-AI](https://huggingface.co/camel-ai) 發布了多個關於不同主題的指令或聊天資料集(每個領域超過 2 萬個範例,物理、生物、化學等),使用 GPT4 獲得。同樣在 6 月,[Airoboros](https://github.com/jondurbin/airoboros) 框架發布,用於使用模型生成的資料微調模型(遵循自我指令方法),以及多個[指令資料集](https://huggingface.co/jondurbin)。

🌻夏季:8 月,中國非營利組織 OpenBMB 發布了 [UltraLM](https://github.com/thunlp/UltraChat)(LLaMA 的高效能聊天微調),9 月,他們發布了相關的偏好資料集 [UltraFeedback](https://huggingface.co/datasets/openbmb/UltraFeedback),一個由 GPT4 比較的輸入回饋資料集(帶註釋)。整個夏天,[NousResearch](https://huggingface.co/NousResearch)(一個集體)發布了幾個微調(特別是 Hermes 和 Capybara 集合),基於幾個私有和公開的指令資料集。9 月,清華大學的一個學生團隊發布了 [OpenChat](https://huggingface.co/openchat/openchat_3.5),一個使用新的 RL 微調策略的 LLaMA 微調,Intel 發布了一個 [Orca 風格的 DPO 資料集](https://huggingface.co/datasets/Intel/orca_dpo_pairs)。

🍂 秋季:10 月,Hugging Face 發布了 [Zephyr](https://huggingface.co/HuggingFaceH4/zephyr-7b-beta),一個使用 DPO 和 AIF 在 UltraChat 和 UltraFeedback 上的 Mistral 微調,社群成員發布了 [OpenHermes 2](https://huggingface.co/teknium/OpenHermes-2-Mistral-7B),一個在 90 萬條條目上微調的 Mistral-7B,這些條目來自網路或使用 Axolotl 生成。Lmsys 發布了 LMSYS-Chat-1M,與 25 個 LLM 的真實使用者對話。11 月,OpenBuddy 發布了 OpenBuddy-Zephyr,一個在多輪對話資料上微調的 Zephyr,Argilla 發布了 [Notus](https://huggingface.co/argilla/notus-7b-v1),一個 Zephyr 的 DPO 微調。NVIDIA 發布了 [HelpSteer](https://huggingface.co/datasets/nvidia/HelpSteer),一個對齊微調資料集,提供提示、相關模型回應以及對這些答案在多個標準上的評分,而 Microsoft Research 發布了 [Orca-2](https://huggingface.co/microsoft/Orca-2-13b) 模型,一個在新的合成推理資料集上微調的 Llama 2,Intel 發布了 [Neural Chat](https://huggingface.co/Intel/neural-chat-7b-v3-1),一個在 Orca 上並使用 DPO 的 Mistral 微調。12 月,柏克萊發布了 [Starling](https://huggingface.co/berkeley-nest/Starling-LM-7B-alpha),一個 Open-Chat 的 RLAIF 微調,以及相關資料集 [Nectar](https://huggingface.co/datasets/berkeley-nest/Nectar),20 萬條比較資料條目。

正如我們所見,今年的整體發展既依賴於透過使用高品質預訓練 LLM 建立新資料集,也依賴於社群發布的所有開源模型,使該領域突飛猛進!如果您現在在模型名稱中看到這些名稱之一,您就能大致了解它的來源了 🤗

註:*還發布了一些更專門的資料集(例如 [MetaMath](https://meta-math.github.io/) 或 [MathInstruct](https://huggingface.co/datasets/TIGER-Lab/MathInstruct) 數學問題微調資料集、[Evol-Instruct](https://huggingface.co/datasets/WizardLM/WizardLM_evol_instruct_70k) 數學和程式碼指令、[CodeAlpaca](https://huggingface.co/datasets/sahil2801/CodeAlpaca-20k) 和 [CodeCapybara](https://github.com/FSoft-AI4Code/CodeCapybara) 程式碼指令),但我們不會在這裡詳細介紹它們,儘管它們也被用於提高模型在特定任務上的效能。您也可以查看 [awesome instructions dataset](https://github.com/jianzhnie/awesome-instruction-datasets) 以獲得其他相關資料集的彙編。*

## 普及化存取
註:還出現了許多工具來支援初學者使用者的推論和部署,例如 llama.cpp、ollama、text-generation-inference、vllm 等。它們不在本文件的討論範圍內。

### 合併:極致客製化
以典型的開源方式,社群的標誌之一是模型/資料合併。隨著每次合併/提交,追蹤使用的資料(因為許多發布的資料集是其他資料集的彙編)和模型的歷史可能變得更加困難,因為高效能模型是類似模型的微調版本的微調版本(請參見 Mistral 的「子模型樹」[這裡](https://huggingface.co/spaces/davanstrien/mistral-graph))。在這個總結中,我們還沒有時間談論這項驚人的技術,所以讓我們花幾句話來說明它。

但是合併模型是什麼意思呢?

**模型合併**是一種將不同模型的權重融合到單個模型中的方法,(理想情況下)在統一的單個模型中結合每個模型各自的優勢。存在幾種技術來做到這一點,這些技術已經被擴展並通常主要在社群論壇上發布,這是一個在世界各地的實踐者、研究人員和愛好者社群之間發生的完全去中心化研究的顯著案例。最簡單的已發布方法之一是對共享共同架構的一組模型的參數進行平均([範例 1](https://huggingface.co/papers/2204.03044)、[範例 2](https://huggingface.co/papers/2109.01903)),但存在更複雜的參數組合,例如確定每個模型中哪些參數對給定任務最有影響([加權平均](https://huggingface.co/papers/2111.09832)),或在合併時選擇保留哪些參數之前考慮模型之間的參數干擾([ties 合併](https://huggingface.co/papers/2306.01708))。有關文獻的良好概述,您可以查看這個[很棒的論文集合](https://huggingface.co/collections/osanseviero/model-merging-65097893623330a3a51ead66)!

這些技術允許任何人輕鬆生成模型組合,並且由於現在大多數模型都是相同架構的變體,因此變得特別容易。這就是為什麼提交到 [open LLM leaderboard](https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard) 的一些模型具有諸如 `llama2-zephyr-orca-ultra` 之類的名稱。這個特定的範例可能是 `llama2` 和 `zephyr` 模型的合併,在 orca 和 ultra 資料集上微調。通常,在 Hugging Face hub 上的各自模型卡中可以找到更多細節。

### PEFT:個人化觸手可及
有時,您可能想要更可控的個人化,但沒有足夠的記憶體來載入整個模型到記憶體中進行微調。您知道在微調時不需要使用整個模型嗎?

您可能想使用所謂的**參數高效微調**(PEFT)。
這種技術首先凍結您感興趣的預訓練模型的參數,然後在其上添加一些新參數,稱為適配器。然後您在您的任務上微調的只是(輕量級的)適配器權重,比原始模型小得多。然後您只需要分享您的小適配器權重(和基礎模型)!您可以在[這裡](https://github.com/huggingface/peft)找到 PEFT 的有趣方法列表。

### 量化:模型隨處執行
我們已經看到,現在效能良好的模型有各種形狀和大小...但即便如此,這並不意味著它們對所有人都可存取!一個 30B 參數的模型僅載入到記憶體中(甚至還沒使用)就可能需要超過 66G 的 RAM,並非社群中的每個人都有必要的硬體來做到這一點。

這就是量化的作用!量化是一種特殊的技術,透過改變模型參數的精度來減小模型的大小。

這是什麼意思?

在電腦中,數字以給定的精度儲存(例如 `float32`、`float16`、`int8` 等)。精度表示數字類型(它是浮點數還是整數)以及數字儲存在多少記憶體上:`float32` 將浮點數儲存在 32 位元上。有關更深入的解釋,請參見[此連結](https://huggingface.co/docs/optimum/concept_guides/quantization#going-further-how-do-machines-represent-numbers)。因此,精度越高,一個數字佔用的實體記憶體就越多,因為它將儲存在更多位元上。

因此,如果您降低精度,您就會減少每個模型參數在儲存中佔用的記憶體,從而減小模型大小!這也意味著您減少了...運算的實際精度,這可能會降低模型的效能。然而,我們發現在更大的模型上,這種效能下降實際上非常[有限](https://huggingface.co/blog/overview-quantization-transformers)。

回到我們上面的例子,我們的 30B 參數模型在 `float16` 中需要略少於 66G 的 RAM,在 `8bit` 中它只需要一半,即 33G 的 RAM,而在 `4bit` 中我們甚至達到這個的一半,即大約 16G 的 RAM,使其更易於存取。

從一種精度到另一種精度有許多方法,存在許多不同的「轉換」方案,每種都有其自己的優點和缺點。流行的方法包括 [bitsandbytes](https://huggingface.co/papers/2208.07339)、[GPTQ](https://huggingface.co/papers/2210.17323) 和 [AWQ](https://huggingface.co/papers/2306.00978)。一些使用者,例如 [TheBloke](https://huggingface.co/TheBloke),甚至正在轉換流行的模型以使它們對社群可存取。所有這些都非常新且仍在開發中,我們希望隨著時間的推移在這方面看到更多進展。

## 接下來是什麼?
這一年還沒有結束!而這最後的~~幾個月~~幾天~~幾小時已經帶來了一些驚喜:新的架構最終會超越簡單高效的 Transformer 嗎?

新發布包括
- 專家混合:
	- [Mixtral](https://huggingface.co/mistralai/Mixtral-8x7B-v0.1),該模型由 8 個子模型(Transformer 解碼器)組成,對於每個輸入,路由器選擇 2 個最佳子模型並對它們的輸出求和。
- 幾個狀態空間模型(透過潛在空間將輸入映射到輸出的模型,根據任務可以表達為 RNN 或 CNN,[此資源](https://srush.github.io/annotated-s4/)很好地解釋了狀態模型,如果您想要更多資訊):
	- [Mamba](https://huggingface.co/papers/2312.00752),具有添加的選擇機制的狀態空間模型
	- [Striped Hyena](https://huggingface.co/togethercomputer/StripedHyena-Nous-7B),具有快速卷積核心的狀態空間模型

現在說這些新方法是否會取代 Transformer 還為時過早,但狀態空間模型相當有前景!

## 要點總結
- 今年見證了來自各種角色(大公司、新創公司、研究實驗室)的開源發布的興起,這使社群能夠以前所未有的速度開始實驗和探索。
- 模型發布的開放性有起有落,從今年早期的非常開放(資料集混合、權重、架構)到後期發布幾乎不提供任何關於訓練資料的資訊,因此無法重現。
- 開源模型從許多新地方湧現,包括中國,有幾個新角色將自己定位為 LLM 競賽中的強勁競爭者。
- 個人化可能性達到了歷史新高,採用了新的微調策略(RLHF、適配器、合併),這些只是開始。
- 更小的模型規模和量化的升級使 LLM 真正為更多人所用!
- 新架構也出現了 - 它們最終會取代 Transformer 嗎?

就這樣了,各位!
我希望您喜歡這一年的回顧,學到了一兩件事,並且和我一樣對現在有多少 AI 進展依賴於開源和社群努力感到熱情! 🤗


[^1]: 後嵌入層正規化是一種使學習更穩定的技巧。
[^2]: ALiBi 位置嵌入在序列中距離太遠的標記被模型連接在一起時引入懲罰(而普通的位置嵌入只會儲存序列中標記的順序和相對位置的資訊)。
