# rOceans

rOceans is an R package designed to facilitate the analysis and visualization of oceanographic data. It provides tools for reading, processing, and plotting data from various oceanographic instruments and databases.

## Features

- **Data Import**: Functions to read and parse data from common oceanographic data formats.
- **Data Processing**: Tools for cleaning and manipulating oceanographic datasets.
- **Visualization**: Functions to create standard plots used in oceanography, such as temperature-salinity diagrams and depth profiles.

## Installation

To install the rOceans package, you can clone the repository and build the package manually:

```bash
# Clone the repository
git clone https://github.com/monteroserra/rOceans_old_version.git

# Navigate to the package directory
cd rOceans_old_version

# Install the package
R CMD INSTALL .

---
author: "I. Montero-Serra"
date: "10/Aug/2018"
output:
  pdf_document: default
  html_document: default
tittle: rOceans Package - rOceansData Package & Study Case (GSoC2018)
---

# rOceans (GSoC2018) by Ignasi Montero-Serra 

This file describes the recently developed rOceans package, the datapackage rOceansData and contains a study case on marine benthic biodiversity. The project has been lead by Ignasi Montero-Serra and fundend by Google within the program Google Summer Code 2018.  

## Deliverable 1 rOceans
rOceans is an R Package for exploring spatial trends in marine biodiversity and conservation. It contains functions to facilitate the accessibility and exploration of spatial trends of marine biodiversity, current environmental drivers and future global change scenarios.


## Deliverable 2 rOceansData
rOceansData is an R DataPackage that contains several marine biodiversity layers and environmental drivers to make easy explorations using rOceans. It is stored in github.com/monteroserra/rOceansData


## Deliverable 3 rOceansApp
Is an R Shiny App that alllos an easy visualization of some examples of marine biodiversity computed using rOceans. The App can be explored at: 

https://monteroserra.shinyapps.io/rOceansApp/


#rOceans

## Functions list

   #    Functions' name       Description
------ ---------------------- -------------------------
  (1)   *oceanDataAccess*     access occurrences data from OBIS and GBIF for a                               list of species and provides options for data                                 quality checks
  (2)    *oceanDataCheck*     automatically merges, checks and filters large                                datasets directly downoladed from OBIS and GBIF
  (3)    *oceanAbundance*     computes global or regional spatial grids of                                  abundance of occurrences per grid cell at multiple                                 scales
  (4)    *oceanMaps*          versatile visualization tool for representing                                      spatial trends in biodiversity
  (5)    *oceanDiversity*     computes spatial explicit layers of several                                        diversity metrics including richness, corrected                                    richness, Simpson and Shannon diversity
  (6)    *oceanEnvironment*   access to data on global spatial layers of multiple                                environmental drivers and explores relationships to                                marine biodiversity metrics
  (7)    *oceanFuture*        access current and future environmental data layers                                and computes expected trends
  (8)    *oceanHotspots*      classify sites in hotspots and coldspots of                                        biodiversity
  (9)  *oceanVulnerability*   explores and ranks vulnerability of sites by                                       linking biodiversity classifications and expected                                  warming trends
------ ---------------------- -------------------------


Download rOceans from GitHub and load the package
```{r, results = "hide", warning = FALSE,  message = FALSE}
#devtools::install_github("monteroserra/rOceans")
#devtools::install_github("monteroserra/rOceansData")
library(rOceans)
library(rOceansData)
```

## Function 2 *oceanDataCheck* 
This functions allows merging, checking, filtering raw data directly downloaded from OBIS and/or GBIF and creates a common standard database. It removes occurrences with inconsistent or inconmplete taxonomic information, non-marine (land-based) occurrences, and duplicate occurrences. 

For this set of functions, we first  need to access and download data from GBIF (www.gbif.org) and OBIS (www.iobis.org). 

Here I show an example with data of species within the genus *Acropora* that were directly downloaded from the website on June 2018. The data can be accessed from my GitHub repository "monteroserra/rOceanData" or using data()

```{r}
#Accesing raw datasets
data(Acropora_GBIF) 
data(Acropora_OBIS)

#Checking OBIS or GBIF individually
Acropora_OBIS_checked = oceanDataCheck(OBIS_occurrences = Acropora_OBIS, source = "OBIS")

Acropora_GBIF_checked = oceanDataCheck(GBIF_occurrences = Acropora_GBIF, source = "GBIF")
```
This function will tell us how many occurrences were removed due to issues with the coordinates (containning NAs), land-based occurrences, and duplicate occurrences between OBIS and GBIF davases. 


Merging OBIS and GBIF Datasets, and checking for taxonomic and geographic issues
```{r}
Acropora_Total_Checked = oceanDataCheck(GBIF_occurrences = Acropora_GBIF,
                                        OBIS_occurrences = Acropora_OBIS,
                                        source = "GBIF_&_OBIS")
```

## Function 3 *oceanAbundance* 
Allows creating global or delimited spatial grids with abundance of occurrences per cell at differenc cell sizes. 

Following with the Acropora example, we will use this function to compute global rasters of abundance per grid cell for Acropora species at different resolutions (10 x 10) (5 x 5) (1 x 1) and (0.5 x 0.5) using *cell_size*

```{r}
Acropora_abundance_10x10 = oceanAbundance(occurrences = Acropora_Total_Checked, cell_size=10)
Acropora_abundance_5x5 = oceanAbundance(occurrences = Acropora_Total_Checked, cell_size=5)
Acropora_abundance_1x1 = oceanAbundance(occurrences = Acropora_Total_Checked, cell_size=1)
Acropora_abundance_0.5x0.5 = oceanAbundance(occurrences = Acropora_Total_Checked, cell_size=0.5)
```

## Function 4 *oceanMaps*  
A versatile data mapping tool for a rapid visualization of marine biodiversity patterns previously computed.  

```{r}
par(mfrow=c(2,2))
oceanMaps(Acropora_abundance_10x10, logScale=T, main="Acropora abundance (10x10)")
oceanMaps(Acropora_abundance_5x5, logScale=T, main="Acropora abundance (5x5)")
oceanMaps(Acropora_abundance_1x1, logScale=T, main="Acropora abundance (1x1)")
oceanMaps(Acropora_abundance_0.5x0.5, logScale=T, main="Acropora abundance (0.5x0.5)")
par(mfrow=c(1,1))

```


# Study Case: global patterns in benthic biodiversity

We will explore the abundance and diversity of eight major benthic groups with data downloaded from GBIF and OBIS. The data stored in *rOceansData* has already preprocessed by merging both datasets (GBIF and OBIS) and filtering and removing land-based occurrences and data with incomplete taxonomic infor using the function *oceansDataCheck()* The main groups included in this study case are: 

Anthozoans, Ascidians,Bryozoans, Porifers, Bivalves, Macroalgae, Seagrasses and sedentary Polychaets. 


Load the data from rOceansData 
```{r}

library(rOceansData)

data("Anthozoa_Total_Checked")
data("Bryozoa_Total_Checked")
data("Porifera_Total_Checked")
data("Ascidiacea_Total_Checked")
data("Bivalvia_Total_Checked")
data("Cirripeda_Polychaeta_Total_Checked")
data("Macroalgae_Total_Checked")
data("Seagrass_Total_Checked")


```

Now we compute abundance trends
```{r}
Anthozoa_abundance = oceanAbundance(occurrences = Anthozoa_Total_Checked)
Bryozoa_abundance = oceanAbundance(occurrences = Bryozoa_Total_Checked)
Porifera_abundance = oceanAbundance(occurrences = Porifera_Total_Checked)
Ascidiacea_abundance = oceanAbundance(occurrences = Ascidiacea_Total_Checked)
Bivalvia_abundance = oceanAbundance(occurrences = Bivalvia_Total_Checked)
Cirripeda_Polychaeta_abundance = oceanAbundance(occurrences = Cirripeda_Polychaeta_Total_Checked)
Macroalgae_abundance = oceanAbundance(occurrences = Macroalgae_Total_Checked)
Seagrasses_abundance = oceanAbundance(occurrences = Seagrasses_Total_Checked)

```


We can visualize the total occurrence of benthic species 
```{r}
Marine_sessile_total = rbind(
              Anthozoa_Total_Checked,
              Bryozoa_Total_Checked,
              Porifera_Total_Checked,
              Ascidiacea_Total_Checked, 
              Bivalvia_Total_Checked,
              Cirripeda_Polychaeta_Total_Checked,
              Seagrasses_Total_Checked,
              Macroalgae_Total_Checked)


Marine_sessile_abundance = oceanAbundance(occurrences =Marine_sessile_total)

oceanMaps(Marine_sessile_abundance, logScale=T, background_color = "grey70", main = "All species abundance", low_color = "#B4EEB4", mid_color = "#6959CD", high_color = "#9B3F2F")

```

This global layer can be used as a proxy for sampling effort in any in-depth analysis of benthic biodiversity using data from OBIS and GBIF.

Visualizing abundance for each group
```{r}

par(mfrow=c(2,2))

oceanMaps(Anthozoa_abundance, logScale=T, background_color = "grey70", main = "Anthozoa",
          low_color = "#FFF8DC", mid_color = "#CD5B45", high_color = "#9B3F2F")

oceanMaps(Bryozoa_abundance, logScale=T, background_color = "grey70", main = "Bryozoa",
          low_color = "#FFF8DC", mid_color = "#CD5B45", high_color = "#9B3F2F")

oceanMaps(Ascidiacea_abundance, logScale=T, background_color = "grey70", main = "Ascidiacea",
          low_color = "#B4EEB4", mid_color = "#6959CD", high_color = "#473C8B")

oceanMaps(Porifera_abundance, logScale=T, background_color = "grey70", main = "Porifera",
          low_color = "#B4EEB4", mid_color = "#6959CD", high_color = "#473C8B")

oceanMaps(Bivalvia_abundance, logScale=T, background_color = "grey70", main = "Bivalvia",
          low_color = "#FFF8DC", mid_color = "#CDB79E", high_color = "#8D7B6D")

oceanMaps(Cirripeda_Polychaeta_abundance, logScale=T, background_color = "grey70", main = "Cirripeda & Polychaeta",
          low_color = "#FFF8DC", mid_color = "#CDB79E", high_color = "#8D7B6D")

oceanMaps(Macroalgae_abundance, logScale=T, background_color = "grey70", main = "Macroalgae",
          low_color = "#CAFF70", mid_color = "#A2CD5A", high_color = "#006400")

oceanMaps(Seagrasses_abundance, logScale=T,background_color = "grey70", main = "Seagrasses",
          low_color = "#CAFF70", mid_color = "#A2CD5A", high_color = "#006400")


par(mfrow=c(1,1))

```


# Exploring diversity patterns

Diversity can be computed using the function *oceanDiversity()* for instance: 
```{r}
#Anthozoa_diversity = oceanDiversity(occurrences = Anthozoa_Total_Checked)
```

For simplicity, here we present a set of preprocessed diversity objecst of the major benthic groups, which are stored in the rOceansData package: 

Here we can observe the major patterns in species richness
```{r}

data("Anthozoa_diversity")
data("Bryozoa_diversity")
data("Ascidiacea_diversity")
data("Porifera_diversity")
data("Bivalvia_diversity")
data("Cirripeda_Polychaeta_diversity")
data("Macroalgae_diversity")
data("Seagrass_diversity")

par(mfrow=c(2,2))

oceanMaps(Anthozoa_diversity[[2]], logScale=T, background_color = "grey70", main = "Anthozoa",
          low_color = "#FFF8DC", mid_color = "#CD5B45", high_color = "#9B3F2F")

oceanMaps(Bryozoa_diversity[[2]], logScale=T, background_color = "grey70", main = "Bryozoa",
          low_color = "#FFF8DC", mid_color = "#CD5B45", high_color = "#9B3F2F")

oceanMaps(Ascidiacea_diversity[[2]], logScale=T, background_color = "grey70", main = "Ascidiacea",
          low_color = "#B4EEB4", mid_color = "#6959CD", high_color = "#473C8B")

oceanMaps(Porifera_diversity[[2]], logScale=T, background_color = "grey70", main = "Porifera",
          low_color = "#B4EEB4", mid_color = "#6959CD", high_color = "#473C8B")

oceanMaps(Bivalvia_diversity[[2]], logScale=T, background_color = "grey70", main = "Bivalvia",
          low_color = "#FFF8DC", mid_color = "#CDB79E", high_color = "#8D7B6D")


oceanMaps(Cirripeda_Polychaeta_diversity[[2]], logScale=T, background_color = "grey70", main = "Cirripeda & Polychaeta",
          low_color = "#FFF8DC", mid_color = "#CDB79E", high_color = "#8D7B6D")

oceanMaps(Macroalgae_diversity[[2]], logScale=T, background_color = "grey70", main = "Macroalgae",
          low_color = "#CAFF70", mid_color = "#A2CD5A", high_color = "#006400")

oceanMaps(Seagrasses_diversity[[2]], logScale=T,background_color = "grey70", main = "Seagrasses",
          low_color = "#CAFF70", mid_color = "#A2CD5A", high_color = "#006400")


```

And species richness applying a correction tools called rarefaction with a treshold sample size of 50 samples
```{r}
par(mfrow=c(2,2))

oceanMaps(Anthozoa_diversity[[3]], logScale=T, background_color = "grey70", main = "Anthozoa",
          low_color = "#FFF8DC", mid_color = "#CD5B45", high_color = "#9B3F2F")

oceanMaps(Bryozoa_diversity[[3]], logScale=T, background_color = "grey70", main = "Bryozoa",
          low_color = "#FFF8DC", mid_color = "#CD5B45", high_color = "#9B3F2F")

oceanMaps(Ascidiacea_diversity[[3]], logScale=T, background_color = "grey70", main = "Ascidiacea",
          low_color = "#B4EEB4", mid_color = "#6959CD", high_color = "#473C8B")

oceanMaps(Porifera_diversity[[3]], logScale=T, background_color = "grey70", main = "Porifera",
          low_color = "#B4EEB4", mid_color = "#6959CD", high_color = "#473C8B")

oceanMaps(Bivalvia_diversity[[3]], logScale=T, background_color = "grey70", main = "Bivalvia",
          low_color = "#FFF8DC", mid_color = "#CDB79E", high_color = "#8D7B6D")

oceanMaps(Cirripeda_Polychaeta_diversity[[3]], logScale=T, background_color = "grey70", main = "Cirripeda & Polychaeta",
          low_color = "#FFF8DC", mid_color = "#CDB79E", high_color = "#8D7B6D")

oceanMaps(Macroalgae_diversity[[3]], logScale=T, background_color = "grey70", main = "Macroalgae",
          low_color = "#CAFF70", mid_color = "#A2CD5A", high_color = "#006400")

oceanMaps(Seagrasses_diversity[[3]], logScale=T,background_color = "grey70", main = "Seagrasses",
          low_color = "#CAFF70", mid_color = "#A2CD5A", high_color = "#006400")


```


Getting current layers of envioronmental parameters and linking them to biodiversity patterns
```{r, results = "hide", warning = FALSE,  message = FALSE}

oceanEnvironment(biod_grid = Anthozoa_diversity[[3]],
                             biodiv_metric = "Correced Richness", 
                             plot=T, 
                             log_scale=T)

```



Now we can use te function *oceanHotspots()* to classify biodiversity sites in high (red); mid(yellow) and low (blue) biodiversity

```{r}
par(mfrow=c(2,2))

oceanHotspots(Anthozoa_diversity[[3]], map_hotspots = T)
oceanHotspots(Bryozoa_diversity[[3]], map_hotspots = T)
oceanHotspots(Macroalgae_diversity[[3]], map_hotspots = T)
oceanHotspots(Seagrasses_diversity[[3]], map_hotspots = T)

```


Finally, the function *oceanVulnerability()* allows us to use future climate change scenarios, that can be accessed using *oceanFuture()* and explore potential climatic vulenrability of high biodiversity sites. 


```{r, results = "hide", warning = FALSE,  message = FALSE}

par(mfrow=c(2,2))


oceanVulnerability(Anthozoa_diversity[[3]], plot_histograms = F)

oceanVulnerability(Seagrasses_diversity[[3]], plot_histograms = F)


```

These maps show potential vulnerability rankings of hotspots of biodiversity in Anthozoans and Seagrasses
