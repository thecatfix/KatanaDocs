# Stationarity Model
[stationarity_model](`src/generate/components/models/stationarity_model.py`)

Overview
The stationarity_model.py file implements a stationarity model for analyzing and predicting investment ideas. It contains two main classes: StationarityModelLoader and StationarityModel. This model is designed to evaluate the stationarity of financial time series data and make predictions based on various similarity metrics.

## Classes
### StationarityModelLoader
This class is responsible for loading the StationarityModel.

Methods
```
load
def load(self, model_path: str, model_options: Optional[Dict[str, Any]] = None) -> Model
```
Loads the StationarityModel from the given path.

## Parameters:

``` model_path (str): The path to the model.
model_options (Optional[Dict[str, Any]]): Additional options for the model.```

Returns:

Model: An instance of StationarityModel.
StationarityModel
This class implements the stationarity model for analyzing investment ideas.

### Attributes
```
adf_regression (str): Type of regression for ADF test (default: 'ct').
adf_threshold (float): Threshold for ADF test (default: 0.05).
pearson_threshold (float): Threshold for Pearson correlation (default: 0.9).
similarity_threshold (float): Threshold for similarity score (default: 0.7).
zspread_to_use (str): Type of z-spread to use (default: 'zspread_mid_best').
min_zspread_to_alert (float): Minimum z-spread to trigger an alert (default: 5.0).
min_zscore_to_alert (float): Minimum z-score to trigger an alert (default: 1.5).

### Methods


``` 
init
def __init__(self, id: str, options: Optional[Dict[str, Any]] = None)
Initializes the StationarityModel with the given ID and options.

_compute_cor_similarity
def _compute_cor_similarity(self, cor1: Optional[Country], cor2: Optional[Country]) -> float
Computes the similarity between two countries of risk.

_compute_ig_similarity
def _compute_ig_similarity(self, ig1: Optional[str], ig2: Optional[str]) -> float
Computes the similarity between two industry groups.

_compute_rating_similarity
def _compute_rating_similarity(self, rating1: Optional[Rating], rating2: Optional[Rating]) -> float
Computes the similarity between two ratings.

_compute_maturity_similarity
def _compute_maturity_similarity(self, maturity1: Optional[datetime], maturity2: Optional[datetime]) -> float
Computes the similarity between two maturity dates.

_compute_similarity
def _compute_similarity(self, candidate: IdeaCandidate) -> Optional[float]
Computes the overall similarity score for an idea candidate.

_preprocess
def _preprocess(self, candidate: IdeaCandidate) -> Tuple[str, str, pd.DataFrame]
Preprocesses the data for the given candidate. This method is currently unused.

predict
def predict(self, candidate: IdeaCandidate, opts: Dict[str, Any] = None) -> ModelResult
Makes a prediction for the given idea candidate.

```

### Parameters:

candidate (IdeaCandidate): The idea candidate to evaluate.
opts (Dict[str, Any]): Additional options for prediction (optional).


Returns:

ModelResult: The prediction result, including alert status, similarity score, and other metrics.
Key Concepts
Similarity Metrics: The model uses various similarity metrics (country of risk, industry group, rating, and maturity) to evaluate the relatedness of bonds in an idea candidate.

Stationarity: While the class is named StationarityModel, the current implementation doesn't explicitly test for stationarity using methods like the Augmented Dickey-Fuller (ADF) test. The adf_score in the predict method is set to a constant value.

Alert Generation: The model generates alerts based on similarity scores, bookmarks, curve trades, and z-score/z-spread thresholds.

Usage
The StationarityModel is typically used as part of a larger system for evaluating investment ideas. It's loaded using the StationarityModelLoader and then used to make predictions on IdeaCandidate objects.

Example:
```
loader = StationarityModelLoader()
model = loader.load("path/to/model")
result = model.predict(idea_candidate)
```


### Notes
The current implementation sets constant values for Pearson correlation and ADF scores in the predict method. These might be placeholders for more sophisticated calculations in future versions.
The _preprocess method is defined but unused in the current implementation.
The model relies heavily on pre-computed similarity scores and thresholds. Adjusting these parameters may significantly affect the model's behavior.


## Stationary Model In Experiment Project

[model_utils/stationarity_model.py](https://gitlab.com/katanalabs/katana-experiments/-/blob/master/model_utils/stationarity_model.py?ref_type=heads)

compute_zscore(df) function:

Purpose: Computes the z-score of the present dislocation for a bond pair compared to its historic variances.
Input: A DataFrame (df) with at least two columns.
Process: a. Calculates the difference between the first and second columns of the input DataFrame. b. Computes the last value, mean (excluding the last value), and standard deviation of this difference. c. Calculates the average of the last 3 days' values. d. Computes the absolute difference between the 3-day average and the mean. e. Calculates the z-score by dividing the absolute difference by the standard deviation.
Output: Returns the computed z-score.
Note: Returns 0.0 if the standard deviation or the absolute difference is zero to avoid division by zero.
compute_pearson_coefficients(df) function:

Purpose: Calculates Pearson correlation coefficients between all columns in the input DataFrame.
Input: A DataFrame (df) with multiple columns.
Process: a. Computes the correlation matrix using df.corr(). b. Retains only the lower triangular part of the correlation matrix. c. Stacks the results into a Series. d. Resets the index and renames columns for clarity.
Output: Returns a DataFrame with columns 'isin-1', 'isin-2', and 'pearson' (correlation coefficient).
compute_pearson_scores(df_of_pairs, df) function:

Purpose: Computes Pearson correlation scores for given pairs of items.
Inputs:
df_of_pairs: A DataFrame containing pairs to be correlated.
df: The data DataFrame used to compute correlations.
Process: a. Calls compute_pearson_coefficients(df) to get all pairwise correlations. b. Merges these correlations with the input pairs DataFrame. c. Cleans up the resulting DataFrame by handling potential duplicates and NaN values.
Output: Returns a DataFrame with correlation scores for the input pairs.
compute_adf_score(df, regression='ct', min_length=30) function:

Purpose: Computes the Augmented Dickey-Fuller test statistic for stationarity.
Inputs:
df: A DataFrame with two columns representing two time series.
regression: The type of regression to use in the ADF test (default 'ct' for constant and trend).
min_length: Minimum length of the time series to perform the test (default 30).
Process: a. Computes the difference between the two input time series. b. If the resulting series is long enough, performs the ADF test using statsmodels' adfuller function. c. Returns the p-value of the test.
Output: Returns the ADF test p-value, or 1.0 if the series is too short, or NaN if there's an error.
compute_return(df, predict_on_date, evaluate_on_date) function:

Purpose: Computes the return between two dates for a pair of time series.
Inputs:
df: A DataFrame with two columns representing two time series.
predict_on_date: The date to start the return calculation.
evaluate_on_date: The date to end the return calculation.
Process: a. Computes the difference between the two input time series. b. Calculates the return as the difference in this spread between the evaluation and prediction dates. c. Adjusts the sign of the return based on the position relative to the mean.
Output: Returns the computed return.