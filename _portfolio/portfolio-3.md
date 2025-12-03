---
title: "Push Notification Optimization: ML for e-commerce"
excerpt: "Push Notification Optimization: ML for e-commerce <br/><img src='/images/portfolio/smart_push_notification/ml_for_e_commerce.png' style='transform: scale(0.5); transform-origin: top left;'>"
collection: portfolio
---
In e-commerce, there is a fine line between being "helpful" and being "annoying." Push notifications are a powerful tool to drive sales, but their indiscriminate use often leads to high uninstall rates (churn).

In this project, developed during the **Zrive Applied Data Science** program, I developed an **end-to-end purchase prediction system** for a Grocery e-commerce platform. The goal was to shift from mass marketing campaigns to a **precision targeting strategy**, sending notifications only to users with a high probability of converting into profitable orders.

## 1. The Business Challenge: Profitability vs. Intrusiveness

The sales team needed a tool to promote specific items (e.g., clearing out discontinuing stock or boosting market share). However, the project had clear constraints defined in the *Project Requirements Document* (TRD):

* **The Cost of Intrusiveness:** Irrelevant notifications generate user dissatisfaction and churn, which is a significant cost for the company. Currently, the app's push notification open rate is only around **5%**.
* **Profitability Margins:** The system must not incentivize purchases of single items due to shipping costs. The model focuses exclusively on predicting purchases that are part of a basket of **at least 5 items**.
* **KPIs:** The target impact was to increase monthly sales by 2% and achieve a 25% boost on the selected items.

## 2. Bussiness Translation
One of the most critical steps in Data Science is translating abstract business goals into a concrete mathematical problem. We cannot simply tell an algorithm to "increase sales without being annoying."

To bridge the gap between the sales team's requirements and the code, I formulated the specific Machine Learning task as follows:

> **Goal:** Develop a Machine Learning model that, given a **user** and a **product**, predicts if the user would purchase it if they were buying with us at that point in time.

This translation turns the business challenge into a Binary Classification problem. By predicting this specific probability, we can score every user against the target products and only act when that probability exceeds our risk threshold.

### Evaluation Metrics: Visualizing the Tradeoff
Because there is a clear tradeoff between the volume of notifications sent (potential sales) and the "annoyance factor" (churn risk), simple accuracy is not enough. We need to visualize the balance between True Positives (successful sales) and False Positives (annoyed users).

To navigate this, I focused on two key curves:
* **ROC Curve:** To evaluate the model's general ability to distinguish between buyers and non-buyers.
* **Precision-Recall (PR) Curve:** Given the class imbalance, this was critical. It helps us understand exactly how much precision we lose as we try to capture more buyers (Recall).

## 3. EDA

I worked with a real transactional dataset including order history, inventory, and user behavior.

### Key Challenges identified during Exploratory Data Analysis:
1.  **Extreme Imbalance:** The positive class (user buys the target product) represented only **1.4%** of the data. This ruled out "Accuracy" as a valid metric.
2.  **Leakage Prevention:** To simulate a real production environment, I avoided random splitting. I implemented a **Time-Aware Split**, dividing Train, Validation, and Test sets by strict time windows (e.g., training on Oct-Jan, validating on Feb) to ensure the model could not "see the future."

## 4. Modeling: From Baseline to Gradient Boosting

I adopted an iterative approach, starting with simple solutions to establish a baseline and increasing complexity only when justified by metrics.

### Phase 1: Linear Models
First, I established a non-ML baseline using **global product popularity**. Then, I trained **Logistic Regression** models with L1 (Lasso) and L2 (Ridge) regularization.
* **Result:** Linear models clearly outperformed the non-ML baseline, proving that past user behavior (e.g., ordered before) was predictive.
* **Feature Selection:** I utilized Lasso to identify the most heavily weighted variables and discard noise, effectively reducing the dimensionality of the problem.

### Phase 2: Non-Linear Models
Given that buying behavior is complex, I experimented with **Random Forest** and **Gradient Boosting Trees (GBT)**.
* **Random Forest:** While powerful, it struggled to generalize effectively due to sampling issues inherent in such imbalanced classes.
* **Gradient Boosting (GBT):** This was the winning model. By tuning hyperparameters (learning rate 0.05, depth 3), it achieved the best balance between the ROC and Precision-Recall (PR-AUC) curves, outperforming both Logistic Regression and Random Forest on the validation set.

![Alt text description](/images/portfolio/smart_push_notification/model_performance_comparison.png)

## 5. From Model to Business Decision: Calibration

A classification model returns a "score," but the business needs a **real probability** to make decisions.
GBT model had an acceptable calibration although it could be improved while LR was poorly calibrated.
To address this, I applied **Isotonic Regression** to the final models. This aligned the predicted probabilities with the actual purchase frequencies.

![Alt text description](/images/portfolio/smart_push_notification/calibration.png)

### The Decision Rule
Based on the fact that the current notification open rate is **5%**, I set the decision threshold at **0.05**.
* If the calibrated probability is > 5%, we send the notification.
* This ensures that every notification sent has a higher theoretical probability of success than the current random approach ("beating the prevalence").
![Alt text description](/images/portfolio/smart_push_notification/final_performance.png)

## 6. Conclusion & Impact

This project demonstrates how to transform a broad business need ("sell more without bothering users") into a robust technical solution.

**Key Achievements:**
* Implementation of an ML pipeline capable of predicting purchase intent for complex baskets (5+ items).
* Significant improvement over the popularity baseline.
* Reduction of churn risk by filtering out users with low probability (<5%) of interest.

---
*The source code for this project, including EDA, linear, and non-linear model notebooks, will be available in my GitHub repository soon.*
