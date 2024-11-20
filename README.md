# Air Quality Prediction for Stockholm, Jultomtestigen, Alvsjö stadsdelsomräde, Sweden

## Project Description
This project involves building and deploying a machine learning model to predict air quality (PM2.5) based on historical weather data and air quality measurements. The model is updated daily using newly available data and predictions are monitored for accuracy.

The daily predictions can be followed on [this dashboard](docs/air-quality/index.md).

---

### Data Used
The model uses **historical weather data** (more than a year of data) and **historical air quality data** from **AQICN**. The chosen location is Stockholm, Jultomtestigen, Alvsjö stadsdelsomräde, Sweden https://aqicn.org/station/@78529/. The weather data includes features such as **temperature**, **precipitation**, **wind speed**, and **wind direction**, while the air quality data provides **PM2.5** measurements.

---

### 1. **Backfill Feature Pipeline**
A backfill feature pipeline was created that downloads historical weather data (more than a year of data) and loads a CSV file of historical air quality data from [AQICN](https://aqicn.org). This data is then registered as **Feature Groups** in **Hopsworks** for use in the pipeline.

### 2. **Daily Feature Pipeline**
A daily feature pipeline is scheduled using **GitHub Actions**. This pipeline downloads **yesterday’s weather and air quality data** and weather predictions for the next 7-10 days, updating the relevant **Feature Groups** in **Hopsworks**.

### 3. **Training Pipeline**
A training pipeline was written to select features and create a **Feature View** in **Hopsworks**. The pipeline reads the training data, trains a regression model (using XGBoost), and registers the model with **Hopsworks**.

### 4. **Batch Inference Pipeline**
The batch inference pipeline downloads the trained model from **Hopsworks** and performs batch inference. The program plots a dashboard showing **predicted air quality** for the next 7-10 days based on the selected sensor data.

### 5. **Monitoring Accuracy (Hindcast Graph)**
The accuracy of the model’s predictions is monitored using a [**hindcast graph**](https://github.com/Eugenius0/air-quality-prediction-service/blob/main/docs/air-quality/assets/img/pm25_hindcast_1day.png), which compares the **predicted PM2.5 values** to the **actual observed values** (outcomes) of air quality measured over time.

### 6. **Model Update with Lagged Feature**
The model was updated by adding a **lagged air quality feature** (`pm25_lag_rolling_3`), which calculates the **mean PM2.5 values** from the previous 1-3 days. This provides the model with additional historical context for better prediction accuracy.

We used here a rolling mean which is a statistical method that calculates the average of the previous N data points (3 days in this case). This method captures trends over time, as air quality is often influenced by prior values. We used Pandas' rolling function to efficiently compute the rolling mean, which allows us to easily apply this calculation to time-series data and ensure that each day's PM2.5 value incorporates the context from the previous days.

---

### Results

#### Without Lag
MSE       = 7.18
R Squared = -0.32

#### With Lag
MSE       = 4.65
R Squared = 0.15

![Pasted Graphic 10](https://github.com/user-attachments/assets/7e1e6799-d94d-44ff-8a99-7c5dfacd10ec)

### Future Work

- **More robust data cleaning process**: Remove negative PM2.5 values and handle any other data inconsistencies.
- **Including additional data sources**: Integrate other relevant data (e.g., satellite data, traffic data) to improve model accuracy.
- **More extensive cross-validation and hyperparameter tuning**: Implement techniques like random search or grid search to optimize model performance.
- **Real-time predictions**: Explore real-time prediction capabilities by integrating the model with live data feeds for continuous updates.
- **Enhancing the dashboard**: Improve visualizations and add more metrics (e.g., AQI) for better user experience.



### Tools and Technologies Used
- **Hopsworks** for managing **Feature Groups** and **Feature Views**.
- **XGBoost** for training the regression model.
- **GitHub Actions** for scheduling and running pipelines.
- **Matplotlib** for plotting and saving the forecast and hindcast graphs.
