# Supply Chain Demand Forecasting

A data-driven approach to inventory management and next-day demand forecasting using exploratory data analysis, statistical analysis, and machine learning.

## Project Overview

This project analyzes supply chain data to understand demand patterns across products, warehouses, suppliers, regions, inventory levels, pricing, and promotional activities.

The main objective is to develop a machine learning model that predicts **next-day units sold** using historical demand patterns and available operational and commercial information.

The project follows a complete data science workflow:

**Data Understanding → Data Cleaning → Feature Engineering → EDA → Statistical Analysis → Machine Learning → Model Evaluation → Interpretation → Recommendations**

---

## Objectives

- Analyze product demand and supply chain patterns.
- Identify factors associated with changes in units sold.
- Examine the effect of promotional activities on demand.
- Engineer historical and temporal features for next-day forecasting.
- Compare multiple machine learning regression models.
- Evaluate model performance using a chronological train-test split.
- Identify the best-performing model among the evaluated approaches.
- Derive practical recommendations for inventory and demand planning.

---

## Dataset

The dataset contains supply chain records at the SKU, warehouse, supplier, and daily level.

### Dataset Dimensions

- **Records:** 91,250
- **Variables:** 15
- **Time period:** 2024
- **Unit of analysis:** SKU-Warehouse-Day

### Main Variables

| Variable | Description |
|---|---|
| `Date` | Date of the observation |
| `SKU_ID` | Product identifier |
| `Warehouse_ID` | Warehouse identifier |
| `Supplier_ID` | Supplier identifier |
| `Region` | Geographic region |
| `Units_Sold` | Actual units sold |
| `Inventory_Level` | Available inventory |
| `Supplier_Lead_Time_Days` | Supplier lead time |
| `Reorder_Point` | Inventory reorder threshold |
| `Order_Quantity` | Quantity ordered |
| `Unit_Cost` | Product cost |
| `Unit_Price` | Product selling price |
| `Promotion_Flag` | Indicates whether a promotion was active |
| `Stockout_Flag` | Indicates stockout status |
| `Demand_Forecast` | Existing demand forecast variable |

The raw dataset is available in the [`data`](./data/) directory.

---

## Target Variable

The forecasting target is:

**`Next_Day_Units_Sold`**

This represents the actual units sold on the following day for the same SKU-Warehouse combination.

The prediction horizon is therefore **one day ahead (t+1)**.

---

## Feature Engineering

Historical demand features were created using information available before the prediction day:

- `lag_1`
- `lag_7`
- `lag_14`
- `rolling_mean_7`
- `rolling_mean_14`

Temporal features include:

- Day of week
- Month
- Quarter
- Week of year
- Day of year

Operational and commercial variables were also included.

To reduce the risk of data leakage:

- Historical rolling features were shifted before calculation.
- The existing `Demand_Forecast` variable was excluded because of its extremely strong relationship with actual demand.
- `Stockout_Flag` was excluded because it contained no variation.
- A chronological rather than random train-test split was used.

---

## Exploratory Data Analysis

The analysis examined:

- Distribution of units sold
- Demand trends over time
- Demand by SKU
- Demand by warehouse
- Demand by region
- Promotion and demand
- Price and demand
- Inventory and demand
- Correlation between numerical variables

### Key EDA Findings

- Demand varies over time and across observations.
- Promotional periods are associated with higher average sales.
- SKU, warehouse, and regional average demand showed relatively small differences.
- Most operational and pricing variables had weak linear relationships with units sold.
- Historical and temporal information provided useful signals for forecasting.

---

## Statistical Analysis

The following statistical methods were applied:

### Promotion vs Demand

An independent samples t-test was used to compare demand during promotional and non-promotional periods.

- **t-statistic:** 44.6215
- **p-value:** < 0.001

This indicates a statistically significant difference in average demand between promotional and non-promotional observations.

### Correlation Significance

Pearson correlation tests were used to assess the relationship between units sold and numerical variables.

Significant relationships were observed for:

- Inventory level
- Order quantity
- Promotion flag

Other tested variables did not show statistically significant linear relationships with units sold.

### Regional Analysis

One-way ANOVA was used to compare average demand across regions.

- **F-statistic:** 0.4842
- **p-value:** 0.6933

The result does not provide evidence of a statistically significant difference in average demand across regions.

---

## Machine Learning Models

Four approaches were evaluated:

1. **Persistence Baseline** using previous-day demand (`lag_1`)
2. **Linear Regression**
3. **Decision Tree Regressor**
4. **Random Forest Regressor**

A chronological **80:20 train-test split** was used to evaluate performance on future observations.

---

## Model Performance

| Model | Test MAE | Test RMSE | Test R² |
|---|---:|---:|---:|
| Persistence Baseline | 5.8938 | 7.4303 | -0.5179 |
| **Linear Regression** | **5.4290** | **6.8054** | **-0.2733** |
| Decision Tree | 8.2031 | 10.1523 | -1.8337 |
| Random Forest | 5.9319 | 7.4302 | -0.5178 |

### Best Model

**Linear Regression** achieved the best test performance among the evaluated models:

- **MAE:** 5.4290
- **RMSE:** 6.8054
- **R²:** -0.2733

However, the negative test R² indicates that the current forecasting model has limited predictive performance and should be considered a baseline rather than a production-ready forecasting system.

The Decision Tree and Random Forest models showed substantially stronger training performance than testing performance, indicating overfitting.

---

## Model Interpretation

The Linear Regression coefficients highlighted historical demand and temporal variables as important contributors to the model.

The Random Forest model identified `day_of_year` as its most influential feature, followed by historical rolling demand and other temporal and operational features.

These results should be interpreted as model associations rather than causal relationships.

---

## Key Findings

- Promotional activity is associated with higher average demand.
- Regional differences in average demand were not statistically significant.
- Several operational and pricing variables showed weak direct linear relationships with demand.
- Historical demand and temporal features were useful components of the forecasting approach.
- Linear Regression performed best on the chronological test set among the evaluated models.
- Tree-based models showed clear signs of overfitting.
- The current feature set does not fully explain the variation in next-day demand.

---

## Business Recommendations

- Use recent historical demand patterns to support daily inventory planning.
- Account for promotional periods when planning inventory and replenishment.
- Monitor inventory levels alongside demand trends.
- Support replenishment decisions with data rather than relying only on fixed assumptions.
- Prioritize performance on unseen future data when selecting forecasting models.
- Collect additional demand-driving information before deploying the forecasting model operationally.

---

## Limitations

- The available dataset covers approximately one year, limiting the ability to learn recurring annual seasonality.
- The best-performing model still has limited predictive performance.
- Decision Tree and Random Forest models showed overfitting.
- Some variables have limited variation, including the constant `Stockout_Flag`.
- `Demand_Forecast` was excluded because of its extremely strong relationship with actual demand and potential leakage concerns.
- Results are specific to the characteristics of the provided dataset.
- Statistical associations should not be interpreted as causal relationships.

---

## Future Enhancements

Future work could include:

- Using multiple years of historical data.
- Adding holidays, weather, events, and marketing information.
- Evaluating advanced ensemble and time-series forecasting methods.
- Performing systematic hyperparameter tuning.
- Using rolling or expanding-window time-series validation.
- Integrating demand forecasting with safety stock and reorder optimization.
- Developing an interactive supply chain decision-support dashboard.
- Applying explainable AI techniques to individual demand predictions.

---

## Technologies Used

- **Python**
- **Pandas**
- **NumPy**
- **Matplotlib**
- **Seaborn**
- **SciPy**
- **Scikit-learn**
- **Google Colab**
- **Jupyter Notebook**

---

## Project Structure

```text
Supply-Chain-Demand-Forecasting/
│
├── data/
│   └── P_3_supply_chain_dataset1.csv
│
├── Supply_Chain_Demand_Forecasting.ipynb
│
└── README.md
