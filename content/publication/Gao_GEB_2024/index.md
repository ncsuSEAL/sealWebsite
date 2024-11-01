---
title: "Thermal Forcing Versus Chilling? Misspecification of Temperature Controls in Spring Phenology Models"
subtitle: 
# Authors
# If you created a profile for a user (e.g. the default `admin` user), write the username (folder name) here 
# and it will be replaced with their full name and linked to their profile.
authors:
- xiaojiegao
- Andrew D. Richardson
- Mark A. Friedl
- Minkyu Moon
- admin

# Author notes (optional)
# author_notes:
# - "Equal contribution"
# - "Equal contribution"

date: "2024-10-28"
doi: "https://doi.org/10.1111/geb.13932"



# Schedule page publish date (NOT publication's date).
publishDate: "2024-10-28"

# indicate if this paper is related to SEAL
categories: "lab-related"

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
publication_types: ["2"]

# Publication name and optional abbreviated publication name.
publication: Global Ecology and Biogeography
publication_short: GEB

abstract: We show that widely used modeling approaches that are calibrated using field-based observations misspecify the role of chilling under current climate conditions as a result of statistical artifacts inherent to the way that chilling is parameterized. Our results highlight the limitations of existing modeling approaches and observational data in quantifying how chilling affects the timing of spring leaf emergence.


# Summary. An optional shortened abstract.
summary: Modeling field phenology observations may not reveal the importance of chilling.

pub_tags: [chilling, plant phenology, phenology model, process-based model, spring leaf-emergence, thermal forcing]

# Display this page in the Featured widget?
featured: true

# Custom links (uncomment lines below)
# links:
# - name: Custom Link
#   url: http://example.org

url_pdf: 'http://doi.org/10.1111/geb.13932'
url_code: ''
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
  caption: ''
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
- chilling

---

{{% callout note %}}
<a href='https://xjgao.netlify.app/publication/2024_gao_geb/' target="_blank" rel="noopener noreferrer">Source of the original blog post!</a>
{{% /callout %}}

This paper was not in the plan.

We started from using the state-of-the-art process-based models to investigate the chilling effect on spring phenology. The original idea was that if we compare models with and without the chilling factor, we would be able to understand if chilling is an important factor in controlling spring phenology and how these chilling parameters vary among time and space. However, when analyzing phenology and temperature datasets from multiple sources and spatiotemporal scales, we found it was challenging to robustly quantify chilling by these models. Sometimes, a chilling model would outperform other non-chilling models in fitting the data and in cross-validation, but if we remove just a few data points or add a small random noise to the data, the non-chilling models would become more signficant. This result let us think that maybe chilling is a subtle process and hard to identify. But, when we digged more into the problem, we found limitations of the model structures and the observational datasets. In other words, even if chilling is an important process in the controlled experiments, calibrating these process-based models with field-collected observational data cannot robustly identify the chilling effect. 

This paper does not suggest that we should abandon process-based models, nor do we intend any disrespect to previous efforts. Instead, we aim to foster discussions and collaborations among modelers, remote sensing scientists, ecologists, plant biologists, and physiologists to better understand the role of chilling in spring leaf development by integrating controlled experiments with field observations. Specifically, address when plants start responding to temperatures, what range of temperatures influences spring phenology, and how and why these controls vary by species and/or climate conditions. This knowledge is key to understanding and modeling the chilling effect and predicting future phenological shifts driven by climate change, which could have significant ecological consequences.

As one of our reviewers said: "The field is mired in a bit of a mess and the only way out is likely through." Discussions and collaborations are critical to solving the problem.

