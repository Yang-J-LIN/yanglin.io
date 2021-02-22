---
title: "lncLocator 2.0: a cell-line-specific subcellular localization predictor for long non-coding RNAs with interpretable deep learning"
authors:
- admin
- Xiao-Yong Pan
- Hong-Bin Shen
author_notes:
date: "2021-02-21T00:00:00Z"
doi: ""

# Schedule page publish date (NOT publication's date).
publishDate: "2021-02-21T00:00:00Z"

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
publication_types: ["2"]

# Publication name and optional abbreviated publication name.
publication: "Bioinformatics"
publication_short: ""

abstract: "In this study, we present an updated cell-line-specific predictor lncLocator 2.0, which trains an end-to-end deep model per cell line, for predicting lncRNA subcellular localization from sequences.We first construct benchmark datasets of lncRNA  subcellular localizations for 15 cell lines. Then we learn word embeddings using natural language models, and these learned embeddings are fed into convolutional neural network, long short-term memory and multilayer perceptron to classify subcellular localizations. lncLocator 2.0 achieves varying effectiveness for different cell lines and demonstrates the necessity of training cell-line-specific models. Furthermore, we adopt Integrated Gradients to explain the proposed model in lncLocator 2.0, and find some potential patterns that determine the subcellular localizations of lncRNAs, suggesting that the subcellular localization of lncRNAs is linked to some specific nucleotides."

# Summary. An optional shortened abstract.
# summary: Lorem ipsum dolor sit amet, consectetur adipiscing elit. Duis posuere tellus ac convallis placerat. Proin tincidunt magna sed ex sollicitudin condimentum.

tags:
- Bioinformatics
featured: true

# links:
# - name: ""
#   url: ""
url_pdf: ''
url_code: ''
url_dataset: ''
url_poster: ''
url_project: ''
url_slides: ''
url_source: ''
url_video: ''

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
# image:
#   caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/jdD8gXaTZsc)'
#   focal_point: ""
#   preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects: []

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: ""
---

<!-- {{% callout note %}}
Click the *Cite* button above to demo the feature to enable visitors to import publication metadata into their reference management software.
{{% /callout %}}

{{% callout note %}}
Create your slides in Markdown - click the *Slides* button to check out the example.
{{% /callout %}}

Supplementary notes can be added here, including [code, math, and images](https://wowchemy.com/docs/writing-markdown-latex/). -->