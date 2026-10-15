# NYC EMS Response Times by Borough

A comparison of 2021 emergency medical response times across New York City's five boroughs, set against each borough's income and poverty rate.

## The question

Do lower-income boroughs get slower or worse emergency medical service than higher-income ones?

## Approach

The project pulls together three public sources, each in a different format:

- **EMS dispatch records** (CSV, 5.6 GB, about 25 million calls) from [NYC Open Data](https://data.cityofnewyork.us/Public-Safety/EMS-Incident-Dispatch-Data/76xm-jjuj). The file was too large to load at once, so it was split into 1-million-row chunks and the 2021 rows were pulled out.
- **Median household income and poverty rate** by borough, scraped from the [NYU Furman Center](https://furmancenter.org/stateofthecity/view/citywide-and-borough-data) with BeautifulSoup. The data stops at 2018, so 2021 values were estimated with a linear trend.
- **Monthly average response times to life-threatening medical emergencies** (JSON) from [911 Open Data, Local Law 119](https://catalog.data.gov/dataset/911-open-data-local-law-119).

**Tools:** Python, pandas, NumPy, scikit-learn, BeautifulSoup, matplotlib.

## Findings

**Income ranking (estimated for 2021):**

| Borough | Median household income | Poverty rate |
|---|---|---|
| Bronx | $37,011 | 27.3% |
| Brooklyn | $60,948 | 18.4% |
| Queens | $68,165 | 11.7% |
| Staten Island | $81,883 | 11.9% |
| Manhattan | $85,820 | 14.4% |

**Response times to life-threatening emergencies** (monthly averages for 2021):

| Borough | Mean (seconds) | Median | Std. dev. across months |
|---|---|---|---|
| Bronx | 607.5 | 603.5 | 32.4 |
| Queens | 594.0 | 590.4 | 24.6 |
| Staten Island | 577.2 | 568.2 | 24.2 |
| Brooklyn | 571.2 | 567.3 | 29.6 |
| Manhattan | 569.7 | 569.7 | 18.6 |

- The Bronx, the lowest-income borough, had the slowest average response, about 38 seconds slower than Manhattan, the highest-income one. Its response times also varied the most from month to month.
- The pattern isn't a clean income ranking. Brooklyn is the second-lowest-income borough but averaged nearly the same as Manhattan.

**Exploratory results from individual calls.** These come from a small subset of 1,799 calls, so treat them with caution:

- The share of calls ending in treatment or transport was lowest in the Bronx (96.3%) and highest in Queens and Manhattan (99.1%).
- A linear regression of incident response time on dispatch response time fit closely (R² = 0.984). The Bronx had the highest share of calls answered faster than the model predicted (84.7%), and Staten Island the lowest (57.7%).

## Limitations

- **The subset is small and skewed.** The severity filter keeps codes 8 and above, which in NYC's coding are the *least* urgent calls. Only 1,799 of the roughly 1.5 million 2021 calls passed the filters.
- **Disposition codes:** the treatment comparison counts code 96 as a bad outcome. In NYC's data dictionary, 96 appears to mean "patient gone on arrival" rather than a death.
- **Income estimates are rough.** The 2021 figures are extrapolated from four data points per borough.

## How to run it

1. The `data/` folder already has everything the notebook needs:
   - `nyc_data.json`: the [911 Open Data, Local Law 119](https://catalog.data.gov/dataset/911-open-data-local-law-119) dataset
   - `borough_demographics.xlsx`: the scraped income and poverty data, created by `scrape_borough_data()`
   - `2021_ems_data.csv`: the filtered 2021 calls, created by `clean_ems_data()`. Rebuilding it needs the full 5.6 GB [EMS dispatch download](https://data.cityofnewyork.us/Public-Safety/EMS-Incident-Dispatch-Data/76xm-jjuj), which isn't included.
2. Install the packages:
   ```
   pip install pandas numpy scikit-learn matplotlib openpyxl xlsxwriter requests beautifulsoup4 jupyter
   ```
3. Open and run `nyc_ems_response_times.ipynb`.

## Background

Fall 2022.
