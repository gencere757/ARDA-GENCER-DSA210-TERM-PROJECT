# ARDA-GENCER-DSA210-TERM-PROJECT
This is my term project for DSA210 course, in Fall 2025 term.
# Project Proposal
In this project, I will compare the effect of walkability index, a crucial factor when determining how easy it is for a person to be phsically active in their daily lives, and several health problems that is correlated with sedentary lifestyle, such as obesity and cardiovascular diseases. I will also be utilizing the fast food restaurant count per population as an additional feature since nutrition and access to healthy foods also plays an important role in such diseases.
## Motivation
Ever since I decided living a healthier lifestyle and changing my habits accordingly a few years ago, I became interested about the effects of environmental factors on human health. In the past, I have heard that the place you live can have a huge impact on your health. For example in USA, people usually choose to drive around instead of walking whereas in Europe, people usually walk or bike around and the obesity rates in these regions seems to correlate with how active people are in these regions of the world.
## Plan
My plan is to compare the walkability index data,  that is determined by the characteristics of the environment which would enable a person to choose walking over other modes of travel, and the amount of fast food restaurants per population for each county in US. I will be comparing this data with other datasets such as obesity rate and cardiovascular disease mortality rate and try to find out if there is any link between these features and such diseases.

## The Data That Will Be Used And How To Collect And Use It
The data I will be using will come from data.gov website. The walkability index dataset is a standalone dataset. The version I will be using is actually cleaned and formed into a proper .csv file by another person. The fast food data  will be extracted from the Food Environment Atlas provided by US Department of Agriculture, Economic Research Service. The heart disease mortality rate data is also collected by the US government and it will be received from the data.gov website. These comparisons will be made for each county in US. I am then planning to clean and compare the data for each division with each other to be able to determine the level of correlation between them.
The links to the datasets are as follows:
Walkability Index data: https://github.com/ThePseudician/EPA-Average-Walkability/blob/main/WalkabilityIndexByCounty.csv
Food Environment Atlas: https://catalog.data.gov/dataset/food-environment-atlas

## Data Analysis
### Data Sourcing
Among the data specified above, the following were used (along with the sources where I got them):
1. Walkabiltiy Index: https://github.com/ThePseudician/EPA-Average-Walkability/blob/main/WalkabilityIndexByCounty.csv
2. Fast Food Restaurants Per 1000 Population (extracted from the US food environment atlas): https://catalog.data.gov/dataset/food-environment-atlas
3. Heart Disease Mortality Rate: https://catalog.data.gov/dataset/heart-disease-mortality-data-among-us-adults-35-by-state-territory-and-county-2019-2021
   
### Processing And Cleaning The Data
Since the data was obtained from public sources, there may be empty or incorrect data. These data were filtered out before going into the EDA steps. For the fast food data, the data was extracted from a much larger dataset (US food environment atlas).

---

## Step-by-Step Procedure (What I Did in the Code)

### 1. Filtering, Cleaning, and Transforming the Data

#### 1.1 Importing Libraries
- Imported the main libraries needed for data handling and transformations:
  - `pandas` as `pd`
  - `numpy` as `np`

#### 1.2 Preparing the Obesity Rate Data
- Read the Food Environment Atlas CSV file:
  - `foodDF = pd.read_csv('StateAndCountyData.csv')`
- Filtered this dataset to keep only rows related to adult obesity rates:
  - Selected rows where `Variable_Code == "PCT_OBESE_ADULTS22"` to create `obesityDF`.
- Cleaned the obesity data:
  - Dropped rows with missing values using `dropna()`.
  - Kept only rows where the `Value` (obesity rate) is greater than 0.
- The result is a cleaned obesity dataframe (`obesityDF`) with valid county-level obesity rate values.

#### 1.3 Loading the Walkability Index Data
- Read the walkability index CSV file:
  - `walkDF = pd.read_csv('WalkabilityIndexByCounty.csv')`
- This dataframe contains the walkability index for each county, including FIPS codes and walkability scores.

#### 1.4 Preparing Fast Food Restaurants per 1000 People
- Re-used `foodDF` to filter fast food restaurant data:
  - Selected rows where `Variable_Code == "FFRPTH20"` to focus on **fast food restaurants per 1000 people**.
- Cleaned the fast food data:
  - Kept only rows where `Value > 0`.
- Applied a log transformation to handle right-skewed distribution:
  - Created a new column `Log_Transformed_Restaurants` using `np.log1p(restaurantDF['Value'])`.
- This produced a cleaned fast food dataframe (`restaurantDF`) with both raw and log-transformed values.

#### 1.5 Combining All Data into a Single DataFrame
- Created subset dataframes with only the necessary columns and renamed them for clarity:

  - **Walkability subset**
    - Selected `['GEOID10', 'NatWalkInd']` from `walkDF`.
    - Renamed:
      - `GEOID10` → `FIPS`
      - `NatWalkInd` → `Walkability_Index`
    - Result: `walkSubset`.

  - **Fast food subset**
    - Selected `['FIPS', 'Value', 'Log_Transformed_Restaurants']` from `restaurantDF`.
    - Renamed:
      - `Value` → `Fast_Food_Per_1000`
      - `Log_Transformed_Restaurants` → `Log_Fast_Food`
    - Result: `restSubset`.

  - **Obesity subset**
    - Selected `['FIPS', 'Value']` from `obesityDF`.
    - Renamed:
      - `Value` → `Obesity_Rate`
    - Result: `obeseSubset`.

- Merged the three datasets:
  - First merged `walkSubset` and `obeseSubset` on `FIPS` using an inner join.
  - Then merged the result with `restSubset` on `FIPS`, again using an inner join.
- Dropped any remaining missing values from the combined dataset.
- Saved the final merged dataset to a CSV file:
  - `combinedDF.to_csv('Processed Data.csv', index=False)`
- The final dataframe `combinedDF` contains:
  - `FIPS`, `Walkability_Index`, `Obesity_Rate`, `Fast_Food_Per_1000`, `Log_Fast_Food`.

---

### 2. Data Visualizations

#### 2.1 General Summary and Distribution Plots

##### 2.1.1 Getting Summary Statistics
- Called `combinedDF.describe()` to get basic descriptive statistics (count, mean, std, min, max, quartiles) for each numeric column.

##### 2.1.2 Histogram of Walkability Index
- Plotted a histogram of `Walkability_Index` using Seaborn:
  - Used `sns.histplot` with:
    - 50 bins
    - Kernel density estimate (`kde=True`)
    - Transparency (`alpha=0.6`)
- Labeled axes and added a title:
  - Title: `"Distribution Of Walkability Index"`.

##### 2.1.3 Histogram of Fast Food Restaurants (Log Transformed)
- Plotted a histogram of `Log_Fast_Food`:
  - Used `sns.histplot` with:
    - 50 bins
    - `kde=True`
    - `alpha=0.6`
- Labeled the x-axis to indicate that the values are log-transformed fast food restaurants per 1000 people.
- Title: `"Distribution Of Fast Food Restaurants Per 1000 Population"`.

##### 2.1.4 Histogram of Obesity Rate
- Plotted a histogram of `Obesity_Rate`:
  - Used `sns.histplot` with:
    - 20 bins
    - `kde=True`
    - `alpha=0.6`
- Title: `"Distribution Of Obesity Rate"`.

#### 2.1.5 Scatterplots: Features vs Obesity Rate

- **Walkability Index vs Obesity Rate**
  - Plotted a scatterplot with:
    - x-axis: `Walkability_Index`
    - y-axis: `Obesity_Rate`
    - `alpha=0.25` for semi-transparent points.
  - Title: `"Obesity Rate vs Walkability Index"`.

- **Fast Food (Log) vs Obesity Rate**
  - Plotted a scatterplot with:
    - x-axis: `Log_Fast_Food`
    - y-axis: `Obesity_Rate`
    - `alpha=0.25`.
  - Title: `"Obesity Rate vs Number Of Fast Food Restaurants Per 1000 People"`.

These scatterplots were used to visually inspect potential relationships between the features and obesity rate.

#### 2.1.6 Correlation Matrix
- Computed pairwise correlations between:
  - `Walkability_Index`
  - `Log_Fast_Food`
  - `Obesity_Rate`
- Created a heatmap using `sns.heatmap`:
  - Displayed correlation values (`annot=True`).
  - Used a `"coolwarm"` colormap.
- Title: `"Correlation Matrix"`.

---

### 2.2 Mapping the Data on a US County Map

#### 2.2.1 Importing Mapping Libraries
- Imported:
  - `plotly.express` as `px`
  - `plotly.graph_objects` as `go`

#### 2.2.2 Preparing FIPS Codes
- Ensured that the `FIPS` column is correctly formatted as a 5-character string with leading zeros:
  - `combinedDF['FIPS'] = combinedDF['FIPS'].astype(str).str.zfill(5)`

#### 2.2.3 Choropleth Map of Obesity Rate
- Created an interactive choropleth map of `Obesity_Rate` by county:
  - Used `px.choropleth` with:
    - `geojson` of US counties from Plotly’s GitHub.
    - `locations='FIPS'`
    - `color='Obesity_Rate'` (Reds color scale).
    - `scope="usa"`.
  - Included hover data:
    - `Obesity_Rate`, `Walkability_Index`, `Fast_Food_Per_1000`.
  - Labeled and titled the map:
    - Title: `"US County-Level Data Visualization For Obesity Rates"`.
- Adjusted layout:
  - Set white lake color and margins.
  - Fixed figure height.

#### 2.2.4 Choropleth Map of Walkability Index
- Re-used the formatted `FIPS` column.
- Created a choropleth map for `Walkability_Index` using the same US counties geojson.
- Color scale based on `Walkability_Index` with a `"Reds"` scale.
- Hover data displayed:
  - `Obesity_Rate`, `Walkability_Index`, `Fast_Food_Per_1000`.
- Title: `"US County-Level Data Visualization For Walkability Index"`.

#### 2.2.5 Choropleth Map of Fast Food Restaurants (Log Transformed)
- Again ensured properly formatted `FIPS`.
- Created a choropleth map for `Log_Fast_Food` (fast food restaurants per 1000 people, log transformed).
- Used the same geojson and `"Reds"` scale.
- Hover data included:
  - `Obesity_Rate`, `Walkability_Index`, `Fast_Food_Per_1000`.
- Title: `"US County-Level Data Visualization For Fast Food Restaurant Per 1000 Population"`.

---

### 3. Hypothesis Testing

#### 3.1 Defining Hypotheses and Testing Approach

- Defined two main hypotheses:

  1. **Walkability Index**
     - Null hypothesis \(H_0\): Walkability index and obesity rate are **not negatively** correlated.
     - Alternative hypothesis \(H_a\): Walkability index and obesity rate are **negatively** correlated (higher walkability → lower obesity).

  2. **Fast Food Restaurants per 1000 People**
     - Null hypothesis \(H_0\): Fast food restaurant density and obesity rate are **not positively** correlated.
     - Alternative hypothesis \(H_a\): Fast food restaurant density and obesity rate are **positively** correlated.

- Chose **permutation testing** with **Spearman correlation**:
  - Used Spearman because obesity data did not look normally distributed (non-parametric).

#### 3.2 Permutation Test Function

- Implemented a custom permutation test function:

  - `permutation_test_correlation(x, y, n_permutations=10000, alternative='two-sided')`:
    - Converts the inputs to NumPy arrays.
    - Computes the **observed Spearman correlation** between `x` and `y`.
    - Generates `n_permutations` random permutations of `y` and computes the Spearman correlation for each permutation.
    - Based on the `alternative` hypothesis:
      - `'two-sided'`: counts how many permuted correlations have absolute value ≥ |observed|.
      - `'less'`: counts how many permuted correlations are ≤ observed (for testing negative correlations).
      - `'greater'`: counts how many permuted correlations are ≥ observed (for testing positive correlations).
    - Computes a Monte Carlo–adjusted p-value:
      - `p_value = (extreme + 1) / (n_permutations + 1)`
    - Returns:
      - Observed correlation
      - p-value
      - The full distribution of permuted correlations.

#### 3.3 Applying the Permutation Test

- **Walkability vs Obesity Rate**
  - Called `permutation_test_correlation` with:
    - `x = combinedDF['Walkability_Index']`
    - `y = combinedDF['Obesity_Rate']`
    - `n_permutations = 10000`
    - `alternative = 'less'` (testing for negative correlation).
  - Printed:
    - Observed correlation.
    - One-sided p-value.
    - An interpretation stating whether the result is significant at α = 0.05 and if there is evidence of a negative correlation.

- **Fast Food (Log) vs Obesity Rate**
  - Called `permutation_test_correlation` with:
    - `x = combinedDF['Log_Fast_Food']`
    - `y = combinedDF['Obesity_Rate']`
    - `n_permutations = 10000`
    - `alternative = 'greater'` (testing for positive correlation).
  - Printed:
    - Observed correlation.
    - One-sided p-value.
    - Interpretation stating whether the result is significant at α = 0.05 and if there is evidence of a positive correlation.

---

### 3.4 Visualizing the Permutation Distributions

- Plotted histograms of the permutation distributions for both tests:

  - **Walkability vs Obesity Rate**
    - Used `sns.histplot` to plot the distribution of `perm_dist_walkability`.
    - Added a vertical red dashed line at the observed correlation value.
    - Title: `"Permutation Test - Walkability Index vs. Obesity Rate"`.

  - **Fast Food vs Obesity Rate**
    - Used `sns.histplot` for `perm_dist_food`.
    - Added a vertical red dashed line at the observed correlation value.
    - Title: `"Permutation Test - Walkability Index vs. Obesity Rate"` (same title string in the code, even though this second plot is for fast food vs obesity).

- These plots were used to visually compare the observed correlations against the permutation-based null distributions and to support the hypothesis test conclusions.
