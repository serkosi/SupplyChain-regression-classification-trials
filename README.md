# Regression and Classification Trials for a Supply Chain Management Case

In this project, we apply the Random Forest algorithm to both regression and classification problems. Our focus is on a manufacturing company with distribution warehouses in different cities. This company sells its products to dealers, meaning the customers are the dealers who purchase products from the company.

## 1. Task Descriptions

### Regression Task
- **Target Variable:** **Purchase Quantity Trend (Quantity_Trend)**
- **Objective:**  
  To predict the total sales quantity made to dealers based on the sales data from distribution warehouses in different cities. This allows determining which city's warehouse has higher sales, enabling strategy formulation accordingly.
  
  Specifically, the goal is to predict the trend component obtained from the seasonal decomposition of the daily Quantity (number of products purchased) data. This way, the general trend in sales quantity can be analyzed to make future predictions.

### Classification Task
- **Target Variable:** **Dealer Churn (Churn)** (1: Dealer stopped purchasing, 0: Dealer continues purchasing)
- **Objective:**  
  To predict whether dealers will make future purchases based on their past purchasing data. This allows taking preventive measures for dealers at high risk of churn.

## 2. Dataset and Variables

The dataset used is adapted from online retail sales data from 2010-2011. However, in our scenario:
- **City:**  
  The "Country" variable in the dataset now represents the cities where the manufacturer distributes its products. Each row indicates that the sale was made from a distribution warehouse in a specific city.
- Other main columns:
  - **InvoiceNo:** Invoice number (unique)
  - **StockCode:** Product code
  - **Description:** Product name
  - **Quantity:** Number of products purchased
  - **InvoiceDate:** Transaction date
  - **UnitPrice:** Unit price of the product
  - **CustomerID:** Unique ID of the dealer

### Data Preprocessing
- Records with missing dealer (CustomerID) information were removed.
- Negative or zero values in Quantity and UnitPrice were excluded.

## 3. Feature Engineering

### Regression Model
- **Daily Totals:**  
  The dataset was reorganized to calculate daily totals (resample('D')) by selecting only numerical columns.

- **Seasonal Decomposition:**  
  - The Quantity and UnitPrice columns were subjected to seasonal decomposition with 30-day periods.
  - As a result of the decomposition, the trend component obtained for Quantity was determined as the target variable for the model.

- **City (Distribution Warehouse):**  
  The City variable represents the manufacturer's distribution warehouses in different cities. It is included in the model as a categorical variable after being numerically encoded.  
  *Why?* This allows the model to learn sales trends in different cities and enables region-based performance analysis.

### Classification Model
- **Encoding Categorical Variables:**  
  The Country column was converted to numerical form using LabelEncoder.

- **Customer-Level Aggregation:**
  The following metrics were calculated for each customer (CustomerID):
  - TotalSpend: Total spending (sum of TotalPrice)
  - OrderCount: Number of unique invoices (number of purchases)
  - AvgOrderValue: Average invoice value
  - Country: Country of the customer (encoded)
  - LastPurchase: Date of the last purchase

- **Defining Churn:**  
  - Using the latest date in the dataset as a reference, the difference between each customer's last purchase date and the reference date was calculated.
  - Customers who had not made a purchase for more than 180 days were labeled as churn (1), otherwise as not churn (0).

## 4. Model Training and Evaluation

The variables and target variable were split into 80% training and 20% testing for both models.
### Regression Model
- **Model:** Random Forest Regressor
- **Target:** Quantity Trend (Trend component obtained from seasonal decomposition of daily total Quantity data)
- **Variables Used:**  
  - Raw values: UnitPrice  
  - Time-based variables: Month, WeekOfYear
- **Hyperparameters:**  
  - n_estimators = 100  
  - random_state = 42  
- **Evaluation:**  
  Model performance is measured using RMSE (Root Mean Squared Error). It is also compared with a simple average prediction (baseline).

### Classification Model
- **Best Model Selection**  
  - The Random Forest classification model with the best performance was selected after hyperparameter optimization.
- **Performance Metrics**  
  - Accuracy
  - Classification Report: Metrics such as Precision, Recall, and F1-score were used to evaluate the classification success of the model.

## 5. Application of the Model in Supply Chain Management

- **Sales Reports and Inventory Management:**  
  Our model can help suppliers with inventory planning by predicting sales quantities from distribution warehouses in different cities. For example, if sales are expected to increase in a specific city, more inventory can be held in that region.

- **Dealer Management and Marketing:**  
  Dealer churn predictions allow evaluating the likelihood of dealers making purchases. Thus, special campaigns can be organized for high-risk dealers to strengthen customer relationships.

- **Dynamic Pricing:**  
  Pricing strategies can be developed based on regional sales trends. For example, prices can be optimized during periods of high demand to increase profits.

## 6. Evaluation

- **For Regression:**  
  The model's performance is measured using RMSE. The improvement of the model is analyzed by comparing it with a simple average prediction.
- **For Classification:**  
  Metrics such as Accuracy and F1-score are used to evaluate the model's performance.

The main difference is that regression aims to predict continuous variables, while classification aims to categorize data into specific classes.

---

This project aims to predict sales (TotalPrice) from the manufacturer's distribution warehouses in different cities and classify dealer purchasing behaviors using the Random Forest algorithm. This enables more informed decisions in areas such as supply chain management, marketing strategies, and inventory planning.
