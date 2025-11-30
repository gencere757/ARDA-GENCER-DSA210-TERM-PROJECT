# ARDA-GENCER-DSA210-TERM-PROJECT
This is my term project for DSA210 course, in Fall 2025 term.
# Project Proposal
In this project, I will compare the effect of walkability index, a crucial factor when determining how easy it is for a person to be phsically active in their daily lives, and several health problems that is correlated with sedentary lifestyle, such as obesity and cardiovascular diseases. I will also be utilizing the fast food restaurant count per population as an additional feature since nutrition and access to healthy foods also plays an important role in such diseases.
## Motivation
Ever since I decided living a healthier lifestyle and changing my habits accordingly a few years ago, I became interested about the effects of environmental factors on human health. In the past, I have heard that the place you live can have a huge impact on your health. For example in USA, people usually choose to drive around instead of walking whereas in Europe, people usually walk or bike around and the obesity rates in these regions seems to correlate with how active people are in these regions of the world.
## Plan
My plan is to compare the walkability index data,  that is determined by the characteristics of the environment which would enable a person to choose walking over other modes of travel, and the amount of fast food restaurants per population for each county in US. I will be comparing this data with other datasets such as obesity rate and cardiovascular disease mortality rate and try to find out if there is any link between these features and such diseases.

## The Data That Will Be Used And How To Collect And Use It
The data I will be using will come from data.gov website. The walkability index dataset is a standalone dataset. The version I will be using is actually cleaned and formed into a proper .csv file by another person. The fast food data  will be extracted from the Food Environment Atlas provided by US Department of Agriculture, Economic Research Service. The obesity rate data is also collected by the US government and it will be received from the data.gov website. These comparisons will be made for each county in US. I am then planning to clean and compare the data for each division with each other to be able to determine the level of correlation between them.
The links to the datasets are as follows:
Walkability Index data: https://github.com/ThePseudician/EPA-Average-Walkability/blob/main/WalkabilityIndexByCounty.csv
Food Environment Atlas: https://catalog.data.gov/dataset/food-environment-atlas

## Data Analysis
### Data Sourcing
Among the data specified above, the following were used (along with the sources where I got them):
1. Walkabiltiy Index: https://github.com/ThePseudician/EPA-Average-Walkability/blob/main/WalkabilityIndexByCounty.csv
2. Fast Food Restaurants Per 1000 Population (extracted from the US food environment atlas): https://catalog.data.gov/dataset/food-environment-atlas
3. Obesity Rate Per County (extracted from the US food environment atlas): https://catalog.data.gov/dataset/food-environment-atlas 
   
### Processing And Cleaning The Data
Since the data was obtained from public sources, there may be empty or incorrect data. These data were filtered out before going into the EDA steps. For the fast food data, the data was extracted from a much larger dataset (US food environment atlas).

---

## Brief Procedure / What I Did in the Code

### 1. Data Preparation

- Imported core libraries (`pandas`, `numpy`) for data handling.
- Loaded the Food Environment Atlas dataset:
  - Filtered it to obtain county-level **adult obesity rates**.
  - Removed missing or non-positive values to keep only valid observations.
- Loaded the **Walkability Index** dataset from its CSV file.
- Extracted the **fast food restaurants per 1000 people** variable from the Food Environment Atlas:
  - Filtered to the relevant indicator.
  - Removed zero or invalid values.
  - Applied a log transform to the fast food variable to reduce skewness.
- Selected and renamed the key columns (FIPS, walkability index, obesity rate, fast food density) from each dataset.
- Merged all three data sources into one combined dataframe on county FIPS codes.
- Dropped remaining missing values and saved the processed combined data as `Processed Data.csv`.

### 2. Exploratory Data Analysis and Visualization

- Computed basic descriptive statistics for the combined dataset.
- Plotted histograms (with optional KDE curves) for:
  - Walkability index
  - Log-transformed fast food restaurants per 1000 people
  - Obesity rate  
  to examine overall distributions and shapes.
- Created scatterplots to visually inspect relationships between:
  - Walkability index vs obesity rate
  - Fast food (log-transformed) vs obesity rate
- Calculated a correlation matrix between the main variables and visualized it with a heatmap to summarize pairwise linear/nonlinear associations.

### 3. Geographic Visualizations

- Formatted county FIPS codes to 5-digit strings to match the county GeoJSON.
- Used Plotly choropleth maps to visualize county-level patterns across the USA for:
  - Obesity rates
  - Walkability index
  - Fast food restaurants per 1000 people (log-transformed)
- Included hover information so each map shows, per county, the relevant value along with the other variables for context.

### 4. Hypothesis Testing with Permutation Tests

- Formally stated hypotheses:
  - **Walkability vs Obesity**  
    - \(H_0\): No negative correlation between walkability index and obesity rate.  
    - \(H_a\): Higher walkability is associated with lower obesity (negative correlation).
  - **Fast Food vs Obesity**  
    - \(H_0\): No positive correlation between fast food restaurant density and obesity rate.  
    - \(H_a\): Higher fast food restaurant density is associated with higher obesity (positive correlation).
- Implemented a custom permutation test using **Spearman correlation**:
  - Computed the observed correlation between each feature and obesity rate.
  - Randomly permuted the outcome variable many times to generate a null distribution of correlations.
  - Calculated one-sided p-values based on where the observed correlation falls relative to the null distribution.
- Used the results to determine whether there is statistically significant evidence for:
  - A negative association between walkability and obesity.
  - A positive association between fast food density and obesity.

### 5. Visualizing Permutation Results

- Plotted histograms of the permutation-based correlation distributions for each test.
- Marked the observed correlation on each plot to visually compare it to the null distribution and support the inference from the p-values.
