# India EV Adoption & Charging Infrastructure Analytics

I built this Power BI analytics project to explore electric-vehicle (EV) registration patterns in India, charging-network availability, connector composition, and city-level registration growth. I combined interactive Power BI dashboards, DAX measures, a dimensional data model, and R/`ggplot2` visuals to examine the data from national, infrastructure, and city-level perspectives.

> **Project focus:** move from a national view of EV adoption, to the charging-infrastructure landscape, and then to city-level growth momentum.

## Table of Contents

- [Project Overview](#project-overview)
- [Objectives and Questions](#objectives-and-questions)
- [Tools and Technologies](#tools-and-technologies)
- [Repository Structure](#repository-structure)
- [Data Sources](#data-sources)
- [Datasets](#datasets)
- [Data Model](#data-model)
- [Dashboard Pages](#dashboard-pages)
  - [Page 1: Registration Trends and Vehicle Category Analysis](#page-1-registration-trends-and-vehicle-category-analysis)
  - [Page 2: Charging Infrastructure and Statistical Gap Analysis](#page-2-charging-infrastructure-and-statistical-gap-analysis)
  - [Page 3: City-Level EV Adoption — Hotspots and Momentum](#page-3-city-level-ev-adoption--hotspots-and-momentum)
- [DAX Calculations](#dax-calculations)
- [R Visualizations](#r-visualizations)
- [Reproducing the Project](#reproducing-the-project)
- [Key Findings Shown in the Dashboards](#key-findings-shown-in-the-dashboards)
- [Limitations and Interpretation Notes](#limitations-and-interpretation-notes)
- [Author](#author)

## Project Overview

I approached India's EV market through several connected perspectives: registration demand, regional adoption, charger availability, connector types and power levels, and the relationship between vehicle registrations and charging supply. I organized this analysis into three interactive Power BI pages.

1. **EV adoption overview** — I examine registration volumes by vehicle category, year, month, state, and city.
2. **Charging infrastructure** — I explore charging sites and points, connector mix, charger power distribution, and state-level comparisons between four-wheeler registrations and car-compatible charging sites.
3. **City-level momentum** — I compare registrations over equivalent periods, identify cities adding registrations, examine yearly city growth rates, and compare city market size with growth momentum.

I used Power BI for data modelling, measures, filtering, and interactive visuals. I used R with `ggplot2` to create selected statistical charts that complement the native Power BI visuals.

## Objectives and Questions

I designed the dashboards to answer these questions:

- How are EV registrations divided between electric two-wheelers (2W) and four-wheelers (4W)?
- How have registrations changed over time, and how does the pattern vary by state and city?
- Which city markets account for substantial registration volumes?
- What does the charging-site and charge-point footprint look like in the supplied infrastructure snapshot?
- Which connector types and charger power levels account for the most listed charging points?
- How does the distribution of charger power vary across selected states?
- How do four-wheeler registrations compare with the number of car-compatible charging sites by state?
- Which cities added the most registrations between January–November 2024 and January–November 2025, and how do cities differ in their year-over-year growth?

## Tools and Technologies

| Tool / technology | Role in the project |
|---|---|
| **Microsoft Power BI Desktop** | Data model, KPI cards, slicers, maps, bar charts, line charts, treemap, matrix, and scatter visuals |
| **DAX** | Calculated date table, category-specific registration measures, charging metrics, growth calculations, and city comparisons |
| **R** | Statistical visuals embedded in Power BI |
| **`ggplot2`** | R charts: charger-congestion lollipop, charger-power boxplot, and adoption/infrastructure scatter plot |
| **CSV** | Source and prepared data files used by the model |

## Repository Structure

```text
.
├── README.md
├── assets/
│   ├── dashboard-page-1-ev-adoption.png
│   ├── dashboard-page-2-charging-infrastructure.png
│   ├── dashboard-page-3-city-momentum.png
│   └── data-model.png
├── data/
│   ├── EV_Registrations_Monthly_City.csv
│   ├── EV_Charging_Sites.csv
│   ├── EV_Charging_Connectors.csv
│   └── Dim_Cities.csv
├── measures/
│   └── EV_DAX_Measures.dax
└── scripts/
    ├── 4w_charger_congestion.R
    ├── charger_power_distribution.R
    └── ev_adoption_vs_charging_gap.R
```

I organized the DAX expressions into one reference file containing the calculated table, calculated column, and measures. I also included each R script separately so its required fields and logic can be reviewed or reused.

## Data Sources

### EV registrations

For the registration data, I used the source identified as the Ministry of Road Transport and Highways response to **Lok Sabha Starred Question No. 68**, answered on **4 December 2025**. The referenced response includes state-wise electric 2W and 4W registrations for 2020–2025, with 2025 data through **27 November 2025**.

- [Official Lok Sabha PDF — Starred Question No. 68](https://sansad.in/getFile/loksabhaquestions/annex/186/AS68_pClC3j.pdf?source=pqals)

### Charging infrastructure

For charging infrastructure, I used the snapshot associated with the **Bureau of Energy Efficiency (BEE)** E-Mobility portal. The data snapshot is dated **26 October 2025**.

- [BEE E-Mobility portal](https://beeindia.gov.in/show_content.php?lang=1&level=2&lid=67&ls_id=345)

### Charging-data intermediary

I identified **VoltMapEV's BEE-derived repository** as the intermediary source used to prepare the charging aggregate.

- [VoltMap repository — BEE charging data](https://github.com/Suriram2000/Voltmap/tree/main/mobile/assets/data/bee)

### City dimension

I used `Dim_Cities.csv` as the city dimension, including city keys and city-centroid latitude/longitude fields for the data model and map visual.

## Datasets

### 1. `EV_Registrations_Monthly_City.csv`

**Purpose:** registration fact table used to analyse adoption by state, city, time, and vehicle category.

| Column | Description |
|---|---|
| `State` | State or territory associated with the registration record |
| `City` | City associated with the record |
| `City_Key` | Combined city key used to relate city records across tables |
| `Year` | Registration year |
| `Month` | Month number |
| `Month_Date` | Month date used for calendar relationships |
| `Vehicle_Category` | Category such as `Electric 2W` or `Electric 4W` |
| `EV_Registrations` | Registration count |

I used the registration fact table at monthly city/category granularity. The data runs from 2020 into 2025, and the available 2025 period is partial.

### 2. `EV_Charging_Sites.csv`

**Purpose:** site-level charging infrastructure table.

| Column | Description |
|---|---|
| `Site_ID` | Identifier for a charging site |
| `State`, `City`, `City_Key` | Geographic identifiers |
| `Operator` | Listed charging operator |
| `Network_Class` | Network class, shown as Private / Other or Public Sector in the dashboard |
| `Latitude`, `Longitude` | Site coordinates |
| `Max_Charging_Power_kW` | Maximum listed charging power at the site |
| `Power_Class` | Power band assigned to the site |
| `Connector_Configurations` | Connector configuration count/field supplied in the data |
| `Charge_Points` | Number of charge points reported for the site |
| `Snapshot_Date` | Snapshot date for the record |

### 3. `EV_Charging_Connectors.csv`

**Purpose:** connector-level table for connector standard, AC/DC classification, rated power, and point counts.

| Column | Description |
|---|---|
| `Connector_ID` | Connector record identifier |
| `Site_ID` | Charging site identifier |
| `State`, `City_Key` | Geographic identifiers |
| `Operator` | Operator associated with the connector record |
| `Connector_Type` | Connector standard/type |
| `AC_DC` | AC or DC classification |
| `Power_kW` | Rated connector power in kW |
| `Number_of_Points` | Reported number of points for the connector record |
| `Snapshot_Date` | Snapshot date for the record |

### 4. `Dim_Cities.csv`

**Purpose:** city dimension for filtering, location labels, and map plotting.

| Column | Description |
|---|---|
| `State` | State or territory |
| `City` | City name |
| `City_Key` | City key used in relationships |
| `Latitude_Centroid` | Latitude of the city centroid |
| `Longitude_Centroid` | Longitude of the city centroid |

### 5. `Dim_Date` (calculated table)

`Dim_Date` is created in Power BI using the minimum and maximum `Month_Date` values in the registration table. It adds calendar year, month number, abbreviated month, month start, and month-year columns for chronological sorting and time-based measures.

The full DAX is in [`measures/EV_DAX_Measures.dax`](measures/EV_DAX_Measures.dax).

## Data Model

![Power BI data model](assets/data-model.png)

I connected the registration records, charging-site records, connector records, city dimension, and calendar dimension in the Power BI model.

| One-side table / key | Many-side table / key | Purpose |
|---|---|---|
| `Dim_Cities[City_Key]` | `EV_Registrations_Monthly_City[City_Key]` | Enables city-level registration analysis |
| `Dim_Cities[City_Key]` | `EV_Charging_Sites[City_Key]` | Enables city-level site analysis |
| `EV_Charging_Sites[Site_ID]` | `EV_Charging_Connectors[Site_ID]` | Connects each site to its connector records |
| `Dim_Date[Date]` | `EV_Registrations_Monthly_City[Month_Date]` | Enables date filtering and time intelligence |

The model diagram included in the repository documents these relationships. I used `City_Key` as the city-level join key instead of relying on city names alone, because the same city name can appear in more than one state.

### Date handling

Because `Month Year` is a text label, I sorted `Dim_Date[Month Year]` by `Dim_Date[Month Start]` in Power BI so month-year visuals appear chronologically. I used the date dimension for time filtering and year/month calculations.

## Dashboard Pages

## Page 1: Registration Trends and Vehicle Category Analysis

![Page 1 — EV adoption dashboard](assets/dashboard-page-1-ev-adoption.png)

**Purpose:** I designed this page to provide a national overview of EV registration volume, vehicle-category composition, geography, and trends over time.

### KPI cards

- **Total EV Registrations — 4.30M:** overall registration volume in the supplied dataset and current filter context.
- **EV 2W Registrations — 3.91M:** registration volume for electric two-wheelers.
- **EV 4W Registrations — 393.84K:** registration volume for electric four-wheelers.
- **EV Active Cities — 283:** distinct city keys represented in the registration data under the current context.
- **Latest Available Month Registrations — 129.31K:** registration count for the latest month available to the measure.

I placed these cards at the top of the page to summarize the registration picture before users explore the distribution and trend visuals.

### Top EV Cities: Volume & 4W Share Intensity (treemap)

I used a treemap to compare city registration volume through tile size and four-wheeler share through colour intensity. This lets me examine city market scale and vehicle-category mix together.

### EV Registrations by Vehicle Category (donut chart)

I used the donut to compare two-wheeler and four-wheeler registrations. In the displayed overall view, Electric 2W accounts for about **90.84%** and Electric 4W about **9.16%** of registrations, while the centre value summarises the total.

### EV Registrations and YoY Growth by Year and Vehicle Category (combo chart)

I used the combo chart to compare annual category registration totals with year-over-year growth. It helps me examine how adoption volume changes across years and how the growth rate changes as the market expands. I interpret the 2025 observations within the period available in the supplied data.

### Total EV Registrations by Month and Year (line chart)

I used a multi-year line chart in which each line represents a year and the horizontal axis shows calendar months. This makes seasonal patterns and differences in monthly registration profiles visible, and lets me compare the same months across years.

### EV Registrations by State (bar chart)

I used a sorted horizontal bar chart to rank states by registration volume in the selected context. This makes it easier to compare state totals directly without requiring the reader to compare areas or angles.

### Urban Category Quadrants: 2W vs 4W Adoption (bubble scatter)

I used a scatter plot to compare city or urban-market Electric 2W registrations against Electric 4W registrations. Bubble size represents the market scale defined in the visual. Reference lines divide the plot into quadrants, helping me distinguish markets with relatively higher 2W or 4W volumes and markets with larger overall values.

### Page 1 slicers

- **Cities:** I added this slicer to narrow the analysis to selected cities and examine local markets.
- **Year:** I used this slicer to focus the annual and monthly analysis on a chosen year or set of years.
- **Month:** I added this slicer to compare calendar months, such as September across different years.

These slicers allow the national overview to be explored at a more specific geographic and temporal level.

## Page 2: Charging Infrastructure and Statistical Gap Analysis

![Page 2 — charging infrastructure dashboard](assets/dashboard-page-2-charging-infrastructure.png)

**Purpose:** I designed this page to describe the listed charging network, compare connector types and power levels, and examine how charging availability relates to electric four-wheeler registrations.

### KPI cards

- **Charging Operators — 14:** count of distinct listed operators in the relevant filter context.
- **Total Charging Sites — 29.25K:** count of distinct charging-site identifiers.
- **Total Charge Points — 67.80K:** total charge points in the site-level data.
- **Weighted Average Charger Power — 28.5 kW:** average rated power weighted by reported number of points.
- **50 kW+ Charge Points — 13.90K:** reported charge points with connector power at or above 50 kW.

### Market Share of Connector Types (horizontal bar chart)

I used a horizontal bar chart to compare charge-point counts across connector types, with colour distinguishing AC and DC types. This helps me identify commonly listed connector standards and compare their reported scale.

### Charging Capacity by Power Tier & Sector (stacked area-style visual)

I used this visual to compare charge-point capacity across power bands and the network classes **Private / Other** and **Public Sector**. It shows how reported charging points are distributed across lower and higher rated power categories and between network classes.

### 4W Charger Congestion (Top 10 Major States) — R lollipop chart

I created this R lollipop chart to rank selected states using **EV 4W registrations per compatible site**. A dashed line indicates the average ratio across the states included in the chart. I mark states above the average as having higher relative demand pressure and those below it as having a lower ratio relative to the comparison group.

This ratio is a comparative indicator, not a direct measurement of queueing, utilisation, or service quality.

### Charger Power Distribution by State — R boxplot

I used the boxplot to compare rated charger-power distributions across six selected states: Maharashtra, Karnataka, Uttar Pradesh, Tamil Nadu, Delhi, and Gujarat. The x-axis uses a logarithmic scale because charger power values can span a wide range. The supplied script weights the distribution by reported point counts.

### EV Adoption vs Charging Infrastructure (Gap Analysis) — R scatter plot

I used this R scatter plot to compare states, with car-compatible charging sites on the x-axis and Electric 4W registrations (January–September 2025) on the y-axis. A fitted linear trend and confidence band show the overall association. I assign point colours using the linear-model residual: observations above the fitted trend are labelled as higher demand relative to the site count, while observations below it are labelled as adequate/surplus relative to the trend.

The Pearson correlation printed in the subtitle summarises the linear association between the two variables. It should not be interpreted as evidence that one variable causes the other.

### AC vs DC Charge Points by State (clustered bar chart)

I added this chart to compare AC and DC charge-point counts across states, so I can examine the mix of charging formats and the relative scale of reported AC and DC provision.

### Page 2 slicers

- **States:** I added this slicer to restrict the infrastructure analysis to selected states.
- **Operator:** I used this slicer to focus on records for a selected charging operator.
- **Network Class:** I added this control to compare the Private / Other and Public Sector categories.

These slicers support examination of the charging network by location, operator, and network type.

## Page 3: City-Level EV Adoption — Hotspots and Momentum

![Page 3 — city-level EV adoption dashboard](assets/dashboard-page-3-city-momentum.png)

**Purpose:** I designed this page to examine comparable-period changes in city-level registrations, identify cities adding registrations, and compare market size with growth momentum.

### KPI cards

- **EV Registration Change Jan–Nov — 128K:** difference between registrations in January–November 2025 and the same months in 2024 in the displayed context.
- **Cities with Positive YoY Growth — 181:** cities with a positive absolute change and a non-zero January–November 2024 baseline.
- **Cities with Negative YoY Growth — 99:** cities with a negative absolute change and a non-zero January–November 2024 baseline.
- **Median City YoY Growth — 0.08 in the displayed card:** median of city growth rates for cities with at least 1,000 registrations in the January–November 2024 baseline. The measure is a decimal ratio; 0.08 corresponds to approximately 8% when formatted as a percentage.

### Cities Adding the Most Registrations (horizontal bar chart)

I used this chart to rank cities by the absolute change between January–November 2024 and January–November 2025. It highlights where the largest increases in registration counts occurred, rather than ranking cities only by total market size. In the displayed view, the leading cities include Kochi, Bengaluru, Salem, Chhatrapati Sambhajinagar, and New Delhi.

### City Growth by Year (matrix)

I used a matrix with cities in rows and years in columns, with comparable-month year-over-year growth in each cell. This lets me compare growth across several years for the same city and identify sustained positive growth, changes in momentum, or negative periods.

### City Market Size vs Growth Momentum (bubble scatter)

I built this scatter plot with January–November 2024 city registrations on the x-axis and the corresponding year-over-year growth rate for January–November 2025 on the y-axis. Bubble size represents January–November 2025 registrations. This helps me distinguish high-growth cities from those with larger established baselines and assess whether market size and growth rate tell the same story.

### EV Charging Infrastructure Across India (map)

I included this map to provide geographic context for the supplied charging-infrastructure locations. It shows the spatial footprint of the charging network alongside the city-level registration growth analysis.

### Page 3 slicers

- **States:** I added this slicer to focus the city-level exploration on selected states.
- **Operator:** I included this control to inspect charging-network context by operator.
- **Vehicle Category:** I used this slicer to focus on Electric 2W or Electric 4W registration behaviour.

These controls allow users to change the geographic, network, and category context while exploring registration momentum.

## DAX Calculations

I collected the full DAX expressions in [`measures/EV_DAX_Measures.dax`](measures/EV_DAX_Measures.dax). The tables below group the calculations by purpose and summarize how I used them in the dashboards.

### Calendar table and sorting column

| Calculation | Purpose |
|---|---|
| `Dim_Date` | Calendar table spanning the first and last registration month, with Year, Month Number, Month, Month Start, and Month Year columns |
| `Power Category Sort` | Assigns a numeric sort band from connector `Power_kW`; invalid/non-positive power values receive a separate sort value |

### Connector and charging measures

| Measure | Purpose |
|---|---|
| `50 kW+ Charge Points` | Sums `Number_of_Points` where `Power_kW >= 50` |
| `Average Charger Power` | Arithmetic average of connector rated power |
| `Car-Compatible Sites` | Distinct site count for the connector types listed in the DAX expression |
| `DC Charging Share` | DC charge points divided by all charge points in the current filter context |
| `Leading Connector Type` | Returns the connector type with the highest summed number of points |
| `Operators Offering Fast Charging` | Distinct operator count among connector records with power at or above 50 kW |
| `Weighted Average Charger Power` | Mean power weighted by `Number_of_Points` |
| `Listed Network Categories` | Distinct count of `Operator` in the site table, as defined in the supplied formula |
| `Total Charge Points` | Sum of `EV_Charging_Sites[Charge_Points]` |
| `Total Charging Sites` | Distinct count of `Site_ID` |

### Registration and category measures

| Measure | Purpose |
|---|---|
| `Total EV Registrations` | Sum of `EV_Registrations` |
| `EV 2W Registrations` / `EV 4W Registrations` | Registration total filtered to Electric 2W or Electric 4W |
| `EV 2W Share %` / `EV 4W Share %` | Category registrations divided by total registrations |
| `EV Active Cities` / `EV Active City` | Distinct count of city keys in the registration fact table; both names were included in the supplied calculations |
| `Average Registrations per Active City` | Total registrations divided by active cities |
| `EV Registrations - Latest Available Month` | Retrieves the latest month in the supplied registration table and calculates its total |
| `Previous-Year Registrations` | Uses `DATEADD` to evaluate the same date context one year earlier |
| `EV 4W Registrations Jan-Sep 2025` | Electric 4W registrations for January–September 2025 |
| `4W Registrations per Compatible Site` | January–September 2025 4W registrations divided by car-compatible sites |

### Comparable-period growth measures

| Measure | Purpose |
|---|---|
| `EV Registrations Jan-Nov 2024` | Registration total for January–November 2024 |
| `EV Registrations Jan-Nov 2025` | Registration total for January–November 2025 |
| `EV Registration Change Jan-Nov` | Absolute difference between the two comparable periods |
| `EV YoY Growth % Jan-Nov` | Absolute change divided by the 2024 baseline |
| `Cities with Positive YoY Growth` | Counts selected cities with a positive change and a non-zero 2024 baseline |
| `Cities with Negative YoY Growth` | Counts selected cities with a negative change and a non-zero 2024 baseline |
| `Median City YoY Growth %` | Median city growth for cities with at least 1,000 registrations in the 2024 baseline |
| `YoY Growth % (Comparable Months)` | Compares a year's registrations to the same number of months in the previous year, using the latest available month number for that year |

## R Visualizations

I created the following three R scripts for the Power BI R visuals. Power BI passes the fields added to each R visual through the `dataset` data frame. To reproduce a chart, add the required fields to the visual's **Values** well and preserve the column names expected by my script, or adjust them consistently.

### 1. `scripts/4w_charger_congestion.R`

**Chart type:** lollipop-style ranking.

**Fields required in the R visual:** `State`, `[4W Registrations per Compatible Site]`, and `[EV 4W Registrations Jan-Sep 2025]`.

**What my script does:** converts the ratio and registration values to numeric form, keeps states with at least 2,500 4W registrations, selects the top 10 by registrations, and then ranks those states by registrations per compatible site. It compares each ratio with the mean of the selected states and marks above-average values as **High Stress (Above Avg)** and the others as **Low Stress (Buffer)**.

### 2. `scripts/charger_power_distribution.R`

**Chart type:** weighted boxplot with a logarithmic x-axis.

**Fields required in the R visual:** `State`, `Power_kW`, `Number_of_Points`, and `Connector_ID` from the connector data.

**What my script does:** locates the required fields even if Power BI prefixes their names, converts power and point counts to numeric values, removes invalid or non-positive records, keeps one row per `Connector_ID`, and filters to Maharashtra, Karnataka, Uttar Pradesh, Tamil Nadu, Delhi, and Gujarat. I repeat records according to `Number_of_Points` so the displayed distribution is weighted by reported charging-point count.

### 3. `scripts/ev_adoption_vs_charging_gap.R`

**Chart type:** scatter plot with linear regression line and confidence band.

**Fields required in the R visual:** `State`, `[Car-Compatible Sites]`, and `[EV 4W Registrations Jan-Sep 2025]`.

**What my script does:** removes incomplete or invalid state observations, calculates Pearson's correlation where possible, fits a linear model, and classifies states according to whether their model residual is above or below zero. I label states with a sufficiently high registration value or site count. This residual classification is relative to the fitted trend in the current set of state observations.

All three scripts use `ggplot2`. The code is included in the `scripts/` folder.

## Reproducing the Project

### Requirements

- Microsoft Power BI Desktop.
- R installed locally and configured for Power BI Desktop R visuals.
- The R package `ggplot2`.
- The four CSV files included in `data/`.

### Steps

1. **Load the data.** In Power BI Desktop, use **Get Data → Text/CSV** to import the four CSV files in `data/`.
2. **Create the date table.** Create `Dim_Date` using the expression in `measures/EV_DAX_Measures.dax`.
3. **Set up relationships.** Use the relationship list and diagram in the [Data Model](#data-model) section.
4. **Configure dates.** Sort `Dim_Date[Month Year]` by `Dim_Date[Month Start]`; use the calendar table for year/month slicing.
5. **Create calculations.** Add the measures and the `Power Category Sort` calculated column from the DAX reference. Create each expression as a separate table, column, or measure as appropriate.
6. **Build the three dashboard pages.** Use the screenshots in `assets/` as the visual reference and configure the slicers, chart field wells, sorting, titles, and formatting.
7. **Configure R visuals.** In Power BI Desktop, configure the installed R home directory under the R scripting options. Install `ggplot2` in that R environment, add the fields noted for each script to an R visual, and paste or source the corresponding script.
8. **Validate displayed figures.** Check that the measures respond as intended to the selected geography, date, category, operator, and network-class filters.

I included the README, CSV data, dashboard screenshots, model diagram, DAX reference, and R scripts in the project bundle. I have not included a Power BI `.pbix` file in the provided materials.

## Key Findings Shown in the Dashboards

In the dashboard views I captured, the following figures are displayed. They describe the current visual context and may change when filters or source data change.

- The registration overview displays **4.30 million total EV registrations**, including approximately **3.91 million Electric 2W registrations** and **393.84 thousand Electric 4W registrations**.
- The overall category visual shows about **90.84% Electric 2W** and **9.16% Electric 4W** registrations.
- The registration page displays **283 EV-active cities** and **129.31K registrations** for the latest available month represented by the measure.
- The charging-infrastructure page displays **29.25K charging sites**, **67.80K charge points**, **14 charging operators**, a **28.5 kW weighted average charger power**, and **13.90K charge points at 50 kW or above**.
- The city-momentum page displays a January–November registration change of approximately **128K**, with **181 cities** having positive absolute growth and **99 cities** having negative absolute growth under the displayed context.
- The city-momentum bar chart highlights Kochi and Bengaluru among the cities with the largest absolute increases in the displayed January–November comparison.

I treat these as values from the current dashboard views rather than permanent constants. They should be recalculated whenever the source data or filters change.

## Limitations and Interpretation Notes

- The registration source I used contains data through **27 November 2025**; I therefore interpret 2025 as a partial year and use comparable-period calculations where specified.
- The charging dataset I used represents a dated snapshot (**26 October 2025**) rather than a live charging-network feed.
- I use **registrations per compatible site** as a comparative pressure indicator, not as a direct measure of charger utilisation, queues, uptime, geographic accessibility, or the number of charging sessions a site can serve.
- I interpret Pearson correlation as a measure of linear association and model residuals as indicating whether a state sits above or below the fitted trend. Neither establishes causation.
- I use city-centroid coordinates to show approximate city locations; they do not identify precise registration locations.
- My dashboard measures follow the definitions in the DAX reference. Their outputs can change with the filter context and selected time period or geography.

## Author

**Vishesh Sanat**  
B.Tech — Artificial Intelligence & Machine Learning (AIML)  


---
