---
title: Release the `blsp` R package for estimating long-term medium spatial resolution land surface phenology
date: 2022-06-06

authors:
- xiaojiegao

image:
  caption: ''
  focal_point: ''
  preview_only: false
---

Land surface phenology (LSP) is a consistent and sensitive indicator of climate change effects on Earth's vegetation, it plays an important role in tracking climate change, diagnosing agricultural management practices, and investigating ecosystem processes over large spatial scales. Existing methods of estimating LSP require time series densities that, until recently, have only been available from coarse spatial resolution imagery such as MODIS (500 m) and AVHRR (1 km). LSP products from these datasets have improved our understanding of phenological change at the global scale, especially over the MODIS era (2001-present). Nevertheless, these products may obscure important finer scale spatial patterns and longer-term changes. 

In 2021, we published [a paper in *Remote Sensing of Environment*](https://www.sciencedirect.com/science/article/pii/S0034425721002029?via%3Dihub) proposing a novel Bayesian hierarchical model (BLSP) to estimate complete annual LSP from Landsat time series over 1984-present. A plain-language description can be found on [this blog post](https://cnr.ncsu.edu/geospatial/news/2021/06/07/long-term-annual-land-surface-phenology/) and the abstract of the paper can also be found [here](https://ncsu-seal.netlify.app/publication/gao_rse_2021/).

Although we shared the R scripts online when the paper was published, we realized it might be even better for open science if we make it an R package. So, with the help of my awesome colleagues Ian McGregor, Owen Smith, Izzi Hinks, and Matt Shisler, we made it happen! The package now can be installed from our [lab Github](https://github.com/ncsuSEAL/Bayesian_LSP). We have detailed instructions on how to use the package and apply the BLSP model to any vegetation time series. We also created a Google Earth Engine script to help users try the model at their own study regions. All the information is available through running `help(package = "blsp")` once the package is installed. Check it now!


