# Credit-Risk-Probability-Model

## Credit Scoring Business Understannding

### 1. Basel II's Emphasis on Risk Measurement and Model Requirements

#### 1.1. The Link Between Basel II and Model Interpretability/Documentation

The Basel II Accord (specifically under the **Internal Ratings-Based (IRB) approach**) requires banks to use their own models to estimate key risk parameters like Probability of Default (**PD**), Loss Given Default (**LGD**), and Exposure at Default (**EAD**). These models directly determine the bank's minimum regulatory capital requirement.

This direct link to capital dictates two strict requirements:

* **Requirement for Interpretability:** Regulators (**Pillar 2: Supervisory Review**) must validate and approve the bank's internal capital assessment process. A model whose logic cannot be easily explained (a "black box") is almost impossible to approve. The bank's management also needs to understand the risk drivers to make informed lending and provisioning decisions. The coefficients and variable effects must have clear economic reasoning.
* **Requirement for Well-Documentation:** Extensive documentation is mandatory (**Pillar 2 and Pillar 3: Market Discipline**). This covers the model development process, data sources, performance, ongoing monitoring, and governance. This documentation ensures transparency for supervisors and demonstrates that the bank is following sound risk management practices.

---

### 2. The Necessity and Risks of Using a Proxy Variable for "Default"

#### 2.1. Why a Proxy Variable is Necessary

In credit risk modeling, the true event of regulatory default (e.g., legal charge-off or bankruptcy) may be too sparse, take too long to materialize, or be inconsistently defined across different portfolios.

A **proxy variable**—or a **Performance/Default Event Definition**—is created to define the target variable (e.g., $Y=1$ for a "bad" account) based on an earlier, more common, and observable signal of financial distress within a specific window. A common definition might be: "An account is defined as 'bad' if it has been **90 days Past Due (DPD)** or more at any point in the next 12 months."

This proxy is essential because it allows for:

* **Sufficient Sample Size:** Generating enough 'bad' observations to train a statistically robust model.
* **Timely Prediction:** Providing an earlier warning signal for risk management interventions and required provisioning *before* the costly true default occurs.

#### 2.2. Potential Business Risks of Predictions Based on a Proxy

The main risk stems from the potential mismatch between the predicted **proxy event** and the actual **true regulatory default**.

1.  **Proxy Mismatch (Over-Provisioning):** The model accurately predicts the proxy event (e.g., 90 DPD), but the borrower subsequently cures and **never** reaches true regulatory default. This leads to classifying accounts as risky that ultimately perform, resulting in unnecessarily high loan loss provisions and capital charges, which harms profitability.
2.  **Model Calibration Risk (Regulatory Non-Compliance):** The statistical model is trained to predict $\text{PD}_{\text{proxy}}$. Basel II, however, requires the final PD to be calibrated to the long-run average of the **true regulatory default rate ($\text{PD}_{\text{Basel}}$)**. If the bank fails to properly adjust or scale the model's output to the true definition, the Risk-Weighted Assets (RWA) calculation will be incorrect, risking regulatory sanctions.

---

### 3. Trade-offs: Simple vs. Complex Models in a Regulated Context

In a Basel II environment, the choice between a simple, interpretable model (like Logistic Regression with Weight of Evidence, or WoE) and a complex, high-performance model (like Gradient Boosting) is a crucial trade-off between **Regulatory Compliance** and **Predictive Accuracy**.

#### Simple/Interpretable Models (Logistic Regression/WoE)

**Trade-Off Advantages:**

* **High Interpretability:** The linear relationship and monotonic effects from WoE transformation make the model easy to validate, audit, and explain to regulators and internal stakeholders. This directly addresses the core **Pillar 2** requirements.
* **Stability & Auditability:** The model is stable, and changes in input variables result in predictable changes in output. This makes model monitoring straightforward and reduces the risk of regulatory rejection.
* **Ease of Deployment:** Widely understood and easier to implement in traditional banking IT systems.

**Trade-Off Disadvantages:**

* **Lower Predictive Performance:** These models are less capable of capturing complex non-linear relationships and interactions, potentially leading to less accurate PD estimates and, consequently, inefficient capital allocation (higher RWA than necessary).
* **Requires Extensive Feature Engineering:** Requires manual binning and WoE calculation, which consumes time and may lead to information loss.

#### Complex/High-Performance Models (Gradient Boosting)

**Trade-Off Advantages:**

* **Superior Predictive Performance:** Captures complex data structures, leading to more precise PD estimation. Higher accuracy can translate into more efficient capital use, potentially leading to a lower (but more accurate) RWA.
* **Less Feature Engineering:** Can automatically discover and exploit high-order feature interactions.

**Trade-Off Disadvantages:**

* **"Black Box" Nature (High Regulatory Risk):** It is extremely difficult to explain *why* a specific complex prediction was made. This opacity is a significant barrier to **Pillar 2** approval, as regulators may reject the model due to a lack of causal clarity.
* **Difficult to Audit and Monitor:** Complex models are more prone to overfitting and can be unstable. Monitoring for parameter drift and ensuring the economic logic remains sound is much harder, increasing operational and model risk.

**The Contextual Decision:**

In practice, simple, interpretable models are often mandated for **Regulatory Capital (Pillar 1)** because they minimize the risk of regulatory rejection. Complex models are increasingly used for **internal "Economic Capital" models** or for **operational decisions** (like optimizing collections strategy), where high accuracy is valued over regulatory-grade transparency. Using a complex model for regulatory capital requires exhaustive, sophisticated, and often *post-hoc* explainability techniques (like SHAP or LIME) to satisfy the high burden of proof for interpretability.
