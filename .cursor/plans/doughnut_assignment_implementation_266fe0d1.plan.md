---
name: Doughnut Assignment Implementation
overview: Implement the full Doughnut of Social and Planetary Boundaries assignment in `assignment.ipynb`, reproducing Tables 1 and 2, trellis plots by wealth group, a sandwich plot, and an interactive doughnut plot -- all from the Fanning & Raworth (2025) dataset.
todos:
  - id: setup
    content: "Setup cell: imports (pandas, numpy, matplotlib, plotly, glob) and define base data path"
    status: pending
  - id: table1-social
    content: "Part 1: Load social CSVs, extract first/last date+value per indicator, build and style Table 1"
    status: pending
  - id: table2-ecological
    content: "Part 2: Load ecological CSVs, extract first/last date+value per indicator, build and style Table 2"
    status: pending
  - id: trellis-ecological
    content: "Part 3: Trellis plot of ecological overshoot by wealth group (B40/M40/T20) using box plots"
    status: pending
  - id: trellis-social
    content: "Part 4: Trellis plot of social shortfall by wealth group (B40/M40/T20) using box plots"
    status: pending
  - id: sandwich-plot
    content: "Part 5: Sandwich plot showing start-to-end change in overshoot/shortfall with colored bars"
    status: pending
  - id: doughnut-plot
    content: "Part 6: Interactive Plotly doughnut plot with area-proportional wedges, saved as HTML"
    status: pending
isProject: false
---

# Doughnut Assignment Implementation Plan

## Data Sources

All data lives under `a-fanning/a-fanning-doughnut-v3-a0460e5/Analysis-Final/`. We will primarily use:

- `**cleanData/soc-*.csv**` (12 files) -- social indicators with columns: `domain, dimension, group, indicator, date, populationTotal, GNIperCap, value`
- `**cleanData/eco-*.csv**` (9 files) -- ecological indicators with columns: `domain, dimension, type, group, indicator, date, populationTotal, GNIperCap, value`
- `**cleanData/001_nationalGNIperCap-Population_2000-2022_n193.csv**` -- country-level GNI, population, and wealth-group classification (`b40m40t20` column: 1=Bottom-40, 2=Middle-40, 3=Top-20)
- `**myData/4_20250108_doughnutData-boundaries.csv**` -- indicators with their boundary thresholds
- `**myData/12_20250515_globalDoughnutData_2000-2022.csv**` -- global data with boundaries and `shortfallOvershoot_pct`
- `**myData/11_20250127-grpDataAndShares.csv**` -- group-level (B40/M40/T20) data with ratios, for the trellis plots

---

## Part 1: Table 1 -- Social Foundation Indicators

**Goal:** Reproduce a styled table with columns: dimension, indicator, first date, last date, first value, last value (21 social indicators across 12 dimensions).

**Approach:**

- Load all 12 `cleanData/soc-*_clean.csv` files and concatenate them
- Filter for `group == "World"`
- For each (dimension, indicator) pair, extract the first and last available `date` and corresponding `value`
- Map indicator codes to human-readable names (matching the reference table)
- Build a MultiIndex DataFrame and style it with `pandas.Styler` to match the reference format

---

## Part 2: Table 2 -- Ecological Ceiling Indicators

**Goal:** Same format as Table 1 but for 13 ecological indicators across 9 dimensions.

**Approach:**

- Load all 9 `cleanData/eco-*_clean.csv` files and concatenate them
- Filter for `group == "World"` and `type == "global doughnut"`
- Same first/last date/value extraction
- Map indicator codes to descriptive names with boundary info from `myData/4_20250108_doughnutData-boundaries.csv`
- Style with pandas Styler

---

## Part 3: Trellis Plot -- Ecological Overshoot by Wealth Group

**Goal:** A faceted (trellis) plot showing ecological overshoot ratios for Bottom-40, Middle-40, Top-20 country groups, one panel per indicator.

**Approach:**

- Use `myData/11_20250127-grpDataAndShares.csv` which has group-level (`B40, M40, T20`) ratios by ecological indicator
- Alternatively, compute from `cleanData/` + national GNI data: classify countries by income group, aggregate indicators, compute boundary ratios
- Create a trellis of box plots (one per indicator) using matplotlib, with the three wealth groups as x-axis categories
- The reference uses box plots showing the distribution of ratios. A ratio > 1 means overshoot (shown by a horizontal reference line at ratio=1)

---

## Part 4: Trellis Plot -- Social Shortfall by Wealth Group

**Goal:** Same as Part 3 but for social shortfall indicators.

**Approach:** Analogous to Part 3 using social indicators from the group-level data. Social shortfall means the value itself (since social indicators are "% of population deprived").

---

## Part 5: Sandwich Plot

**Goal:** A horizontal bar chart showing both ecological overshoot and social shortfall side-by-side, with light red bars for improvement and dark red bars for worsening between start and end periods.

**Approach:**

- Use `myData/9_20250516_Doughnut-GlobalTablesData.csv` which has `valueStart, valueEnd, diff, betterWorse` for each indicator
- Or compute from the global doughnut data (first vs last year values)
- Social indicators on one side, ecological on the other (or stacked vertically)
- Color-code: light red = reduction in shortfall/overshoot, dark red = increase

---

## Part 6: Doughnut Plot (Interactive)

**Goal:** An interactive radial chart with ecological indicators as outer wedges and social indicators as inner wedges. Key difference from the paper: values must be **proportional to area** (not radius), and outer wedges scaled by a constant divisor to fit.

**Approach:**

- Use Plotly `go.Barpolar` to create the radial wedge chart
- For area-proportional scaling: since wedge area ~ r^2 * theta, and theta is constant per wedge, set r = sqrt(value) instead of r = value
- Ecological ceiling on the outside, social foundation on the inside
- Two time periods (start and end) shown per wedge to visualize change
- Scale outer ecological wedges by dividing by a constant (e.g., 3-5) to keep them visually balanced
- Save as `doughnut.html` and embed with `IFrame`

---

## Notebook Structure

The notebook will be organized with clear markdown sections, explanations of methodology, and inline comments only where non-obvious. Each section will:

1. Explain what is being computed and why
2. Load and process the data
3. Produce the output (table or plot)

