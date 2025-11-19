# 獲取評判用的 LLM

在使用現有的 LLM 時，您可以選擇[通用型高能力模型](https://arxiv.org/abs/2306.05685v4)、使用專門針對偏好資料進行辨別訓練的[小型專業模型](https://arxiv.org/abs/2405.01535)，或是訓練您自己的模型。

## 使用通用型 LLM

隨著更強大的 LLM（例如 ChatGPT）問世，一些研究人員開始探索使用大型模型作為評判者。目前最佳的大型模型評判者往往是閉源模型（如 Claude 或 gpt-o 系列模型），不過由於像 [Qwen 2.5](https://huggingface.co/collections/Qwen/qwen25-66e81a666513e518adb90d9e)、[Command R+](https://huggingface.co/CohereForAI/c4ai-command-r-plus-08-2024) 或 [Llama 3.1-405-Instruct](meta-llama/Llama-3.1-405B-Instruct) 等高品質模型的出現，與開源模型的差距正在迅速縮小。

閉源模型儘管效能出色，但也存在多項缺點：
- 透過 API 提供服務，這意味著模型（因此評估結果）可能在沒有通知的情況下變更，損害評估的可重現性
- 黑盒模型，使其無法解釋
- 可能造成資料外洩或缺乏資料隱私，因為您需要透過網路將資料傳送給第三方（這往往比本地管理的資料更不安全），而且您無法確定資料會被如何使用（通常需要主動選擇退出才能避免資料被用於訓練集）。

然而，閉源模型也讓任何人都能存取高品質模型，無需在本地設置或取得硬體資源。這些優點現在也適用於大多數高品質的開源模型，這些模型可透過模型服務提供商存取，並且解決了上述前兩個問題。

如果您需要協助選擇模型服務提供商，可以在[這裡](https://huggingface.co/spaces/ArtificialAnalysis/LLM-Performance-Leaderboard)找到不錯的成本分析。

## 使用小型專業 LLM 評判模型

您也可以選擇使用小型專業 LLM 評判模型。這些模型通常只有數十億個參數，可以在大多數近期的消費級硬體上本地執行，並且可以從頭訓練或使用指令資料進行微調。您通常需要遵循它們特定的提示詞格式。

一些現有的模型：
- Flow-Judge-v0.1（[權重](https://huggingface.co/collections/flowaicom/flow-judge-v01-66e6af5fc3b3a128bde07dec)），38 億參數，在合成偏好資料集上微調的 Phi-3.5-mini-instruct 模型
- Prometheus（[權重](https://huggingface.co/prometheus-eval/prometheus-13b-v1.0)、[論文](https://arxiv.org/abs/2310.08491)），130 億參數，在合成偏好資料集上從頭訓練的模型。也有 70 億參數的 [v2 版本](https://huggingface.co/prometheus-eval/prometheus-7b-v2.0)，這是在更大的合成偏好資料集上微調的 Mistral-7B-Instruct-v0.2 模型，並加入了權重合併
- JudgeLM（[論文](https://arxiv.org/abs/2310.17631)），70 億至 330 億參數，在由多種模型生成的合成偏好資料集上從頭訓練的模型。

## 訓練您自己的模型

您也可以選擇訓練或微調您自己的 LLM 評判模型。

您首先需要為感興趣的任務收集偏好資料，資料來源可以是：
- 現有的[人類偏好資料集](https://www.kaggle.com/competitions/lmsys-chatbot-arena)
- 模型生成的偏好資料（您可以參考上述小型模型評判者論文的資料部分來生成，或直接取得，例如從 Prometheus 的[偏好](https://huggingface.co/datasets/prometheus-eval/Preference-Collection)和[回饋](https://huggingface.co/datasets/prometheus-eval/Feedback-Collection)資料集）。

接著您需要決定是從小型模型開始從頭訓練，還是從現有模型開始，您可以：
- 將其蒸餾成新的較小模型
- 進行量化
- 然後使用上述資料進行微調（如果模型很大且訓練運算資源有限，可以使用 PEFT 或適配器權重）
	- 顯然[從獎勵模型開始訓練比從指令模型開始效果更好](https://x.com/dk21/status/1826292289930674590)
