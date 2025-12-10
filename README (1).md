# Predict Future Sales Model Card

### Basic Information
* **Group member names and emails**: Andrew Cheon , `andrewcheon@gwu.edu`, Henry Lin,  `henry.lin@gwu.edu`, Zhengrong Hu, `jshuzhengrong41@gwu.edu`, Alp Sarioz, `alp.sarioz@gwu.edu`
* **Model date**: November, 2025
* **Model version**: 1.0
* **License**: MIT
* **Model implementation code**: [DNSC_3288_Project.ipynb](https://github.com/Acheon-stst/Acheon-stst/blob/main/DNSC3288.ipynb)

### Intended Use
* **Primary intended uses**: The model is intended to forecast the total sales for every unique product and store combination for the next month (specifically, November 2015) based on historical daily sales data
* **Primary intended users**: Students in DNSC 3288 completing the semester project.
* **Out-of-scope use cases**: Any use beyond an educational example is out-of-scope.

### Training Data
* Data dictionary:

 
| Name | Modeling Role | Measurement Level | Description |
| ------------- | ------------- | ------------ | ------------- |
| Date  | Input | object | Day of purchase (DD.MM.YYYY) |
| Date_block_num  | input | int | Month index starting at 0; represents time for forecasting |
| Shop_id | input | int | Unique identifier of a shop |
| Item_id  | input | int | Unique identifier of a product |
| Item_price | input | float | Current price of an item |
| Item_category_id | input | int | Category ID for each item |
| Shop_city | input | string | Extracted city from shop name |
| Shop_type | input | string | mall vs standalone, derived from shop name |
| Item_cnt_day | input | float | Number of products sold on that day |
| Item_cnt_month | target | float | Total number of sales (item_cnt_day aggregated monthly and capped at [0,20]) for a specific shop and item in the next month |
| ID | ID | int | Unique (Shop, Item) tuple identifier within the test set |

* Source of training data: Provided by Kaggle Competition [Predict Future Sales](https://www.kaggle.com/competitions/competitive-data-science-predict-future-sales/overview)
* How training data was divided into training and validation data
  - Training: All months from Jan 2013 to Sept 2015 (Months 0–32)
  - Validation: Oct 2015 (month 33)
* Number of rows in training and validation data (after monthly aggregation):
  - Rows in training set: 1,577,593
  - Rows in validation set: 31,531
     - Only one month (October 2015) is held out to simulate real-world forecasting, so the validation set is intentionally smaller than the training set.
 
### Test Data
* Source of test data: Provided by Kaggle Competition [Predict Future Sales](https://www.kaggle.com/competitions/competitive-data-science-predict-future-sales/overview)
* Number of rows in test data: 214,200 
* Differences in columns between training and test data: 
  - Test data does not include date, date_block_num, item_price
  - Test data includes an ID column for submission mapping
  
### Model Details
* Columns used as inputs in the final model: 
  - All engineered features including lagged sales, shop/item/category averages, long-term means, trend features, and calendar month.
* Target column: item_cnt_month
* Type of model: Gradient Boosted Decision Tree Regression (XGBoost)
* Software used to implement the model: Python, XGBoost, Pandas, NumPy, Scikit-learn
* Version of modeling software: scikit-learn 1.3+, XGBoost 1.7+, python 3.9+
* Hyperparameters: NOT SURE
  - XGBRegressor(
    max_depth=8,
    learning_rate=0.05,
    n_estimators=500,
    subsample=0.8,
    colsample_bytree=0.8,
    reg_alpha=1,
    reg_lambda=3,
    random_state=42,
    tree_method='hist')

### Quantitative analysis NOT SURE IF THESE IS RIGHT
* Models were assessed primarily with RMSE
 

| Training RMSE | Validation RMSE | Test RMSE |
| ------------- | ------------- | ------------ |
| 0.94 | 1.12 | 1.1 |

### Ethical Considerations
* Potential negative impacts
  - Math or software problems
    - Forecasting errors could inflate or underestimate demand, leading to unrealistic expectations
    - Poor feature engineering or incorrect lags could create misleading projections
    - Model bias across shops could systematically over- or under-predict certain locations
  - Real-world risks
    - If used by retailers, inaccurate forecasts could lead to overstocking, financial loss, or supply chain waste
    - Smaller shops could be disproportionately harmed if model error is not evenly distributed
* Potential uncertainties
  - Math or software uncertainties
    - Model may not generalize outside the training time range (2013–2015)
    - Rapid price changes, stockouts, or seasonality shifts not seen in training data may introduce error
  - Real-world uncertainties
    - True consumer demand fluctuates due to promotions, holidays, competition, and macroeconomic factors that are not included in the dataset
    - The dataset is synthetic and may not reflect all real-world complexities
* Unexpected results
  - Adding more lag features beyond 3 months did not improve performance
  - Certain shops exhibited almost zero sales for many items, causing sparse patterns that the model struggled to learn
  - XGBoost performed significantly better than baseline linear regression or naive mean forecasting


