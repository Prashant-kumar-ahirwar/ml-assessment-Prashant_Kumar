# Business Analysis – Promotion Effectiveness at a Fashion Retail Chain

---

## B1. Problem Formulation

### (a) ML Problem Definition

**Target Variable (Y):**  
`items_sold` (number of items sold per store per month)

**Input Features (X):**  
- Store attributes: `store_size`, `location_type` (urban/semi-urban/rural), `competition_density`  
- Promotion details: `promotion_type` (Flat Discount, BOGO, etc.)  
- Time features: `month`, `season`, `is_weekend`, `is_festival`  
- Customer behavior: `monthly_footfall`  
- Historical features: previous month sales (lag features)

**Type of ML Problem :** Supervised Regression Problem  

**Justification:**  
The target variable (`items_sold`) is continuous numeric data. The goal is to predict quantity sold based on inputs, making this a regression task.

---

### (b) Why Use Items Sold Instead of Revenue

Revenue can be misleading because:
- It depends on price variations (discounts reduce revenue but may increase volume)
- High-priced items can skew revenue
- Promotions directly affect pricing

**Items sold is better because:**
- Directly measures customer demand
- Reflects promotion effectiveness
- Not distorted by pricing strategies

**Broader Principle:**  
Always choose a target variable that directly aligns with the business objective.

---

### (c) Alternative Modelling Strategy

Instead of a single global model:

**Option 1: Segmented Models**
- Separate models for:
  - Urban stores
  - Semi-urban stores
  - Rural stores

**Option 2: Hierarchical / Mixed Models**
- Include `store_id` as a feature
- Use models like Gradient Boosting

**Justification:**
Different store types behave differently. Segmenting improves prediction accuracy and personalization.

---

## B2. Data and EDA Strategy

### (a) Data Joining and Dataset Design

**Tables:**
1. Transactions  
2. Store attributes  
3. Promotion details  
4. Calendar  

**Joining Strategy:**
- Transactions + Promotions → join on `promotion_id`
- Add Store attributes → join on `store_id`
- Add Calendar → join on `transaction_date`

**Final Dataset Grain:**  
One row = one store per month  

**Aggregations:**
- `items_sold = SUM(items)`  
- `total_revenue = SUM(revenue)`  
- `avg_footfall = AVG(footfall)`  
- `promotion_type = assigned promotion`  
- `is_festival`, `is_weekend` → aggregated flags  

---

### (b) EDA Strategy

**1. Sales vs Promotion Type**
- Chart: Bar plot  
- Insight: Which promotion performs best  
- Action: Feature encoding and importance  

**2. Sales Trend Over Time**
- Chart: Line plot  
- Insight: Seasonality and trends  
- Action: Add time-based features  

**3. Location Type vs Sales**
- Chart: Box plot  
- Insight: Regional performance differences  
- Action: Add interaction features  

**4. Competition Density vs Sales**
- Chart: Scatter plot  
- Insight: Effect of competition  
- Action: Consider nonlinear relationships  

**5. Promotion vs No Promotion**
- Chart: Grouped bar / histogram  
- Insight: Promotion effectiveness  
- Action: Handle imbalance  

---

### (c) Handling Imbalance (80% No Promotion)

**Problem:**
- Model may become biased toward "no promotion"
- Poor learning of promotion effects  

**Solutions:**
- Oversample promotion cases  
- Apply class weighting  
- Use balanced datasets  
- Create feature: `is_promotion`  

---

## B3. Model Evaluation and Deployment

### (a) Train-Test Split Strategy

**Approach: Time-Based Split**
- Train: First 2–2.5 years  
- Test: Last 6–12 months  

**Why NOT Random Split:**
- Causes data leakage  
- Unrealistic predictions  
- Future data influences past  

---

### Evaluation Metrics I use

**MAE (Mean Absolute Error):**
- Average prediction error in units sold  

**RMSE (Root Mean Squared Error):**
- Penalizes large errors  

**R² Score:**
- Measures explained variance  

**Business Interpretation:**
- Lower MAE → better accuracy  
- Lower RMSE → fewer large mistakes  

---

### (b) Explaining Different Recommendations

Use Feature Importance or SHAP values.

**Steps:**
1. Compare feature contributions (December vs March)  
2. Identify key drivers like seasonality, demand, trends  

**Example:**
- December → Festivals → Loyalty Points Bonus works better  
- March → Lower demand → Flat Discount works better  

**Communication:**
- Use charts (feature importance, SHAP plots)  
- Explain in simple business terms  

---

### (c) Deployment Strategy

**1. Model Saving**
- Use `pickle` or `joblib`  

**2. Monthly Data Pipeline**
- Collect new data  
- Apply same preprocessing steps  

**3. Prediction Process**
- Input: Monthly store data  
- Output: Promotion recommendation  

**4. Automation**
- Schedule using cron jobs or pipelines  

**5. Monitoring**
- Track MAE over time  
- Compare predicted vs actual sales  
- Measure promotion effectiveness  

**6. Retraining Triggers**
- Performance drop  
- Market changes  
- Scheduled retraining (every 3–6 months)  
