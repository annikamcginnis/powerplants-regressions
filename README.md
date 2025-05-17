# Natural Gas Power Plants Regressions: National and Maryland

In this repo, I scrape national power plants data from the Energy Information Administration, merge it with CDC Environmental Justice Index data on the census tract level, and conduct various linear and logistic statistical regressions assessing the relationship between natural gas power plant existence and various environmental justice and health indicators. I also conduct regressions on a list of potential locations for new power plants in Maryland. This project was conducted in partnership with Lucia de la Torre.

## Goal
- To assess the relationship between the location of natural gas power plants and various socioeconomic and health-related indicators of census tracts located within 0-20km of the plants, on a national-level and in Maryland
- To assess the socioeconomic and health characteristics of census tracts located within 0-20km of possible locations of new power plants in Maryland

## Main Findings
Our analysis demonstrated a strong spatial relationship between proximity to natural gas plants and adverse environmental conditions in Maryland. Some of our main findings were: 
- Air Toxic Risk: A clear negative slope showed higher air toxics risk in areas closer to natural gas plants, with proximity associated with higher toxic risk scores. This relationship was consistent across multiple pollutants including PM2.5, ozone, and diesel particulate matter.
- Worse Air Quality: Census tracts closer to natural gas plants experienced significantly more days above regulatory standards for both ozone and particulate matter, with values diminishing as distance from plants increased.
- Asthma Prevalence: A consistent negative relationship between distance to plants and asthma rates was observed, with census tracts closer to plants showing elevated asthma rates compared to areas farther away. This pattern was particularly pronounced in several counties including Howard, Prince George's, and Charles, though Baltimore City showed more complex patterns potentially influenced by other urban environmental factors.

Socioeconomic Vulnerability Patterns
Our regression analyses revealed strong associations between plant proximity and socioeconomic vulnerability indicators:
- Poverty Concentration: Significantly higher poverty rates were observed near plants, with a steep gradient visible in many counties. This pattern was especially pronounced in Baltimore City, where regression lines showed substantially higher poverty rates in areas closest to natural gas facilities.
- Housing Characteristics: Strong negative relationships showed higher percentages of renters closer to plants across multiple counties. Similarly, housing cost burden was elevated in proximity to plants in most counties, suggesting financial strain on nearby residents.
- Educational Attainment: Many areas showed higher percentages of residents without high school diplomas closer to plants, particularly in Baltimore City and Baltimore County, though this pattern varied more at the county level, with Prince George's and St. Mary's counties showing opposite trends.

## Data Collection
- Power plant locations: A dataset of all U.S. power plants was obtained from the [Energy Information Administration](https://www.eia.gov/beta/electricity/data/browser/#/topic/1?agg=2,0,1&fuel=vtvv&sec=g&geo=g&freq=A&datecode=2023&tab=overview&start=200101&end=201710&ctype=linechart&ltype=pin&maptype=0&rse=0&pin=), with latitude and longitude coordinates extracted from each plant’s URL through scraping (notebook 'Scraping Coordinates for U.S. Power Plants from EIA').
- Environmental Justice Data: [CDC Environmental Justice Index dataset](https://www.atsdr.cdc.gov/place-health/php/eji/eji-data-download.html), providing census tract-level data on socioeconomic, demographic and health indicators.
- Potential Future Plant Sites: A dataset of 35 retired power plant locations ('maryland_possible_locations.csv') across 15 Maryland counties identified as potential sites for new natural gas plants, sourced from Phil Webster, a legislative environmental advocate with Maryland's Unitarian Church climate division.

Census geographies for U.S. power plants and Maryland potential locations were obtained from the U.S. Census' official Geocoder API (notebook 'Getting Census Geographies').

U.S. power plants and Maryland potential locations data were merged with the CDC environmental justice data on GEOIDs of census tracts.

## Data Analysis
We used the following languages, libraries, and applications in our data cleaning and analysis:
- R
- Tidyverse
- DescTools
- ggplot
- Python
- BeautifulSoup
- Playwright
- pandas
- Geocorr 2022

In notebook 'Determining Centroids and Distance Bands - Existing Plants', we imported centroids of U.S. census tracts weighted by population ('geocorr.csv'), calculated through the [Geocorr 2022: Geographic Correspondence Engine by the Missouri Census Data Center](https://mcdc.missouri.edu/applications/geocorr2022.html), and merged them with my main dataset. We were then able to calculate the distance between each census tract and the closest possible new natural gas plant location, saved as the 'distance_to_plant' variable that we use as the Y-variable for most of our regressions. We also determine four distance bands for tracts located between 0-1 km from a natural gas plant, between 1-5 km, 5-10 km and 10-20 km, saved as a categorical variable.
We do the same for a dataset with all U.S. census tracts and Maryland possible locations for new plants in notebook 'Determining Centroids and Distance Bands - Possible Plant Locations.'

In notebooks 'Revising EJI-Plants Dataset to One Row Per GEOID' and 'Revising EJI-Proposed Plants Dataset to One Row Per GEOID,' we aggregate data by GEOID (census tract), keeping only one row per GEOID and creating new columns with the total number and all the plant names in each GEOID. This ensured our final dataframes for regressions contained only one row per census tract, even when the tract hosted multiple power plants. 

In notebook 'Natural Gas- Bands,' we create sub-datasets containing tracts located within 5km, 10km, and 20km of natural gas power plants on a national level and in Maryland. We do the same for the possible plants locations in Maryland. Six final datasets are produced that were used for regressions. On the national level, these are: 'tracts_within_5_km_plant.csv', 'tracts_within_10_km_plant.csv' and 'tracts_within_20_km_plant.csv.' On the Maryland level, these are: 'md_tracts_within_5_km_plant.csv', 'md_tracts_within_10_km_plant.csv' and 'md_tracts_within_20_km_plant.csv.' For Maryland possible new plants locations, these are 'tracts_within_5_km_proposed_plant.csv', 'tracts_within_10_km_proposed_plant.csv' and 'tracts_within_20_km_proposed_plant.csv.'

## Regressions
We ran statistical regressions comparing various CDC environmental justice variables with the distance_to_plant Y-variable. These are located in notebooks 'Regressions - National Level- Actual Plant Locations', 'Regressions - Maryland - Existing Plant Locations' and 'Regressions - Proposed Plant Locations.' Our independent variable was distance to plant (in km).

Dependent variables included the following from the EJI Data: 

Socioeconomic Indicators:
- E_POV200: Percentage of persons with income below 200% of the federal poverty level
- E_UNEMP: Percentage of persons who are unemployed
- E_HOUBDN: Percentage of households that make less than 75,000 who are burdened by housing costs
- E_NOHSDP: Percentage of persons with no high school diploma (age 25+)
- E_RENTER: Percentage of housing units that are renter occupied
- E_UNINSUR: Percentage of persons who are uninsured
Demographic Indicators:
- E_AFAM: Percentage of Black and African American, Not Hispanic or Latino
- E_HISP: Percentage of Hispanic or Latino (of any race)
- E_AGE17: Persons aged 17 and younger
- E_AGE65: Persons aged 65 and older
- E_LIMENG: Percentage of persons (age 5+) who speak English "less than well"
Environmental Quality Indicators:
- SPL_EBM_DOM1: Air toxic risk (composite measure)
- E_PM: Annual mean number of days above regulatory standard for Particulate Matter 2.5
- E_OZONE: Annual mean number of days above regulatory standard for Ozone
- E_IMPWTR: Percentage of census tract that intersects impaired watershed
Health Outcome Indicators:
- E_ASTHMA: Percentage of individuals with asthma
- E_CANCER: Percentage of individuals with cancer
- E_CHD: Percentage of individuals with coronary heart disease
- E_TOTCR: The likelihood of developing cancer from air toxics over the course of a lifetime, assuming continuous exposure

We conducted simple linear regressions for each environmental justice indicator to establish baseline relationships with distance to natural gas plants. These models were run for all key socioeconomic, demographic, and environmental variables at each distance threshold (5km, 10km, and 20km). 

We developed a series of multivariate linear models to account for potential correlated factors. We further analyzed model residuals to evaluate model fit and identify patterns in unexplained variance.

We also used facet wrapping to assess how relationships varied across counties and states.

Scatterplots, including small multiples, were produced for each variable and geographic threshold as part of our exploratory data analysis.

We further computed basic summary statistics, comparing mean values between tracts near plants and those further away to quantify disparities. These statistics helped confirm patterns identified in the regression analyses, showing consistent relationships between plant proximity and key socioeconomic and health indicators.

Further description of our methodology, findings and limitations to our approach are laid out in our full methodology post.

