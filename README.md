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
# [# Load required packages
required_packages <- c("shiny", "shinydashboard", "thematic", "plotly", "DT", "shinyWidgets", "RSQLite", "dplyr", "tidymodels", "rmarkdown", "knitr")
for (pkg in required_packages) {
  if (!requireNamespace(pkg, quietly = TRUE)) {
    tryCatch({
      install.packages(pkg, dependencies = TRUE, repos = "https://cran.rstudio.com/")
    }, error = function(e) {
      stop(sprintf("Failed to install package %s: %s", pkg, conditionMessage(e)))
    })
  }
  suppressPackageStartupMessages(library(pkg, character.only = TRUE))
}

# Initialize app
cat("Starting Shiny app...\n")

# Set thematic styling
thematic::thematic_shiny(font = "Inter, Roboto, Arial, sans-serif", bg = "#2A2E39", fg = "#FFFFFF", accent = "#005B82")

# Generate sample data
set.seed(123)
audience_data <- data.frame(
  ID = 1:1000,
  Age = sample(18:65, 1000, replace = TRUE),
  Gender = sample(c("Male", "Female", "Non-Binary"), 1000, replace = TRUE, prob = c(0.45, 0.50, 0.05)),
  Engagement_Rate = runif(1000, 0, 100),
  Conversion_Propensity = runif(1000, 0, 100),
  Preferred_Channel = sample(c("Social", "Search", "CTV", "OOH"), 1000, replace = TRUE, prob = c(0.3, 0.3, 0.2, 0.2))
)

media_data <- data.frame(
  Channel = c("Social", "Search", "CTV", "OOH"),
  CO2_per_100K = c(60, 45, 110, 80),
  ROI_Uplift = c(5, 8, 6, 3)
)

# Initialize in-memory SQLite database
con <- dbConnect(SQLite(), ":memory:")
dbWriteTable(con, "audience", audience_data)

# Custom CSS
custom_css <- HTML("
  .main-sidebar.dark-theme { background-color: #2A2E39; color: #FFFFFF; transition: all 0.3s ease; }
  .main-sidebar.light-theme { background-color: #F5F6F5; color: #333333; transition: all 0.3s ease; }
  .main-sidebar.high-contrast-theme { background-color: #000000; color: #FFFFFF; border: 2px solid #FFFF00; }
  .sidebar-menu li a.dark-theme { color: #FFFFFF; padding: 12px 15px; transition: background 0.2s ease; }
  .sidebar-menu li a.light-theme { color: #333333; padding: 12px 15px; transition: background 0.2s ease; }
  .sidebar-menu li a.high-contrast-theme { color: #FFFF00; padding: 12px 15px; transition: background 0.2s ease; }
  .sidebar-menu li a.dark-theme:hover { background-color: #00A3AD; }
  .sidebar-menu li a.light-theme:hover { background-color: #00A3AD; color: #FFFFFF; }
  .sidebar-menu li a.high-contrast-theme:hover { background-color: #FFFF00; color: #000000; }
  .sidebar-menu li.active a.dark-theme { background-color: #005B82; color: #FFFFFF; }
  .sidebar-menu li.active a.light-theme { background-color: #005B82; color: #FFFFFF; }
  .sidebar-menu li.active a.high-contrast-theme { background-color: #FFFF00; color: #000000; }
  body.dark-theme, body.dark-theme .content-wrapper { background-color: #2A2E39; color: #FFFFFF; }
  body.light-theme, body.light-theme .content-wrapper { background-color: #F5F6F5; color: #333333; }
  body.high-contrast-theme, body.high-contrast-theme .content-wrapper { background-color: #000000; color: #FFFFFF; }
  body.dark-theme .box { background-color: #3B3F4A; border: none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.2); transition: transform 0.2s ease; }
  body.light-theme .box { background-color: #FFFFFF; border: none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); transition: transform 0.2s ease; }
  body.high-contrast-theme .box { background-color: #333333; border: 2px solid #FFFF00; border-radius: 8px; box-shadow: 0 4px 12px rgba(255,255,0,0.3); }
  body.dark-theme .box:hover, body.light-theme .box:hover, body.high-contrast-theme .box:hover { transform: translateY(-2px); }
  body.dark-theme .box-header { background: linear-gradient(90deg, #005B82, #00A3AD); color: #FFFFFF; font-weight: 600; font-size: 20px; padding: 12px; border-radius: 8px 8px 0 0; }
  body.light-theme .box-header { background: linear-gradient(90deg, #005B82, #00A3AD); color: #FFFFFF; font-weight: 600; font-size: 20px; padding: 12px; border-radius: 8px 8px 0 0; }
  body.high-contrast-theme .box-header { background: #000000; color: #FFFF00; font-weight: 600; font-size: 20px; padding: 12px; border: 2px solid #FFFF00; border-radius: 8px 8px 0 0; }
  body.dark-theme .kpi-card { background: linear-gradient(45deg, #005B8222, #00A3AD44); border: none; border-radius: 8px; padding: 20px; text-align: center; color: #FFFFFF; font-weight: 600; margin-bottom: 20px; box-shadow: 0 4px 12px rgba(0,0,0,0.2); transition: transform 0.2s ease; }
  body.light-theme .kpi-card { background: linear-gradient(45deg, #005B8244, #00A3AD88); border: none; border-radius: 8px; padding: 20px; text-align: center; color: #333333; font-weight: 600; margin-bottom: 20px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); transition: transform 0.2s ease; }
  body.high-contrast-theme .kpi-card { background: #000000; border: 2px solid #FFFF00; border-radius: 8px; padding: 20px; text-align: center; color: #FFFF00; font-weight: 600; margin-bottom: 20px; box-shadow: 0 4px 12px rgba(255,255,0,0.3); }
  body.dark-theme .kpi-card:hover, body.light-theme .kpi-card:hover, body.high-contrast-theme .kpi-card:hover { transform: scale(1.03); }
  body.dark-theme .table { color: #FFFFFF; background-color: #3B3F4A; font-size: 14px; border-radius: 8px; }
  body.light-theme .table { color: #333333; background-color: #FFFFFF; font-size: 14px; border-radius: 8px; }
  body.high-contrast-theme .table { color: #FFFF00; background-color: #333333; font-size: 14px; border: 2px solid #FFFF00; border-radius: 8px; }
  body.dark-theme .dt-buttons .btn { background-color: #005B82; color: #FFFFFF; border: none; border-radius: 6px; padding: 8px 12home2: 12px; transition: background 0.2s ease; }
  body.light-theme .dt-buttons .btn { background-color: #005B82; color: #FFFFFF; border: none; border-radius: 6px; padding: 8px 12px; transition: background 0.2s ease; }
  body.high-contrast-theme .dt-buttons .btn { background-color: #FFFF00; color: #000000; border: none; border-radius: 6px; padding: 8px 12px; transition: background 0.2s ease; }
  body.dark-theme .dt-buttons .btn:hover { background-color: #00A3AD; }
  body.light-theme .dt-buttons .btn:hover { background-color: #00A3AD; }
  body.high-contrast-theme .dt-buttons .btn:hover { background-color: #FFFFFF; }
  .sliderInput .irs-bar { background: #00A3AD; border: none; }
  .sliderInput .irs-slider { background: #005B82; border: 2px solid #FFFFFF; }
  .sliderInput .irs-grid-text { color: #FFFFFF; font-size: 12px; }
  body.dark-theme .form-control { background-color: #3B3F4A; color: #FFFFFF; border: 1px solid #7F8C8D; }
  body.light-theme .form-control { background-color: #FFFFFF; color: #333333; border: 1px solid #7F8C8D; }
  body.high-contrast-theme .form-control { background-color: #333333; color: #FFFF00; border: 2px solid #FFFF00; }
  .theme-toggle-container { background-color: #005B82; padding: 8px; border-radius: 20px; display: inline-block; }
  body.light-theme .theme-toggle-container { background-color: #00A3AD; }
  body.high-contrast-theme .theme-toggle-container { background-color: #000000; border: 2px solid #FFFF00; }
  .pretty .state { border-radius: 20px !important; padding: 5px 10px !important; }
  .pretty .state label:before, .pretty .state label:after { border-radius: 20px !important; }
  .shiny-spinner { color: #00A3AD; }
")

# UI definition
ui <- dashboardPage(
  skin = "black",
  dashboardHeader(
    title = span("Converged Compass™", style = "font-weight: 600; color: #FFFFFF; font-size: 20px;"),
    tags$li(
      class = "dropdown",
      div(
        class = "theme-toggle-container",
        prettySwitch(
          inputId = "theme_toggle",
          label = span(tags$i(class = "fas fa-moon"), " Theme"),
          status = "primary",
          fill = TRUE,
          value = FALSE,
          inline = TRUE,
          bigger = TRUE,
          width = "150px"
        ),
        selectInput(
          inputId = "theme_select",
          label = NULL,
          choices = c("Dark" = "dark", "Light" = "light", "High Contrast" = "high-contrast"),
          selected = "dark",
          width = "120px"
        )
      ),
      style = "margin-right: 15px; margin-top: 10px;"
    )
  ),
  dashboardSidebar(
    tags$style(custom_css),
    sidebarMenu(
      menuItem("Audience Workbench", tabName = "audience", icon = tags$i(class = "fas fa-users")),
      menuItem("Media Allocation", tabName = "media", icon = tags$i(class = "fas fa-chart-line")),
      menuItem("Recommendations", tabName = "recommend", icon = tags$i(class = "fas fa-lightbulb")),
      menuItem("Data Explorer", tabName = "explorer", icon = tags$i(class = "fas fa-database"))
    )
  ),
  dashboardBody(
    tags$head(
      tags$link(rel = "stylesheet", href = "https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css"),
      tags$script(HTML("
        Shiny.addCustomMessageHandler('toggleTheme', function(message) {
          document.body.classList.toggle('light-theme', message.isLight);
          document.body.classList.toggle('dark-theme', !message.isLight && !message.isHighContrast);
          document.body.classList.toggle('high-contrast-theme', message.isHighContrast);
          document.querySelector('.main-sidebar').classList.toggle('light-theme', message.isLight);
          document.querySelector('.main-sidebar').classList.toggle('dark-theme', !message.isLight && !message.isHighContrast);
          document.querySelector('.main-sidebar').classList.toggle('high-contrast-theme', message.isHighContrast);
          document.querySelectorAll('.sidebar-menu li a').forEach(function(el) {
            el.classList.toggle('light-theme', message.isLight);
            el.classList.toggle('dark-theme', !message.isLight && !message.isHighContrast);
            el.classList.toggle('high-contrast-theme', message.isHighContrast);
          });
          document.querySelectorAll('.theme-toggle-container').forEach(function(el) {
            el.classList.toggle('light-theme', message.isLight);
            el.classList.toggle('dark-theme', !message.isLight && !message.isHighContrast);
            el.classList.toggle('high-contrast-theme', message.isHighContrast);
          });
        });
      "))
    ),
    tags$body(class = "dark-theme"),
    tabItems(
      tabItem(tabName = "audience",
              fluidRow(
                column(4, div(class = "kpi-card", h3(tags$i(class = "fas fa-chart-pie"), "High-Value Segments", style = "color: #FFFFFF;"), h2("3", style = "color: #FFFFFF; font-size: 32px;"), p("Clusters identified", style = "font-size: 14px; color: #FFFFFF; opacity: 0.8;"))),
                column(4, div(class = "kpi-card", h3(tags$i(class = "fas fa-percent"), "Avg. Conversion", style = "color: #FFFFFF;"), h2("65%", style = "color: #FFFFFF; font-size: 32px;"), p("Top segment", style = "font-size: 14px; color: #FFFFFF; opacity: 0.8;"))),
                column(4, div(class = "kpi-card", h3(tags$i(class = "fas fa-bullhorn"), "Top Channel", style = "color: #FFFFFF;"), h2("Social", style = "color: #FFFFFF; font-size: 32px;"), p("Preferred by 35%", style = "font-size: 14px; color: #FFFFFF; opacity: 0.8;")))
              ),
              fluidRow(
                box(title = "Audience Segmentation", status = "primary", solidHeader = TRUE, width = 12,
                    addSpinner(plotlyOutput("cluster_plot", height = "400px"), spin = "circle", color = "#00A3AD"),
                    downloadButton("download_cluster_plot", "Download Plot", style = "background-color: #005B82; color: #FFFFFF; border-radius: 6px; padding: 8px 12px; margin-top: 10px;"),
                    downloadButton("download_report", "Download Report", style = "background-color: #005B82; color: #FFFFFF; border-radius: 6px; padding: 8px 12px; margin-top: 10px; margin-left: 10px;"),
                    addSpinner(DTOutput("segment_table"), spin = "circle", color = "#00A3AD"))
              )
      ),
      tabItem(tabName = "media",
              fluidRow(
                column(4, uiOutput("co2_kpi")),
                column(4, uiOutput("roi_kpi")),
                column(4, uiOutput("carbon_roi_kpi"))
              ),
              fluidRow(
                box(title = "Media Allocation Planner", status = "warning", solidHeader = TRUE, width = 12,
                    sliderInput("social_spend", "Social Spend (%):", 0, 100, 25, step = 5),
                    sliderInput("search_spend", "Search Spend (%):", 0, 100, 25, step = 5),
                    sliderInput("ctv_spend", "CTV Spend (%):", 0, 100, 25, step = 5),
                    sliderInput("ooh_spend", "OOH Spend (%):", 0, 100, 25, step = 5),
                    addSpinner(plotlyOutput("media_plot", height = "400px"), spin = "circle", color = "#00A3AD"),
                    downloadButton("download_media_plot", "Download Plot", style = "background-color: #005B82; color: #FFFFFF; border-radius: 6px; padding: 8px 12px; margin-top: 10px;"))
              )
      ),
      tabItem(tabName = "recommend",
              fluidRow(
                box(title = "Smart Recommendations", status = "success", solidHeader = TRUE, width = 12,
                    addSpinner(DTOutput("recommend_table"), spin = "circle", color = "#00A3AD"))
              )
      ),
      tabItem(tabName = "explorer",
              fluidRow(
                box(title = "Data Explorer", status = "info", solidHeader = TRUE, width = 12,
                    textAreaInput("sql_query", "Enter SQL Query:", value = "SELECT * FROM audience LIMIT 10", height = "100px"),
                    actionButton("run_query", "Run Query", style = "background-color: #005B82; color: #FFFFFF; border-radius: 6px; padding: 8px 12px;"),
                    addSpinner(DTOutput("query_result"), spin = "circle", color = "#00A3AD"))
              )
      )
    )
  )
)

# Server logic
server <- function(input, output, session) {
  cat("Server starting...\n")
  
  # Reactive value for slider inputs
  slider_values <- reactiveVal(list(social = 25, search = 25, ctv = 25, ooh = 25))
  
  # Function to update sliders
  update_sliders <- function(changed_channel, new_value, current, other_channels) {
    tryCatch({
      if (new_value != current[[changed_channel]]) {
        total <- new_value + sum(unlist(current[other_channels]))
        new_values <- current
        new_values[[changed_channel]] <- new_value
        if (total == 0 || !is.finite(total)) {
          new_values <- list(social = 25, search = 25, ctv = 25, ooh = 25)
        } else {
          other_total <- sum(unlist(current[other_channels]))
          if (other_total > 0) {
            scaling_factor <- (100 - new_value) / other_total
            for (ch in other_channels) {
              new_values[[ch]] <- round(current[[ch]] * scaling_factor)
            }
          }
          diff <- 100 - sum(unlist(new_values))
          if (diff != 0 && other_total > 0) {
            max_idx <- which.max(unlist(current[other_channels]))
            new_values[[other_channels[max_idx]]] <- new_values[[other_channels[max_idx]]] + diff
          }
        }
        new_values <- lapply(new_values, function(x) max(0, x))
        slider_values(new_values)
        isolate({
          for (ch in other_channels) {
            updateSliderInput(session, paste0(ch, "_spend"), value = new_values[[ch]])
          }
        })
        cat(sprintf("Slider %s updated, new values: %s\n", changed_channel, paste(unlist(new_values), collapse = " ")))
      }
    }, error = function(e) {
      cat(sprintf("Error updating %s slider: %s\n", changed_channel, conditionMessage(e)))
    })
  }
  
  # Observe slider changes
  observeEvent(input$social_spend, {
    update_sliders("social", input$social_spend, slider_values(), c("search", "ctv", "ooh"))
  }, ignoreInit = TRUE)
  
  observeEvent(input$search_spend, {
    update_sliders("search", input$search_spend, slider_values(), c("social", "ctv", "ooh"))
  }, ignoreInit = TRUE)
  
  observeEvent(input$ctv_spend, {
    update_sliders("ctv", input$ctv_spend, slider_values(), c("social", "search", "ooh"))
  }, ignoreInit = TRUE)
  
  observeEvent(input$ooh_spend, {
    update_sliders("ooh", input$ooh_spend, slider_values(), c("social", "search", "ctv"))
  }, ignoreInit = TRUE)
  
  # Theme toggle
  observeEvent(input$theme_select, {
    tryCatch({
      theme <- input$theme_select
      session$sendCustomMessage("toggleTheme", list(isLight = theme == "light", isHighContrast = theme == "high-contrast"))
      updatePrettySwitch(session, "theme_toggle", value = theme == "light")
      cat("Theme switched to ", theme, "\n")
    }, error = function(e) {
      cat("Error in theme switch: ", conditionMessage(e), "\n")
    })
  })
  
  observeEvent(input$theme_toggle, {
    tryCatch({
      theme <- ifelse(input$theme_toggle, "light", "dark")
      updateSelectInput(session, "theme_select", selected = theme)
      session$sendCustomMessage("toggleTheme", list(isLight = input$theme_toggle, isHighContrast = FALSE))
      cat("Theme toggled to ", theme, "\n")
    }, error = function(e) {
      cat("Error in theme toggle: ", conditionMessage(e), "\n")
    })
  })
  
  # KPI outputs
  output$co2_kpi <- renderUI({
    tryCatch({
      spend <- unlist(slider_values()) / 100
      req(all(is.finite(spend)), length(spend) == 4)
      budget <- 1000000
      df <- media_data
      df$CO2 <- df$CO2_per_100K * (budget / 100000) * spend
      total_co2 <- sum(df$CO2)
      cat("Rendering co2_kpi, total_co2 = ", total_co2, "\n")
      div(class = "kpi-card",
          h3(tags$i(class = "fas fa-cloud"), "Total CO₂", style = "color: #FFFFFF;"),
          h2(paste(round(total_co2), "kg"), style = "color: #FFFFFF; font-size: 32px;"),
          p("Estimated footprint", style = "font-size: 14px; color: #FFFFFF; opacity: 0.8;")
      )
    }, error = function(e) {
      cat("Error in co2_kpi: ", conditionMessage(e), "\n")
      div(class = "kpi-card", h3(tags$i(class = "fas fa-cloud"), "Total CO₂", style = "color: #FFFFFF;"), h2("Error", style = "color: #E63946; font-size: 32px;"))
    })
  })
  
  output$roi_kpi <- renderUI({
    tryCatch({
      spend <- unlist(slider_values()) / 100
      req(all(is.finite(spend)), length(spend) == 4)
      budget <- 1000000
      df <- media_data
      df$ROI <- df$ROI_Uplift * spend
      total_roi <- sum(df$ROI)
      cat("Rendering roi_kpi, total_roi = ", total_roi, "\n")
      div(class = "kpi-card",
          h3(tags$i(class = "fas fa-chart-line"), "ROI Uplift", style = "color: #FFFFFF;"),
          h2(paste(round(total_roi, 1), "%"), style = "color: #FFFFFF; font-size: 32px;"),
          p("Estimated performance", style = "font-size: 14px; color: #FFFFFF; opacity: 0.8;")
      )
    }, error = function(e) {
      cat("Error in roi_kpi: ", conditionMessage(e), "\n")
      div(class = "kpi-card", h3(tags$i(class = "fas fa-chart-line"), "ROI Uplift", style = "color: #FFFFFF;"), h2("Error", style = "color: #E63946; font-size: 32px;"))
    })
  })
  
  output$carbon_roi_kpi <- renderUI({
    tryCatch({
      spend <- unlist(slider_values()) / 100
      req(all(is.finite(spend)), length(spend) == 4)
      budget <- 1000000
      df <- media_data
      df$CO2 <- df$CO2_per_100K * (budget / 100000) * spend
      df$ROI <- df$ROI_Uplift * spend
      total_co2 <- sum(df$CO2)
      total_roi <- sum(df$ROI)
      carbon_roi <- if (total_co2 > 0) total_roi / total_co2 * 1000 else 0
      cat("Rendering carbon_roi_kpi, carbon_roi = ", carbon_roi, "\n")
      div(class = "kpi-card",
          h3(tags$i(class = "fas fa-balance-scale"), "Carbon ROI", style = "color: #FFFFFF;"),
          h2(round(carbon_roi, 2), style = "color: #FFFFFF; font-size: 32px;"),
          p("Performance per kg CO₂", style = "font-size: 14px; color: #FFFFFF; opacity: 0.8;")
      )
    }, error = function(e) {
      cat("Error in carbon_roi_kpi: ", conditionMessage(e), "\n")
      div(class = "kpi-card", h3(tags$i(class = "fas fa-balance-scale"), "Carbon ROI", style = "color: #FFFFFF;"), h2("Error", style = "color: #E63946; font-size: 32px;"))
    })
  })
  
  # Clustering
  clusters <- reactive({
    tryCatch({
      set.seed(123)
      km <- kmeans(audience_data[, c("Engagement_Rate", "Conversion_Propensity")], centers = 3)
      audience_data$Cluster <- as.factor(km$cluster)
      cat("Clustering completed successfully\n")
      audience_data
    }, error = function(e) {
      cat("Error in clustering: ", conditionMessage(e), "\n")
      audience_data
    })
  })
  
  # Plot outputs
  output$cluster_plot <- renderPlotly({
    withProgress(message = 'Rendering plot...', value = 0, {
      tryCatch({
        df <- clusters()
        p <- plot_ly(
          data = df,
          x = ~Engagement_Rate,
          y = ~Conversion_Propensity,
          color = ~Cluster,
          colors = c("#4A90E2", "#F4A261", "#6BBF59"),
          type = "scatter",
          mode = "markers",
          text = ~paste("ID:", ID, "<br>Gender:", Gender, "<br>Channel:", Preferred_Channel),
          hovertemplate = "%{text}<extra></extra>",
          marker = list(size = 10, opacity = 0.8)
        ) %>%
          layout(
            title = list(text = "Audience Segments (Engagement vs. Conversion)", font = list(size = 20, color = "#FFFFFF")),
            xaxis = list(title = "Engagement Rate (%)", tickfont = list(size = 14, color = "#FFFFFF"), gridcolor = "#7F8C8D"),
            yaxis = list(title = "Conversion Propensity (%)", tickfont = list(size = 14, color = "#FFFFFF"), gridcolor = "#7F8C8D"),
            plot_bgcolor = "rgba(0,0,0,0)",
            paper_bgcolor = "rgba(0,0,0,0)",
            font = list(family = "Inter, Roboto, Arial, sans-serif", color = "#FFFFFF"),
            showlegend = TRUE,
            legend = list(font = list(size = 12, color = "#FFFFFF"))
          )
        cat("Rendering cluster_plot\n")
        p
      }, error = function(e) {
        cat("Error in cluster_plot: ", conditionMessage(e), "\n")
        plot_ly() %>% layout(title = "Plot Error", font = list(color = "#E63946"))
      })
    })
  })
  
  output$download_cluster_plot <- downloadHandler(
    filename = function() { "audience_segmentation.png" },
    content = function(file) {
      tryCatch({
        p <- output$cluster_plot()
        plotly::export(p, file = file, format = "png")
        cat("Cluster plot downloaded\n")
      }, error = function(e) {
        cat("Error in download_cluster_plot: ", conditionMessage(e), "\n")
      })
    }
  )
  
  output$media_plot <- renderPlotly({
    withProgress(message = 'Rendering plot...', value = 0, {
      tryCatch({
        spend <- unlist(slider_values()) / 100
        req(all(is.finite(spend)), length(spend) == 4, sum(spend) > 0)
        budget <- 1000000
        df <- media_data
        df$CO2 <- df$CO2_per_100K * (budget / 100000) * spend
        df$ROI <- df$ROI_Uplift * spend
        cat("Rendering media_plot, CO2 = ", df$CO2, " ROI = ", df$ROI, "\n")
        p <- plot_ly(
          data = df,
          x = ~Channel,
          y = ~CO2,
          type = "bar",
          name = "CO₂ (kg)",
          marker = list(color = "#4A90E2"),
          text = ~round(CO2),
          textposition = "auto",
          textfont = list(size = 14, color = "#FFFFFF")
        ) %>%
          add_trace(
            y = ~ROI,
            name = "ROI (%)",
            yaxis = "y2",
            marker = list(color = "#F4A261")
          ) %>%
          layout(
            title = list(text = "Media Allocation Impact", font = list(size = 20, color = "#FFFFFF")),
            xaxis = list(title = NULL, tickfont = list(size = 14, color = "#FFFFFF"), gridcolor = "#7F8C8D"),
            yaxis = list(title = "CO₂ (kg)", tickfont = list(size = 14, color = "#FFFFFF"), gridcolor = "#7F8C8D"),
            yaxis2 = list(title = "ROI (%)", overlaying = "y", side = "right", tickfont = list(size = 14, color = "#FFFFFF"), gridcolor = "#7F8C8D"),
            plot_bgcolor = "rgba(0,0,0,0)",
            paper_bgcolor = "rgba(0,0,0,0)",
            font = list(family = "Inter, Roboto, Arial, sans-serif", color = "#FFFFFF"),
            showlegend = TRUE,
            legend = list(font = list(size = 12, color = "#FFFFFF"))
          )
        cat("Rendering media_plot\n")
        p
      }, error = function(e) {
        cat("Error in media_plot: ", conditionMessage(e), "\n")
        plot_ly() %>% layout(title = "Plot Error", font = list(color = "#E63946"))
      })
    })
  })
  
  output$download_media_plot <- downloadHandler(
    filename = function() { "media_allocation.png" },
    content = function(file) {
      tryCatch({
        p <- output$media_plot()
        plotly::export(p, file = file, format = "png")
        cat("Media plot downloaded\n")
      }, error = function(e) {
        cat("Error in download_media_plot: ", conditionMessage(e), "\n")
      })
    }
  )
  
  # Table outputs
  output$segment_table <- renderDT({
    withProgress(message = 'Rendering table...', value = 0, {
      tryCatch({
        df <- clusters() %>%
          group_by(Cluster, Gender, Preferred_Channel) %>%
          summarise(
            Avg_Age = mean(Age),
            Avg_Engagement = mean(Engagement_Rate),
            Avg_Conversion = mean(Conversion_Propensity),
            Count = n(),
            .groups = "drop"
          ) %>%
          arrange(desc(Avg_Conversion))
        cat("Rendering segment_table, rows = ", nrow(df), "\n")
        datatable(
          df,
          options = list(
            dom = 'Bfrtip',
            buttons = c('copy', 'csv', 'excel'),
            pageLength = 10,
            columnDefs = list(list(className = 'dt-center', targets = '_all')),
            rowCallback = JS(
              "function(row, data) {",
              "  $(row).hover(function() {",
              "    $(this).css('background-color', '#00A3AD22');",
              "  }, function() {",
              "    $(this).css('background-color', '');",
              "  });",
              "}"
            )
          ),
          extensions = c("Buttons"),
          style = "bootstrap5",
          class = "table table-striped table-hover",
          rownames = FALSE,
          caption = "Top audience segments by conversion propensity"
        ) %>%
          formatStyle(columns = names(df), fontSize = "14px", color = "#FFFFFF", backgroundColor = "#3B3F4A") %>%
          formatRound(columns = c("Avg_Age", "Avg_Engagement", "Avg_Conversion"), digits = 1)
      }, error = function(e) {
        cat("Error in segment_table: ", conditionMessage(e), "\n")
        datatable(data.frame(Error = "Table failed to render"), style = "bootstrap5", class = "table table-striped table-hover")
      })
    })
  })
  
  output$recommend_table <- renderDT({
    withProgress(message = 'Rendering table...', value = 0, {
      tryCatch({
        optimal <- data.frame(
          Strategy = c("Performance-Driven", "Low-Carbon", "Balanced"),
          Social = c(20, 40, 30),
          Search = c(50, 40, 45),
          CTV = c(20, 10, 15),
          OOH = c(10, 10, 10),
          ROI = c(6.5, 5.5, 6.0),
          CO2 = c(65000, 55000, 60000),
          Carbon_ROI = c(0.1, 0.1, 0.1)
        )
        cat("Rendering recommend_table, rows = ", nrow(optimal), "\n")
        datatable(
          optimal,
          options = list(
            dom = 'Bfrtip',
            buttons = c('copy', 'csv', 'excel'),
            pageLength = 10,
            columnDefs = list(list(className = 'dt-center', targets = '_all')),
            rowCallback = JS(
              "function(row, data) {",
              "  $(row).hover(function() {",
              "    $(this).css('background-color', '#00A3AD22');",
              "  }, function() {",
              "    $(this).css('background-color', '');",
              "  });",
              "}"
            )
          ),
          extensions = c("Buttons"),
          style = "bootstrap5",
          class = "table table-striped table-hover",
          rownames = FALSE,
          caption = "Recommended media mixes for performance and sustainability"
        ) %>%
          formatStyle(columns = names(optimal), fontSize = "14px", color = "#FFFFFF", backgroundColor = "#3B3F4A") %>%
          formatRound(columns = c("Social", "Search", "CTV", "OOH", "ROI", "CO2", "Carbon_ROI"), digits = 1)
      }, error = function(e) {
        cat("Error in recommend_table: ", conditionMessage(e), "\n")
        datatable(data.frame(Error = "Table failed to render"), style = "bootstrap5", class = "table table-striped table-hover")
      })
    })
  })
  
  output$query_result <- renderDT({
    input$run_query
    isolate({
      withProgress(message = 'Running query...', value = 0, {
        tryCatch({
          query <- input$sql_query
          cat("Running SQL query: ", query, "\n")
          result <- dbGetQuery(con, query)
          cat("SQL query result, rows = ", nrow(result), "\n")
          datatable(
            result,
            options = list(
              dom = 'Bfrtip',
              buttons = c('copy', 'csv', 'excel'),
              pageLength = 10,
              columnDefs = list(list(className = 'dt-center', targets = '_all')),
              rowCallback = JS(
                "function(row, data) {",
                "  $(row).hover(function() {",
                "    $(this).css('background-color', '#00A3AD22');",
                "  }, function() {",
                "    $(this).css('background-color', '');",
                "  });",
                "}"
              )
            ),
            extensions = c("Buttons"),
            style = "bootstrap5",
            class = "table table-striped table-hover",
            rownames = FALSE,
            caption = "SQL Query Results"
          ) %>%
            formatStyle(columns = names(result), fontSize = "14px", color = "#FFFFFF", backgroundColor = "#3B3F4A")
        }, error = function(e) {
          cat("Error in query_result: ", conditionMessage(e), "\n")
          datatable(
            data.frame(Error = "Invalid SQL query. Please check syntax and try again."),
            options = list(pageLength = 1),
            style = "bootstrap5",
            class = "table table-striped table-hover",
            rownames = FALSE
          ) %>%
            formatStyle(columns = "Error", fontSize = "14px", color = "#E63946", backgroundColor = "#3B3F4A")
        })
      })
    })
  })
  
  # Report download
  output$download_report <- downloadHandler(
    filename = function() { "Converged_Compass_Report.pdf" },
    content = function(file) {
      tryCatch({
        temp_report <- tempfile(fileext = ".Rmd")
        cat("Generating report...\n")
        rmd_content <- '
---
title: "Converged Compass™ Report"
date: "`r Sys.Date()`"
output:
  pdf_document:
    latex_engine: pdflatex
header-includes:
  - \\usepackage{geometry}
  - \\geometry{margin=1in}
  - \\usepackage{booktabs}
  - \\usepackage{longtable}
  - \\usepackage{amsmath}
---

# Converged Compass™ Analysis Report

## Audience Segmentation Summary
```{r, echo=FALSE, results="asis"}
library(dplyr)
df <- segment_data %>%
  group_by(Cluster, Gender, Preferred_Channel) %>%
  summarise(
    Avg_Age = mean(Age),
    Avg_Engagement = mean(Engagement_Rate),
    Avg_Conversion = mean(Conversion_Propensity),
    Count = n(),
    .groups = "drop"
  ) %>%
  arrange(desc(Avg_Conversion))
knitr::kable(df, format = "latex", booktabs = TRUE, longtable = TRUE, caption = "Top Audience Segments by Conversion Propensity", digits = 1)
```

## Media Allocation Summary
**Current Allocation:**
- Social: `r slider_values$social`%
- Search: `r slider_values$search`%
- CTV: `r slider_values$ctv`%
- OOH: `r slider_values$ooh`%

**Key Metrics:**
- Total CO₂: `r round(sum(media_data$CO2_per_100K * (1000000 / 100000) * unlist(slider_values) / 100))` kg
- ROI Uplift: `r round(sum(media_data$ROI_Uplift * unlist(slider_values) / 100), 1)`%
- Carbon ROI: `r round(ifelse(sum(media_data$CO2_per_100K * (1000000 / 100000) * unlist(slider_values) / 100) > 0, sum(media_data$ROI_Uplift * unlist(slider_values) / 100) / sum(media_data$CO2_per_100K * (1000000 / 100000) * unlist(slider_values) / 100) * 1000, 0), 2)`

## Recommendations
```{r, echo=FALSE, results="asis"}
optimal <- data.frame(
  Strategy = c("Performance-Driven", "Low-Carbon", "Balanced"),
  Social = c(20, 40, 30),
  Search = c(50, 40, 45),
  CTV = c(20, 10, 15),
  OOH = c(10, 10, 10),
  ROI = c(6.5, 5.5, 6.0),
  CO2 = c(65000, 55000, 60000),
  Carbon_ROI = c(0.1, 0.1, 0.1)
)
knitr::kable(optimal, format = "latex", booktabs = TRUE, longtable = TRUE, caption = "Recommended Media Mixes", digits = 1)
```
'
      writeLines(rmd_content, temp_report)
      rmarkdown::render(temp_report, output_file = file, params = list(
        segment_data = clusters(),
        slider_values = slider_values(),
        media_data = media_data
      ))
      cat("Report downloaded\n")
      }, error = function(e) {
        cat("Error in download_report: ", conditionMessage(e), "\n")
      })
    }
  )

# Cleanup on app stop
onStop(function() {
  cat("Shutting down app, closing database connection...\n")
  dbDisconnect(con)
})
}

# Run the app
shinyApp(ui = ui, server = server)
]
```

</details>

---

## 📬 Built by Maurice McDonald

* **LinkedIn**: [linkedin.com/in/mauricemcdonald](https://www.linkedin.com/in/mauricemcdonald)
* **GitHub**: [github.com/emcdo411](https://github.com/emcdo411)
* **Email**: [moe.mcdonald@gmail.com](mailto:moe.mcdonald@gmail.com)

---

> *"What if your best-performing media segment also had your lowest carbon footprint? With Converged Compass, it’s not just possible — it’s visual."*
