
# New York Taxi Expense Analysis

This project predicts the average money spent on taxi rides for each region of New York per given day and hour.
This is a supervised regression problem because the actual value of the prediction value is known. Regression model is used because the predictive value is a continuous variable (distinct from categorical variable).


## Data problems I fixed

![7bd07519-811c-4643-bfec-138e283e5e29](https://user-images.githubusercontent.com/107722688/182239319-f91baac7-32d3-4297-93b7-f7c482343de4.png)

Negative total_amount values: removed them since they are likely faulty data points and there are not many of them with Zero values for total_amount: The same applies to negative values. Zero values are removed.

![0b9fc0a8-dbf2-4636-8f13-ef29ca0ca3f3](https://user-images.githubusercontent.com/107722688/182240117-13d2683e-de28-4340-a836-98303211c035.png)

Total_amount values were too high reaching upto 600000. As these are unlikely values for a taxi fare, I decided to come up with an upper limit. The average taxi_fare was $16 and there were only 1166 data points > $200. Compared to the 7667792 data points, this is not a great loss of information. Hence, I removed data points with a total_amount value > 200.

## Original features of the model
Here is the list of features that can be used for model development that came with the original data: [‘PULocationID’, ‘transaction_date’,’ transaction_month’,’ transaction_day’, ‘transaction_hour’, ‘trip_distance’,’ total_amount’, ‘count_of_transactions’]
You can refer to https://www1.nyc.gov/assets/tlc/downloads/pdf/data_dictionary_trip_records_yellow.pdf for the meaning of each feature.

### Feature engineering
Three sets of new features were added to the model. First set is time-based feature. These include, weekend and holiday boolean.
Second set is location-based information. Location IDs per region is given but there is a higher level oulandishness for a region called Boroughs. This information came from the source of the main data.
The last set is weather related data. I’ve downloaded this data from same website. 
Here is a list of all features used in the final model: ['PULocationID', 'transaction_month', 'transaction_day', 'transaction_hour', 'transaction_week_day', 'weekend', 'is_holiday', 'Borough’, 'temperature', 'humidity', 'wind speed', 'cloud cover', 'amount of precipitation’, ‘total_amount’]

## Used algorithms and the results
I used Decision Trees, Random Forest and Gradient Boosting. However, the benchmark model is Decision Tree. In the benchmark model, I only included the original features of the model as stated above. And on the normal models I used all original features plus the newly created ones.

Performance results before tuning:
|  **Algorithm** 	    | **MAE** | **RMSE**| **R2** 	|
|        ---	        |  ---	  |  ---	  |  ---	  |
| Benchmark model 	  | 9.776   | 14.737 	| 0.224 	|
| Decision tree 	    | 9.887 	| 15.008 	| 0.206 	|
| Random forest 	    | 7.585 	| 13.563 	| 0.351 	|
| Gradient boosting 	| 9.511 	| 14.176 	| 0.291 	|

The Random Forest model is selected to be tuned. Here are the best parameter values: n_estimators: 600, min_samples_split: 10, min_samples_leaf: 2, max_features: sqrt max_depth: 500 bootstrap: False.

Because of the high n_estimator value, I decided to go with the parameter values that give the 2nd best performance, which is not very different than the best performance: n_estimators: 200 min_samples_split: 10 min_samples_leaf: 1 max_features: sqrt max_depth: 200 bootstrap: False.

The performance compares to previous models is:
| **    Algorithm**   	| ** MAE** 	| ** RMSE** 	| ** R2** 	|
|---------------------	|----------	|-----------	|---------	|
| Benchmark Model     	| 9.776    	| 14.737    	| 0.224   	|
| Decision Tree       	| 9.887    	| 15.008    	| 0.206   	|
| Random Forest       	| 7.585    	| 13.563    	| 0.351   	|
| Gradient Boosting   	| 9.511    	| 14.176    	| 0.291   	|
| Tuned Random Forest 	| 7.300    	| 12.762    	| 0.425   	|

Here is the True vs. Predicted value plot for the tuned random forest model. X-axis is the true values and y-axis the predicted values.

![1bdc144e-f708-4d36-a445-f001427c18e5](https://user-images.githubusercontent.com/107722688/182248587-5bf62b96-501f-489a-9db1-fb31a9ce37fd.png)


Next steps:
As you can see from the plot above, the performance can be improved. Three ways that I did not try in this notebook:
•	limiting the regions included in this analysis. Removing the regions that do not normally get a lot of taxi traffic can be omitted. This might be a good action to take depending on the problem at hand. (If the goal is to service all of NYC no matter what, we should keep those data points)
•	hand selecting borough that should be included in the model. Again this should be decided based on the problem at hand and how this model is going to be used. But if the goal is solely to increase model performance, only including boroughs with the most transactions can increase the performance since likely most mistakes come from boroughs with fewer data points. Though this assumption should be checked before taking this action.

