# Additional Materials

## 1. Purpose

This folder provides data access and preparation instructions and the code for reproducing the reported area-level and branch-level TDABM analyses. Users should obtain the required inputs from the sources below and prepare them in the formats expected by the notebooks.

The workflow combines England and Wales Census 2021 data, Scotland Census 2022 data, Geolytix bank-branch records, a postcode to small area lookup file and a combined Great Britain boundary file. Final outputs contain two analytical CSV files, exploratory analyses, maps, Ball Mapper graphs and robustness checks.

## 2. Data Sources and Websites

### Census data

Census data describe the social and economic characteristics of the neighbourhoods in which bank branches are located. Census data input in theThis study uses the `2021 Census for England and Wales at LSOA level` and the `2022 Census for Scotland at Data Zone level`.
Required topics are including age, household deprivation, general health, disability, car or van availability, industry, National Statistics Socio-economic Classification (NS-SEC), qualifications and economic activity. 
England and Wales data are available through [Nomis Census 2021 downloads](https://www.nomisweb.co.uk/sources/census_2021_bulk). Scottish data can be obtained through [Scotland’s Census](https://www.scotlandscensus.gov.uk/webapi/jsf/dataCatalogueExplorer.xhtml). Select the relevant topics and geographical units, then prepare the downloaded tables in the formats specified in the data matching notebook.

### Bank branch status data 
Geolytix records provide information on branch locations and operating status. Both area level and branch level analysis will link the bank status to census census to compare `open` and `closed` branches and identify areas with `complete withdrawal` and retained provision.
Data access information is available through the [Geolytix banking page](https://geolytix.com/blog/banking-building-societies-locations-2/) and [open data main portal](https://geolytix.com/blog/tag/open-data/
). Record the release version and download date, as later updates may change the branch records and resulting counts.

### Postcode lookup and Boundary Files

Postcode lookup data assign each bank branch to its corresponding LSOA or Data Zone. Lookup file must contain pcds for the postcode and lsoa21cd while downloading the file. The latter Lsoa21cd is a field name which should contain `2021 LSOA codes for England and Wales` and `2022 Data Zone codes for Scotland`.

The [ONS Open Geography Portal](https://geoportal.statistics.gov.uk/) provides access to geographical lookup products, including the [postcode lookup access page](https://geoportal.statistics.gov.uk/datasets/9d8364ebae8b4439aa66cda440e54fc8/about). Check that the lookup covers the required countries and uses geographical identifiers consistent with the census inputs.

Boundary files provide the polygons used to map banking outcomes. The study combines [England and Wales 2021 LSOA boundaries](https://geoportal.statistics.gov.uk/datasets/ons::lower-layer-super-output-areas-december-2021-boundaries-ew-bfc-v10-2/about) with [Scotland 2022 Data Zone boundaries](https://spatialdata.gov.scot/geonetwork/srv/eng/catalog.search#/metadata/f6656adf-b720-4612-ad5c-1d13eae94c8b). Both layers are aligned to the same coordinate reference system and their area-code fields are standardised before being combined into a Great Britain boundary layer.


## 3. Run order

Before running the notebooks, download and prepare the data listed in Sections 2 and 4, create the required directories. Open Jupyter from the package root and run the notebooks in the order below.

### Step 1: `data matching and cleaning.ipynb`

Reads all Census, bank and postcode files from `data/raw` and `data/reference`. It standardises area identifiers, reshapes the Census source tables, checks each area merges, attaches bank records to their located small areas:

`data/processed/eda_area_level.csv`
`data/processed/eda_bank_level.csv`

### Step 2: `notebooks/01_area_level_EDA.ipynb`

Reads both processed CSV files, constructs percentages and area-level bank-status measures, writes descriptive tables to `results/eda_tables`, and writes area-level distributions and maps to `results/figures`. To combine the two shape file by running the last block of the notebook file. The prepared combined boundary file is read from `spatial`.

### Step 3: `notebooks/02_area_level_TDABM_robustness_pyballmapper.ipynb`

Builds the area-level neighbourhood-characteristics space from 30 Census features. Banking outcomes are used for colouring and do not determine distance or ball membership. The main manually selected radius is 7.2, and the baseline landmark seed is 42. The notebook also contains reduced-variable and expanded-variable checks, followed by a landmark-order check. The 10,000-seed procedure is computationally expensive and may require several hours depending on hardware. 
Warning 

### Step 4: `notebooks/03_bank_level_EDA.ipynb`

Produces branch-record descriptive tables, distributions and maps. Open, closed and closing records are retained for descriptive checks. TDABM later uses realised open and closed records for the main branch comparison.

### Step 5: `notebooks/04_bank_level_TDABM_robustness_pyballmapper.ipynb`

Builds the branch-level neighbourhood-characteristics space from the same 30 Census features. The main manually selected radius is 7.0, and the baseline landmark seed is 42. It repeats the reduced-variable, expanded-variable and landmark-order checks used in the area analysis.

### Step 6: `notebooks/tdabm_method_appendix_figures.ipynb`

Generates the synthetic diagrams used to explain TDABM method and graph construction. Explain the effect of changing epsilon. It does not use the empirical Census or branch records and may be run independently.

Notebooks can be opened from either the package root or the `notebooks` directory. All paths are resolved relative to the package root. No code refers to the author's original `C:\Users\...` location.

## 4. Required input files

### England and Wales Census 2021

Directory: `data/raw/census_england_wales`

| File | Topic |
|---|---|
| `census2021-ts007-lsoa-age 5 years.csv` | Age |
| `census2021-ts011-lsoa-deprivation.csv` | Household deprivation dimensions |
| `census2021-ts037-lsoa-General Health.csv` | General health |
| `census2021-ts038-lsoa-disability.csv` | Disability |
| `census2021-ts045-lsoa-car.csv` | Car or van availability |
| `census2021-ts060-lsoa-industry.xlsx` | Industry |
| `census2021-ts062-lsoa-NS-SEC.csv` | National Statistics Socio-economic Classification |
| `census2021-ts066-lsoa-economic activity.csv` | Economic activity |
| `census2021-ts067-lsoa-education.csv` | Qualifications |

Use the download links in Section 2. Match the filenames, worksheet names and column labels in the matching notebook. In particular, the England and Wales industry workbook must contain the prepared sheet `census2021_ts060_lsoa_wide`; it is not an unmodified bulk download.

### Scotland Census 2022

Directory: `data/raw/census_scotland`

Prepare these nine Excel files for Data Zone (2022) geography:

- `Data zone age S.xlsx`
- `Data zone Car S.xlsx`
- `Data zone deprivation S.xlsx`
- `Data zone disability S.xlsx`
- `Data zone economic activity S.xlsx`
- `Data zone education S.xlsx`
- `Data zone General Health S.xlsx`
- `Data zone Industry S.xlsx`
- `Data zone NS-SEC S.xlsx`

Each file must contain a `Data Sheet 0` worksheet with the column labels read by the matching notebook. The spelling of the expected filenames is retained for compatibility with the code.

### Geolytix bank records

File: `data/raw/bank/geolytix_uk_open_bank_branches.csv`

The input must include the identifiers, postcodes, regions and branch statuses read by the matching notebook, together with the location and closure fields used in the analyses. Retain the release version and download date. Use the Geolytix links in Section 2.

### Postcode-to-LSOA lookup

File: `data/reference/postcode_lookup/Prepared pcd to lsoa.csv`

Only the `pcds` and `lsoa21cd` fields are read by the matching notebook. Prepare these fields using the geographic definitions described in Section 2. A downloaded lookup may need additional Scottish matching or geographic harmonisation before it can serve as this input.

### Map boundary

Files: `spatial/GB_LSOA_DZ_2022.shp`, `.shx`, `.dbf`, `.prj` and `.cpg`

Keep the shapefile and its companion files in same directory folder. They form one combined plotting layer for England and Wales LSOAs and Scottish Data Zones. Shapefile is used only for mapping. TDABM distance is calculated from Census features. The main boundary file was combined from two sub file for easier graph construction.

## 5. Analytical levels and outcomes

- **Area level:** one observation is an LSOA in England or Wales or a Data Zone in Scotland. Among previously observed banking areas, `complete withdrawal` indicates closed records without a remaining open record, while `retained provision` indicates that at least one open record remains.
- **Branch level:** one observation is a matched bank record. The principal comparison is between records labelled `Open` and `Closed`. A closed branch does not necessarily imply complete withdrawal from its host area because another branch may remain there.

`Complete withdrawal` and `retained provision` can only be used in describing area-level banking status. `Open` and `closed` are used in describing individual branch records.

## 6. TDABM feature space

The same 30 Census features define distance at both analytical levels:

- age: 0-15, 16-24, 25-64 and 65 plus;
- household deprivation: zero, one and two or more dimensions;
- car availability: zero, one and two or more cars;
- health and disability: very good health, good health, other health and disability;
- qualifications: low, middle and higher groups;
- NS-SEC: managerial/professional/small-employer, intermediate/lower-supervisory, semi-routine/routine, and never-worked/long-term-unemployed groups;
- economic activity: employed, unemployed, retired, long-term sick or disabled, and other inactive groups;
- industry: production, distribution/transport, finance/real-estate/professional/administrative, and public/other services.

Each feature is standardised before Euclidean distance is calculated. Ball size directly shows observations in balls. Edges records shared observation between two balls. Colour records a node-level mean. 

## 7. Saved outputs

- `results/figures` contains all figures, Ball Mapper graphs, epsilon scans, and robustness plots.
- `results/eda_tables` contains descriptive and country/status summaries produced by notebooks 01 and 03.
- `results/tdabm_tables` contains epsilon selection results, ball summaries, selected ball details and landmark robustness outputs.

These directories receive outputs when the notebooks are run. Any saved figures or tables retained in the submission provide reference results; they do not replace the required input datasets.


