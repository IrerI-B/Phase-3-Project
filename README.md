# Phase-3-Project
**Customer Churn Prediction (SyriaTel)**

---

## 1. Business Context
- Problem: customer churn at SyriaTel causes revenue loss.
- Goal: predict churn (binary 0/1) and identify drivers.
- Value: targeted retention actions, reduce churn cost, improve lifetime value.

---

## 2. Data Overview
- Dataset: `SyriaTel.csv`
- Key columns:
  - `churn` (target)
  - usage metrics: `total day minutes`, `total eve minutes`, `total night minutes`, `total intl minutes`
  - charges: `total day charge`, `total eve charge`, `total night charge`, `total intl charge`
  - `customer service calls`, `international plan`, `voice mail plan`, demographics (`state`, `phone number` dropped)

---

## 3. EDA Highlights + Visuals (from notebook)
- Class imbalance: 85.5% non-churn, 14.5% churn.
![Churn Distribution](figures/churn_distribution.png)

- Visual:
  - `sns.countplot(x='churn', palette='viridis')`  
  - `df['total day minutes'].hist()` / day/eve/night hist
  - `sns.boxplot(x=df['churn'], y=df['customer service calls'], palette='viridis')`
  - `sns.boxplot(x=df['churn'], y=df['total day minutes'], palette='viridis')`
- Correlation:
![Correlation Heatmap](figures/correlation_heatmap.png)
  - `sns.heatmap(corr_df.corr(), annot=True, cmap='viridis')`
  - Strong multicollinearity:
    - day minutes <-> day charge
    - eve minutes <-> eve charge
    - night minutes <-> night charge
    - intl minutes <-> intl charge

---

## 4. Preprocessing
- dropped `phone number`, `state`
- one-hot encode categoricals
- `churn` cast to int
- split: 70/30 train/test
- Handle imbalance: SMOTE on training set

---

## 5. Models Built
1. Logistic Regression (balanced class_weight)
2. Logistic Regression (GridSearchCV tuned):
   - `C=10`, `penalty='l2'`, `solver='liblinear'`, F1 ~0.743
3. Decision Tree (max_depth=5)
4. Random Forest (baseline and tuned via GridSearchCV)

---

## 6. Metrics + Visuals
- Confusion matrix visual:
![Confusion Matrix RF](figures/confusion_matrix_rf.png)
  - `ConfusionMatrixDisplay(...).plot(cmap='viridis')`
- ROC Curve:
  - `roc_curve` plot + diagonal baseline
![ROC Curve RF](figures/roc_curve_rf.png`)
- Precision-Recall Curve:
  - ![precision_recall_curve](figures/precision_recall_rf_tuned.png)
- Random Forest (baseline):
  - Accuracy ≈ 92%, F1 ≈ 72%, ROC-AUC ≈ 88%
- Random Forest (tuned):
  - better precision/recall tradeoff, fewer churn miss and false alarm
- Interpretation note:
  - good discrimination but class imbalance sensitivity needs continuous monitoring

---

## 7. Feature importance (Random Forest)
- Important features:
  - `customer service calls`
  - `total day charge` / `total day minutes`
  - `international plan`
  - `total eve charge/minutes` etc  
- Visual: horizontal bar chart of top 10 importances.
![Horizontal bar chart of top 10 importances](figures/feature_importance.png)
---

## 8. Limitations
- No temporal features (behavior over time).
- SMOTE adds synthetic minority rows (small risk of overfitting).
- Market-specific dataset → tune for new regions.

---

## 9. Business Recommendations
- High-contact customers (`customer service calls`) should be prioritized for retention.
- Offer cost-sensitive incentives for high day/eve/night charge users.
- Review international plan value perception; adjust pricing/benefits.
- Deploy model in CRM, threshold for recall-focused alerts.
- Set monitoring + periodic retraining for model drift.

---

## 10. Conclusion
- Best current model: **Tuned Random Forest**
- Use for risk-based retention scoring
- Follow recommended business actions and reevaluate quarterly.

---
