---
title: SEAL Updates
date: 2022-05-06

authors:
- ianmcgregor

image:
  caption: ''
  focal_point: ''
  preview_only: true
---

Hard to believe that in just a week, we will be finished with the Spring term here in warm Raleigh, with all the trees fully-leafed out and the dread of the incoming mid-summer humidity slowly starting to settle. But that's ok! We here in SEAL are adaptive if nothing else, and this is clearly evident in how each of our research has progressed over the past few months. Though we all have our community together in this lab, our work is on pretty different trajectories and supports an interdisciplinary learning environment. So! To give y'all an indication of what we're currently up to, here are some small updates from lab members.

**MUTATED**
Our first research updates come from members of the [MUTATED](https://ncsu-seal.netlify.app/project/mutated/) project, which involve optimization of change detection algorithms at high resolution. However, each project member also has their own dissertation research they are working toward.

Jenna
- This past semester I was able to submit a research proposal for the NASA FINESST grant. Through this writing process, I developed a research idea focused on mapping daily inundation probabilities in a coastal wetland environment and relating inundation events to their associated methane fluxes. This research proposes a variety of techniques that I’ve spent the semester getting familiar with including multi-sensor data assimilation, ecological forecasting with Bayesian statistics, and biogeochemical modeling. For the MUTATED research project, I’ve been working on developing a variety of spectral-based filters that aid in classifying areas of heavy construction from other types of change.  Together with Laura and Owen I’ve been working on fine tuning the roboBayes algorithm and was excited to travel and present at the IARPA site meeting in Washington, DC at the end of April!

{{< figure src="updateJenna.png" caption="This image shows for two construction change sites (annotated in red) how patterns emerge in the spectral model values for identified change pixels. This information was used to build parameter filters targeted at classifying construction from other types of change." >}}

Owen
- With computational gains in mind, I have been exploring adaptive resolution file structures/representations for raster datasets, such as the Adaptive Particle Representation found in Microscopy domain and general mesh data structures. I hope that this will allow us to reduce the computational footprint of our data while increasing our ability to perform analysis at scale. An additional thrust of my research has involved the early generation of ideas for hybrid spectral/spatial change detection systems utilizing adaptive sampling. My hope is this will help us obtain solely what we need from a scene instead of downloading everything first. Lastly, my role as a part of the MUTATED team has seen my attention focused on data engineering and writing the code needed for us to create the datacubes we need on our end to test and develop our algorithms.

Laura
- This semester, I am incorporating spatial characteristics into the robust online Bayesian (roboBayes; Mr. Rob O'Bayes) monitoring framework. I transformed gapless Planet Fusion Monitoring data for a region in Jacksonville, FL using a wavelet representation and monitored the contributions of multiple resolutions to the images over time to identify new construction. Spatial coherence of a changing region, as well as the change of a region relative to its surroundings, lends strength to detection that is unavailable from single pixel models. Moving forward, I will apply this approach to different terrains and characterize sensitivity to spatial and temporal resolution.

{{< figure src="updateLaura.png" caption="Change detected in multiple resolutions over Jacksonville, FL" >}}

**Phenology**
Xiaojie
- This semester, I’m working on testing the chilling hypothesis that plants need to experience sufficiently low temperatures before breaking dormancy. I applied several process-based spring phenology models to phenological data from satellite remote sensing, ground observations, and their corresponding temperature records. I've found that there might be some potential issues in these models that prevent us from drawing inferences from their parameters. For my next step, I'm going to consider some better ways to test chilling.

**Agriculture and phenology**
Izzi
- This term, Izzi has developed and compared several augmentation approaches to explore how minimal amounts of well-selected commercial imagery, identified beforehand, can be used to augment publicly available time series. In this process, she as also fine-tuned algorithms to detect and remove outliers in time series of remotely sensed data. This work has been applied to monitor smallholder crop development across space and time, to ultimately quantify their adaptive potential. Outside of research, Izzi has enjoyed taking on the role as Co-President of the Geospatial Graduate Student Organization to serve and advocate for graduate students in the Center for Geospatial Analytics.

**Deforestation and change detection**
Ian
- Over the course of this term, I've made good progress on my goal to improve change detection of deforestation. Specifically, I finalized my landscape application code for applying my algorithm to the entirety of a protected area in Myanmar at 10m resolution. I've also improved my training code bit by bit by changing aspects of a spike filter, determining if Sentinel-1 is worth keeping for timeseries analysis, and comparing moving average methods. For the summer I hope to finally finish my first chapter and submit a manuscript. Otherwise, I've been busy finishing up my time as the Geospatial Graduate Student Organization secretary and coordinating the college's graduate Diversity, Equity, and Inclusion committee.

{{< figure src="updateIan.png" caption="Comparisons of different moving average methods for detecting change" >}}
