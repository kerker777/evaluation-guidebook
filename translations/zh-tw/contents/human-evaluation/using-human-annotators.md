# 使用人工標註者

我建議閱讀這篇[評論](https://aclanthology.org/2024.cl-3.1/)的第 3 節，內容涵蓋資料標註品質的良好實務。如果您希望達到生產等級的品質，且具備實施所有這些方法的條件，那就放手進行吧！

  ![Best_annotation_practices](https://github.com/huggingface/evaluation-guidebook/blob/main/assets/best_annotation_practices.png?raw=true)

然而，一旦您定義了任務和評分準則，以下是重要的指導原則（無論專案規模大小）。

- **工作人員選擇以及（如果可以的話）金錢激勵**
您可能希望從事您任務的人員能夠：
1) 符合某些人口統計特徵。
	例如：目標語言的母語使用者、具有較高教育程度、特定領域的專家、地理來源多元化等。
	 您的需求會因任務而異。
1) 產出高品質的工作成果。
	現在特別重要的是加入檢查答案是否為 LLM 生成的方法，而且您需要從標註者人才庫中篩選掉某些標註者。
  *依我之見，除非您依賴的是高度積極主動的群眾外包標註者，否則正確支付標註者報酬總是比較好的做法。*

- **準則設計**
務必花費大量時間仔細思考您的準則！這是我們在 [GAIA](https://huggingface.co/gaia-benchmark) 資料集上花費最多時間的要點之一。

- **迭代式標註**
請做好進行多輪標註的準備，因為您的標註者會誤解您的準則（它們比您想像的更加模糊）！多次生成樣本將使您的標註者真正收斂到您所需的內容。

  - **品質評估**和**人工審查**
您需要控制答案品質（特別是透過標註者間一致性，如果能取得的話），並進行最終篩選，只保留最高品質/最相關的答案。

專門用於建立高品質標註資料集的工具（如 [Argilla](https://argilla.io/)）也能幫助您。
### 延伸閱讀
- ⭐ [幾分鐘內建立您自己的標註平台](https://huggingface.co/learn/cookbook/enterprise_cookbook_argilla)，作者：Moritz Laurer。這是一篇很好的文章，可以讓您獲得使用開源工具（如 Argilla 和 Hugging Face）的實作經驗，並更了解大規模人工標註的注意事項。
- ⭐ [標註良好實務指南](https://aclanthology.org/2024.cl-3.1/)。這是一份關於 2023 年所有人工標註相關論文的評論，內容非常完整。稍微密集，但非常容易理解。
- [另一份標註良好實務指南](https://scale.com/guides/data-labeling-annotation-guide)，由專精於人工評估的 ScaleAI 提供。這是上述文件的輕量級補充資料。
- [擷取人工標籤的假設與挑戰](https://aclanthology.org/2024.naacl-long.126/)是一篇論文，探討如何檢視標註者意見分歧的來源，並在實務中減輕這些問題。
