# SDG Data Quality and Collection-Priority Dashboard

An interactive Power BI dashboard for identifying gaps in national reporting on the Sustainable Development Goals (SDGs). It compares data availability, timeliness, and demographic or geographic disaggregation across countries and areas, then summarizes those dimensions from the UN's six key transitions down to individual SDG series.

The dashboard is intended to help analysts explore where additional statistical collection or reporting effort may be most useful. A higher score indicates stronger data coverage under the selected weights; a lower score indicates a larger potential collection priority.

> **Data vintage:** 2023. The dashboard is a historical snapshot and does not reflect subsequent updates to the UN SDG Global Database.

<!-- Add a dashboard overview image here:
![Dashboard overview](images/dashboard-overview.png)
-->

## Product overview

The report supports filtering and comparison by:

- country or area;
- UN key transition;
- SDG goal;
- SDG target;
- SDG indicator; and
- statistical series.

It contains two principal analytical views:

1. **Data Quality Score** — compares the composite score across goals, targets, indicators, and series. Three sliders let the user adjust the relative weight assigned to availability, timeliness, and disaggregation.
2. **Component diagnostics** — donut charts show the balance between available and unavailable data, recent and older data, and fully versus partially disaggregated data.

The embedded snapshot contains 208,627 analytical records covering 255 geographic entities, 17 goals, 158 targets, 234 indicators, and 613 unique series. Geographic entities include countries, territories, and some subnational entries, so the total should not be read as a count of sovereign states.

## Data sources

The underlying observations were retrieved in 2023 from the [United Nations Statistics Division SDG API](https://unstats.un.org/SDGAPI/swagger/), using the series-last-updated resource documented in the dashboard. Series codes and observations come from the UN SDG Global Database.

Goal, target, and indicator labels follow the UN global indicator framework and its official [SDG metadata repository](https://unstats.un.org/sdgs/metadata/).

The dashboard also groups goals under the UN's [six key transitions](https://unsdg.un.org/resources/six-transitions-investment-pathways-deliver-sdgs):

- food systems;
- energy access and affordability;
- digital connectivity;
- education;
- jobs and social protection; and
- climate change, biodiversity loss, and pollution.

These transition groupings provide a policy-oriented entry point across the 17 SDGs. Because goals can contribute to more than one transition, the transition categories are not mutually exclusive.

## Methodology

Each stored record receives three gap penalties. Lower penalties represent better coverage.

### 1. Data availability

A series that has never been reported for the selected geographic entity receives an availability penalty of 9. A reported series receives 0. When a series is unavailable, its timeliness and disaggregation penalties are set to 0 so the same absence is not penalized three times.

### 2. Data timeliness

Timeliness is based on the latest year reported in the 2023 snapshot:

| Latest observation | Penalty |
|---|---:|
| 2020 or later | 0 |
| 2019 | 0.5 |
| 2018 | 1.0 |
| 2017 | 1.5 |
| 2016 | 2.0 |
| 2015 | 2.5 |
| 2014 or earlier | 3.0 |

This is a deliberately simple recency rule. It does not adjust for the expected reporting frequency of different indicators.

### 3. Availability of disaggregated data

The model checks whether a reported series contains breakdowns by age, sex, and location. If **k** of those three dimensions are represented, the penalty is:

**Disaggregation penalty = 3 − k**

For example, a series disaggregated by age and sex but not location receives a penalty of 1. Goals 7, 12, 13, and 14 are treated as exempt from these three disaggregation dimensions and receive a disaggregation penalty of 0.

### Composite score

For **n** records and user-selected availability, timeliness, and disaggregation weights, the dashboard calculates:

**Data Quality Score = 100 × [1 − Σ(weighted gap penalties) / 9n]**

The reference results below use equal weights of 1. Users can vary each weight between 0 and 1 in increments of 0.1 to reflect different collection priorities.

The score is an exploratory prioritization index developed for this dashboard. It is not an official UN data-quality measure and does not assess sampling error, accuracy, methodological comparability, or the substantive level of SDG achievement.

## Findings from the 2023 snapshot

The following descriptive findings reproduce the dashboard's record-level aggregation with equal weights:

1. **Availability remains a major constraint.** Of the 208,627 stored analytical records, 68,425 (32.8%) represent series that had never been reported for the corresponding geographic entity in the snapshot.

2. **Disaggregation is the largest recurring gap among reported records.** Among the 140,202 records with reported data, 84.4% carry a nonzero disaggregation penalty, while 34.2% carry a timeliness penalty because the latest observation predates 2020. The disaggregation result should be interpreted in light of the exemption applied to Goals 7, 12, 13, and 14.

3. **The weakest goal-level coverage is concentrated in cross-cutting institutional and inclusion domains.** Under equal weights, the lowest record-weighted composite scores are for Goal 10, Reduced Inequalities (36.0%); Goal 17, Partnerships for the Goals (36.5%); and Goal 11, Sustainable Cities and Communities (39.7%). Goal 13, Climate Action (85.3%), and Goal 7, Affordable and Clean Energy (78.0%), score highest, partly because both are exempt from the dashboard's age/sex/location disaggregation penalty.

These results describe coverage in the UN database snapshot, not whether relevant information exists elsewhere in national statistical systems. Low scores should be treated as prompts for validation with national data producers before directing collection resources.

<!-- Add one or more findings images here:
![Country-level results](images/country-results.png)
![Goal-level results](images/goal-results.png)
-->

## Using the dashboard

1. Download [**SDG Prioritization Dashboard - All Countries.pbix**](./SDG%20Prioritization%20Dashboard%20-%20All%20Countries.pbix).
2. Open it in Microsoft Power BI Desktop.
3. Select a country or area.
4. Use the key-transition, goal, target, indicator, and series filters to narrow the analysis.
5. Adjust the three component weights to test alternative collection priorities.
6. Review the component charts before interpreting the composite score.

## Reproducibility and limitations

The repository currently provides the self-contained PBIX artifact with its embedded 2023 data model. The original extraction and transformation scripts and source CSV files are not included, and the Power Query definitions retain local source paths. The existing file can therefore be opened and explored, but refreshing the model requires reconstructing those source inputs.

Several series appear in multiple stored records because of alternative SDG mappings or available age, sex, and location breakdowns. The current DAX measure averages stored records, so aggregate scores are record-weighted rather than a strict one-series-one-vote calculation. This particularly affects comparisons in which one country or goal has more reported breakdowns than another.

The fixed 2020 recency threshold was appropriate to the 2023 snapshot but should be updated if the data are refreshed. Future versions should also preserve the snapshot date, source query, transformation code, and a unique aggregation key so results can be reproduced and compared over time.
