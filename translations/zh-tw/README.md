# LLM 評測指南 ⚖️

如果您曾經想知道如何確保 LLM 在特定任務上表現良好，這份指南就是為您而寫的！
本指南涵蓋了評測模型的不同方法、設計自訂評測的指引，以及來自實務經驗的技巧與訣竅。

無論您是使用正式產品模型的工作者、研究人員還是業餘愛好者，我希望您能在這裡找到所需的內容；如果沒有，請開一個 issue（建議改進或補充遺漏的資源），我會持續完善這份指南！

## 如何閱讀本指南
- **初學者使用者**：
  如果您對評測一無所知，應該先從每章的 `Basics` 章節開始，再深入探討其他內容。
  您也可以在 `General knowledge` 中找到關於重要 LLM 主題的說明：例如，模型推論的運作方式以及什麼是 tokenization。
- **進階使用者**：
  更實用的章節是 `Tips and Tricks` 以及 `Troubleshooting` 章節。您也會在 `Designing` 章節中發現有趣的內容。
- **回訪使用者**：
  每年我都會深入探討一個主題，記得查看這些內容！

在文章中，標示 ⭐ 的連結是我非常喜歡並推薦閱讀的。

## 目錄
如果您想要對這個主題有個入門了解，可以閱讀這篇關於我們如何以及為何進行評測的[部落格文章](https://huggingface.co/blog/clefourrier/llm-evaluation)！

### 自動化基準測試
- [基礎知識](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/automated-benchmarks/basics.md)
- [設計您的自動化評測](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/automated-benchmarks/designing-your-automatic-evaluation.md)
- [一些評測資料集](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/automated-benchmarks/some-evaluation-datasets.md)
- [技巧與訣竅](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/automated-benchmarks/tips-and-tricks.md)

### 人工評測
- [基礎知識](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/human-evaluation/basics.md)
- [使用人工標註者](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/human-evaluation/using-human-annotators.md)
- [技巧與訣竅](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/human-evaluation/tips-and-tricks.md)

### LLM 作為評審
- [基礎知識](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/model-as-a-judge/basics.md)
- [取得評審 LLM](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/model-as-a-judge/getting-a-judge-llm.md)
- [設計您的評測提示](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/model-as-a-judge/designing-your-evaluation-prompt.md)
- [評測您的評測器](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/model-as-a-judge/evaluating-your-evaluator.md)
- [關於獎勵模型](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/model-as-a-judge/what-about-reward-models.md)
- [技巧與訣竅](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/model-as-a-judge/tips-and-tricks.md)

### 疑難排解
本指南中最具實務性的部分。
- [推論疑難排解](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/troubleshooting/troubleshooting-inference.md)
- [可重現性疑難排解](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/troubleshooting/troubleshooting-reproducibility.md)

### 通用知識
這些主要是 LLM 基礎的入門指南，但仍會包含一些技巧和實用參考資料！
如果您是進階使用者，我建議直接瀏覽 `Going further` 章節。
- [模型推論與評測](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/general-knowledge/model-inference-and-evaluation.md)
- [Tokenization](https://github.com/huggingface/evaluation-guidebook/blob/main/contents/general-knowledge/tokenization.md)

## 年度深入探討
- [2023 年，開源之年](https://github.com/huggingface/evaluation-guidebook/blob/main/yearly_dives/2023-year-of-open-source.md)
- [2024 年，評測應該為了什麼？](https://github.com/huggingface/evaluation-guidebook/blob/main/yearly_dives/2024-evals-thoughts-from-iclr.md)
- [2025 年，建構「真實世界」實用模型的評測](https://github.com/huggingface/evaluation-guidebook/blob/main/yearly_dives/2025-evaluations-for-useful-models.md)

## 資源
我喜歡的連結
- [關於評測](https://github.com/huggingface/evaluation-guidebook/blob/main/resources/about-evaluation.md)
- [關於一般 NLP](https://github.com/huggingface/evaluation-guidebook/blob/main/resources/about-NLP.md)
- [The UltraScale Playbook](https://huggingface.co/spaces/nanotron/ultrascale-playbook)

## 社群翻譯
本指南已由熱心的社群成員翻譯！
- 🇨🇳 https://github.com/huggingface/evaluation-guidebook/tree/main/translations/zh/contents，感謝 @SuSung-boy
- 🇫🇷 https://huggingface.co/spaces/CATIE-AQ/Guide_Evaluation_LLM，感謝 @lbourdois

## 致謝
本指南深受 Stas Bekman 的 [ML Engineering Guidebook](https://github.com/stas00/ml-engineering) 啟發！感謝這個很棒的資源！

同時也非常感謝所有在活動或線上討論中啟發本指南的人們，特別感謝但不限於：
- 🤝 Luca Soldaini、Kyle Lo 和 Ian Magnusson（Allen AI）、Max Bartolo（Cohere）、Kai Wu（Meta）、Swyx 和 Alessio Fanelli（Latent Space Podcast）、Hailey Schoelkopf（EleutherAI）、Martin Signoux（Open AI）、Moritz Hardt（Max Planck Institute）、Ludwig Schmidt（Anthropic）
- 🔥 Open LLM Leaderboard 和 lighteval 的社群使用者，他們經常在討論中提出非常有趣的觀點
- 🤗 Hugging Face 的成員，如 Lewis Tunstall、Hynek Kydlíček、Guilherme Penedo 和 Thom Wolf，當然還有我的團隊夥伴 Nathan Habib，自 2022 年以來我們一直在進行評測和排行榜工作

當然也要感謝所有貢獻者 :)

## 引用
[![CC BY-NC-SA 4.0][cc-by-nc-sa-image]][cc-by-nc-sa]

[cc-by-nc-sa]: http://creativecommons.org/licenses/by-nc-sa/4.0/
[cc-by-nc-sa-image]: https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png
[cc-by-nc-sa-shield]: https://img.shields.io/badge/License-CC-BY--NC--SA-4.0-lightgrey.svg

```
@misc{fourrier2024evaluation,
  author = {Clémentine Fourrier and The Hugging Face Community},
  title = {LLM Evaluation Guidebook},
  year = {2024},
  journal = {GitHub repository},
  url = {https://github.com/huggingface/evaluation-guidebook)
}
```
