## Exploratory Data Analysis (EDA) on Unemployment rates in America

**Project description:** This project utilizes data from the Federal Reserve Economic Data (FRED) API to analyze unemployment rates at various levels: national, state, and industry-specific. By visualizing and examining unemployment trends, the project aims to provide insights into the impact of economic factors on employment across different regions and sectors.

### Importing the FRED API
The initial step involves importing the 'fredapi' library and establishing the connection with a provided API key, which is securely stored as an environment variable. Subsequently, a 'fred' object is created, enabling access to economic data for analysis.

```
from fredapi import Fred
fred_key = os.environ.get('fred_api_key')
fred = Fred(api_key=fred_key)
```
Now raw data can be pulled straight into my notebook. 

<img src="images/uemp.png?raw=true"/>

*Overall Unemployment Rate (1948- )*

### Unemployment by State (1976- )

While it's easy to plot certain data, such as the overall unemployment rate, visualizing specific insights requires us to clean and reorganize the data through transformations.

Here we want to visualize unemployment rates across U.S. states. See the process of transforming data in python with the following code snippets:

- Filtering and Cleaning Data

```
uemp_state = fred.search('unemployment rate state', 
    filter=('frequency','Monthly'))

uemp_state = uemp_state.query('seasonal_adjustment == 
    "Seasonally Adjusted" and units == "Percent"')

uemp_state = uemp_state.loc[uemp_state['title']
    .str.contains('Unemployment Rate in')]

uemp_state = uemp_state.loc[uemp_state
    ['id'].str.len() == 4]

uemp_state = uemp_state.drop('DCUR')
uemp_state = uemp_state.drop('PRUR')
```
*In this section, we filter the unemployment data by frequency (monthly), seasonal adjustment (seasonally adjusted), and units (percentage). We also narrow down to titles specifically related to "Unemployment Rate in" and remove entries for Washington DC and Puerto Rico, as they're not states.*

- Retrieving and Combining Data

```
all_results = []
for myid in uemp_state.index:
    results = fred.get_series(myid)
    results = results.to_frame(name=myid)
    all_results.append(results)

uemp_state_results = pd.concat(all_results, axis = 1)
```
*Retrieve the unemployment data for each state by iterating over the filtered IDs. Then combine all the data into a single dataframe.*

- Renaming Columns

```
id_to_state = uemp_state['title'].str
    .replace('Unemployment Rate in ','').to_dict()
uemp_state_results.columns = [id_to_state[i] 
    for i in uemp_state_results.columns]
```
*Here, we replace the IDs in the column names of the dataframe with the corresponding state names.*

- Plotting

```
px.line(uemp_state_results,
    title='Unemployment Rates by State')
```
<img src="images/statePlot.png?raw=true" />

Below I have added similar plots giving insight to how unemployment was affected by region and industry, including bar charts during the peak (April 2020) due to the pandemic:

<img src="images/stateBar.png?raw=true"/> 

---

<img src="images/countyPlot.png?raw=true" />


<img src="images/countyBar.png?raw=true"/>

---

<img src="images/industryPlot.png?raw=true" />

<img src="images/industryBar.png?raw=true" />

---

### Unemployment vs Key Economic Indicators

Now let's examine how the unemployment rate correlates with various key economic metrics sourced from FRED. These indicators provide insights into different aspects of the economy and might help us understand the factors driving employment trends.

Our exploration begins with a look at SPY, a well-known benchmark for the U.S. stock market's performance:

<img src="images/spyPlot.png?raw=true" />

From there, we move on to explore other critical indicators, each providing unique insights into different aspects of the economy:

- **Industrial Production**: Output of the industrial sector of the economy, including manufacturing, mining, and utilities.
- **Consumer Sentiment Index**: Measure of consumer confidence regarding the overall state of the economy.
- **Participation Rate**: Percentage of working-age individuals who are either employed or actively seeking employment.
- **All Employees**: Total number of non-farm payroll jobs in the economy.
- **Consumer Price Index**: Average change over time in the prices paid by urban consumers for a basket of goods and services.
- **Federal Funds Effective Rate**: Interest rate at which banks lend money to each other overnight without requiring collateral.
- **Real GDP**: Total value of all goods and services produced within a country's borders adjusted for inflation.
- **Money Supply**: Total amount of money in circulation within an economy.

<img src="images/indicatorsPlot.png?raw=true" />

See the Jupyter Notebook with full code for the project <a href="https://github.com/jaredec/jaredec.github.io/blob/master/projects/unemp/unemp.ipynb" target="_blank">here</a>

---

> Data from <a href="https://fred.stlouisfed.org/" target="_blank">Federal Reserve Bank of St. Louis</a>
