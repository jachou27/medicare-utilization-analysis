# Geographic and Socioeconomic Variation in Medicare Healthcare Utilization

This project examines how Original Medicare healthcare utilization varies geographically across the United States and how county-level socioeconomic factors are associated with these differences.

## Research Questions

1. How do inpatient, outpatient, and emergency department utilization rates vary geographically among Original Medicare beneficiaries?
2. How are county-level income, poverty, and educational attainment associated with these utilization patterns?

## Data Sources

### CMS Medicare Geographic Variation

The project uses the CMS Original Medicare Geographic Variation Public Use File for 2020–2024.

Selected variables include:

- Original Medicare beneficiary count
- Average beneficiary age
- Female and male percentage
- Inpatient stays per 1,000 beneficiaries
- Outpatient visits per 1,000 beneficiaries
- Emergency department visits per 1,000 beneficiaries

The raw CMS file is included in:

`data/raw/cms/`

### American Community Survey

County-level socioeconomic data comes from the U.S. Census Bureau 2024 ACS 5-Year Data Profiles and is retrieved through the Census API.

Selected variables include:

- Median household income
- Poverty rate
- Bachelor's degree or higher percentage

The 2024 ACS 5-Year estimates represent pooled survey data from 2020–2024 rather than separate annual observations. CMS annual data from 2020–2024 is therefore aggregated to one record per geography before merging with ACS.

## Data Processing

Data cleaning and integration are performed using Python and DuckDB.

Main processing steps:

1. Filter CMS data to county-level observations from 2020–2024.
2. Convert CMS suppressed values (`*`) to `NULL`.
3. Retrieve ACS socioeconomic variables using the Census API.
4. Convert invalid Census sentinel values to `NULL`.
5. Standardize 5-digit county FIPS codes.
6. Resolve geographic mismatches between CMS and ACS:
   - Exclude Connecticut because CMS changes from the historical 8-county geography to 9 planning regions during the study period.
   - Unmatched Puerto Rico, District of Columbia, and U.S. Virgin Islands geographies are excluded during the final merge.
7. Aggregate CMS annual measures to one record per geography.
8. Calculate beneficiary-weighted averages for demographic and utilization measures.
9. Join CMS and ACS data using county FIPS codes.
10. Validate duplicates, missing values, geography coverage, and variable ranges.

The final merged dataset contains 3,134 geographic units.

## Missing Data

CMS suppressed values are retained as `NULL` rather than imputed or converted to zero. Census negative sentinel values are also converted to `NULL`.

Geographies are not removed solely because an individual measure is missing. Missing observations can instead be excluded as needed for specific analyses.

## Project Structure

```text
medicare-utilization-analysis/
├── data/
│   ├── raw/
│   │   └── cms/
│   │       └── 2014-2024 Original Medicare Geographic Variation Public Use File.csv
│   └── processed/
│       └── medicare_acs_analysis.csv
├── data_cleaning.ipynb
├── README.md
├── requirements.txt
└── .gitignore
```

## Setup

Create a `.env` file in the project root containing your Census API key:

```text
CENSUS_API_KEY=your_api_key_here
```

The `.env` file is excluded from Git version control.

Install the required Python packages:

```bash
pip install -r requirements.txt
```

Then run the notebook:

`data_cleaning.ipynb`

The notebook retrieves ACS data through the Census API, cleans and aggregates CMS data, merges the two datasets, performs validation checks, and exports the final analysis-ready dataset.

## Output

The final analysis-ready dataset is saved as:

`data/processed/medicare_acs_analysis.csv`

It contains 3,134 geographic units with CMS utilization and demographic measures combined with ACS socioeconomic indicators.
