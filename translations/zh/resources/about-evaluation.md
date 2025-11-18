## 知識
### 通用
- [基礎模型開發速查表](https://fmcheatsheet.org/)，由 AllenAI 提供

### 自動化評估
兩個關於自動化評估挑戰的精彩概述！
- [大型語言模型評估的挑戰](https://github.com/lm-evaluation-challenges/lm-evaluation-challenges.github.io/blob/main/%5BMain%5D%20ICML%20Tutorial%202024%20-%20Challenges%20in%20LM%20Evaluation.pdf)，由 Hailey Schoelkopf 和 Lintang Sutawika 演講。
- [大型語言模型可重現評估的實戰經驗](https://arxiv.org/abs/2405.14782)，EleutherAI 的論文
- Latent Space 的兩個評估相關 Podcast
    - [Benchmarks 101](https://www.latent.space/p/benchmarks-101)，介紹自動化基準測試的歷史和常見相關問題
    - [Benchmarks 201](https://www.latent.space/p/benchmarks-201)，討論何時該使用哪種評估方法，以及關於排行榜的一些趣聞！


### LLM 作為評審
精彩的摘要和經驗分享：
- https://eugeneyan.com/writing/llm-evaluators/
- https://cameronrwolfe.substack.com/p/llm-as-a-judge
- https://dylandigitalgarden.com/2024/July/July+31%2C+2024+LLM+%26+VLM-as-a-Judge

## 軟體
### 評估套件
- [`lm_eval`](https://github.com/EleutherAI/lm-evaluation-harness/)，由 Eleuther 開發（也稱為「the Harness」）。LLM 評估的強大工具，讓您能以穩定且可重現的方式，在多個基準測試上評估來自眾多供應商的任何 LLM。
- [`lighteval`](https://github.com/huggingface/lighteval)，由 Hugging Face 開發（聲明：我是作者之一）。輕量級的 LLM 評估套件，專注於客製化和最新的基準測試。

### 排行榜
- [Open LLM Leaderboard](https://huggingface.co/spaces/open-llm-leaderboard/open_llm_leaderboard)，由 Hugging Face 提供。
  中立第三方對開放 LLM 在參考靜態基準測試上的評估（開放提交）
- [HELM](https://crfm.stanford.edu/helm/lite/latest/#/leaderboard)，由 Stanford 提供。
  同樣在靜態基準測試上評估模型，但使用勝率來排名模型
- [Chatbot Arena](https://huggingface.co/spaces/lmsys/chatbot-arena-leaderboard)，由 LMSys 提供
  使用群眾外包的人工評估來評分 150 個 LLM 的競技場
- [LLM Performance Leaderboard](https://huggingface.co/spaces/ArtificialAnalysis/LLM-Performance-Leaderboard)，由 Artificial Analysis 提供
  最大型 LLM API 供應商的效能基準測試和定價，如果您想使用 API 而非在本地執行
- [所有關於評估和排行榜的部落格文章](https://huggingface.co/blog?tag=leaderboard)
- [排行榜搜尋器](https://huggingface.co/spaces/leaderboards/LeaderboardFinder)：尋找最適合您使用情境的排行榜

### 教學
- [端對端客製化領域評估教學](https://github.com/argilla-io/argilla-cookbook/tree/main/domain-eval)：本教學將引導您為自己的領域建立客製化評估任務。使用合成資料以及透過 [Argilla](https://github.com/argilla-io/argilla/) 和 [distilabel](https://github.com/argilla-io/distilabel) 進行人工評估。

