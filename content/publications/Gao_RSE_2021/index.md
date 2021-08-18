---
title: "Long-term, medium spatial resolution annual land surface phenology with a Bayesian hierarchical model"
subtitle: https://doi.org/10.1016/j.rse.2021.112484
# Authors
# If you created a profile for a user (e.g. the default `admin` user), write the username (folder name) here 
# and it will be replaced with their full name and linked to their profile.
authors:
- xiaojiegao
- admin
- Brian J. Reich

# Author notes (optional)
# author_notes:
# - "Equal contribution"
# - "Equal contribution"

date: "2021-08-01"
doi: "https://doi.org/10.1016/j.rse.2021.112484"

# Schedule page publish date (NOT publication's date).
publishDate: "2021-03-01"

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
publication_types: ["2"]

# Publication name and optional abbreviated publication name.
publication: Remote Sensing of Environment
publication_short: RSE

abstract: "Land surface phenology (LSP) is a consistent and sensitive indicator of climate change effects on Earth's vegetation. Existing methods of estimating LSP require time series densities that, until recently, have only been available from coarse spatial resolution imagery such as MODIS (500 m) and AVHRR (1 km). LSP products from these datasets have improved our understanding of phenological change at the global scale, especially over the MODIS era (2001-present). Nevertheless, these products may obscure important finer scale spatial patterns and longer-term changes. Therefore, we have developed a Bayesian hierarchical model to retrieve complete annual sequences of LSP from Landsat imagery (1984-present), which has medium spatial resolution (30 m) but relatively sparse temporal frequency. Our approach uses Markov Chain Monte Carlo (MCMC) sampling to quantify individual phenometric uncertainty, which is especially important when considering long time series with variable observation quality and density, but has rarely been demonstrated. The estimated spring LSP had strong agreement with ground phenology records at Harvard Forest (R2 = 0.87) and Hubbard Brook Experimental Forest (R2 = 0.67). The estimated LSP were consistent with the recently released 30 m LSP product, MSLSP30NA, in its time period of 2016 to 2018 (R2 of 0.86 and 0.73 for spring and autumn phenology, respectively). Our Bayesian hierarchical model is an important step forward in extending medium resolution LSP records back in time as it accomplishes both critical goals of retrieving annual LSP from sparse time series and accurately estimating uncertainty."

# Summary. An optional shortened abstract.
summary: We developed a new algorithm to produce long-term annual land surface phenology data at 30 m spatial reolution with pixel-wise uncertainty.

tags: [LSP, RS]

# Display this page in the Featured widget?
featured: true

# Custom links (uncomment lines below)
# links:
# - name: Custom Link
#   url: http://example.org

url_pdf: 'https://doi.org/10.1016/j.rse.2021.112484'
url_code: 'https://github.com/MrJGao/Bayesian_LSP'
url_dataset: ''
url_poster: ''
url_project: ''
url_slides: ''
url_source: ''
url_video: ''

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
# Placement options: 1 = Full column width, 2 = Out-set, 3 = Screen-width
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
# Set `preview_only` to `true` to just use the image for thumbnails.
image:
  caption: 'Estimated annual LSP with uncertainty'
  focal_point: "Smart"
  Placement: 1
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects:
- MuSLI

---