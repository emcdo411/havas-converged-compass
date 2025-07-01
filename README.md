# havas-converged-compass

> GitHub-worthy RShiny app combining audience targeting and carbon-conscious media planning for Havas Media Group

![RStudio](https://img.shields.io/badge/Platform-RStudio-blue?style=for-the-badge)
![Shiny](https://img.shields.io/badge/Built_with-Shiny-005B82?style=for-the-badge)
![SQLite](https://img.shields.io/badge/Backend-SQLite-003B57?style=for-the-badge)
![Tidymodels](https://img.shields.io/badge/ML-Tidymodels-6BBF59?style=for-the-badge)
![Plotly](https://img.shields.io/badge/Visualization-Plotly-F4A261?style=for-the-badge)
![DT](https://img.shields.io/badge/DataTables-DT-2A2E39?style=for-the-badge)
![Dplyr](https://img.shields.io/badge/Data_Manipulation-dplyr-7F8C8D?style=for-the-badge)
![Thematic](https://img.shields.io/badge/Styling-thematic-00A3AD?style=for-the-badge)
![ShinyWidgets](https://img.shields.io/badge/Widgets-shinyWidgets-FFC300?style=for-the-badge)

---

## 🔎 Executive Summary

**Converged Compass** is a next-gen RShiny application purpose-built for Havas Media Group. It empowers both technical and non-technical teams to:

* Analyze and cluster audiences with ML techniques
* Optimize media budget allocations
* Visualize tradeoffs between performance and carbon impact
* Simulate strategy outcomes and extract SQL insights — no code required

Designed with a sleek PowerBI-style interface, this app is a showcase of **data storytelling, audience intelligence, and ESG-aware planning** for enterprise media teams.

---

## 🧠 Project Highlights

* 🎯 **Audience Clustering Workbench**: Uses `tidymodels` to segment 1000+ synthetic audience records by engagement + conversion propensity.
* 📊 **Media Allocation Simulation**: Interactive sliders adjust budget mix across Social, Search, CTV, and OOH, with real-time bar graphs reflecting ROI uplift and kg CO₂.
* ⚡ **Carbon ROI KPI**: New smart metric: performance per unit of carbon output.
* 🧾 **Smart SQL Explorer**: A non-technical interface to write and execute SQL against an in-memory SQLite database.
* 🧪 **REST API Ready**: Includes documented example for `plumber`-based API integration.

---

## 🏗️ Architecture Overview

```
+------------------+       +-------------------------+
|   RShiny Front   | <---> |   SQLite / API Layer    |
| UI, Sliders, Viz |       | Query, Storage, ML      |
+------------------+       +-------------------------+
```

> Optional upgrade: Replace SQLite with GCP-hosted PostgreSQL via plumber API for scale + CI/CD

---

## 📦 Tech Stack

Each component below is presented as its own colorful badge to highlight its role:

* ![RStudio](https://img.shields.io/badge/Platform-RStudio-blue?style=for-the-badge)
* ![Shiny](https://img.shields.io/badge/Built_with-Shiny-005B82?style=for-the-badge)
* ![SQLite](https://img.shields.io/badge/Backend-SQLite-003B57?style=for-the-badge)
* ![Tidymodels](https://img.shields.io/badge/ML-Tidymodels-6BBF59?style=for-the-badge)
* ![Plotly](https://img.shields.io/badge/Visualization-Plotly-F4A261?style=for-the-badge)
* ![DT](https://img.shields.io/badge/DataTables-DT-2A2E39?style=for-the-badge)
* ![Dplyr](https://img.shields.io/badge/Data_Manipulation-dplyr-7F8C8D?style=for-the-badge)
* ![Thematic](https://img.shields.io/badge/Styling-thematic-00A3AD?style=for-the-badge)
* ![ShinyWidgets](https://img.shields.io/badge/Widgets-shinyWidgets-FFC300?style=for-the-badge)

---

## 📂 Folder Structure (recommended)

```
havas-converged-compass/
├── app.R
├── data/
│   └── sample_audience.csv
├── api/
│   └── plumber.R (future-ready REST API)
├── README.md
└── www/
    └── style.css (PowerBI-inspired theme)
```

---

## 🚀 Run Locally

```R
# Install dependencies
install.packages(c("shiny", "shinydashboard", "thematic", "plotly", "DT", "shinyWidgets", "RSQLite", "dplyr", "tidymodels"))

# Run app
shiny::runApp("app.R")
```

---

## 🤖 Why This Matters

Havas Media’s Converged team thrives on smart, scalable insights. This project:

* Makes **audience intelligence and ESG** operational
* Supports **AI-ready media strategy** that’s explainable and traceable
* Bridges gaps between **media buyers, data scientists, and C-levels**

---

## 🧩 Full Code for Duplication

> Below is the complete code block for `app.R` — ready to copy and paste directly into RStudio.

<details>
<summary><strong>Click to Expand Full Application Code</strong></summary>

```r
# [Insert your full app.R code here exactly as shown in your latest working version]
```

</details>

---

## 📬 Built by Maurice McDonald

* **LinkedIn**: [linkedin.com/in/mauricemcdonald](https://www.linkedin.com/in/mauricemcdonald)
* **GitHub**: [github.com/emcdo411](https://github.com/emcdo411)
* **Email**: [moe.mcdonald@gmail.com](mailto:moe.mcdonald@gmail.com)

---

> *"What if your best-performing media segment also had your lowest carbon footprint? With Converged Compass, it’s not just possible — it’s visual."*
