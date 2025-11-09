# 🧠 AM Build Optimizer: Understanding 3D Printing Quality Through Data

---

## 🧩 Overview

This project explores how **different 3D printing (Additive Manufacturing)** settings affect the **quality of metal parts** that are printed layer-by-layer using a laser and metal powder.  

When printing hundreds of small metal parts on one build plate, some of them turn out perfectly within size limits — but some become **defective (scrap)** due to temperature differences, powder reuse, or layout choices.  

The goal of this project is simple:
> **Use data to understand why defects happen and how we can minimize them.**

We’re treating this like a real industrial data challenge where the objective is to:
- Find what **process factors** cause higher scrap rates,  
- Predict which combinations of settings will produce good parts,  
- And suggest **optimal settings** for future builds.

---

## ⚙️ Background

In metal 3D printing (specifically **Laser Powder Bed Fusion – LPBF**), a laser melts thin layers of metal powder on a build plate to form parts.  
Multiple parts can be printed together on one plate under different conditions:

- **Layout** – how many parts are arranged on the plate (6×6 or 11×11 grid)  
- **Powder Type** – whether the metal powder is **Virgin (fresh)** or **Recycled (used)**  
- **Test Artifacts (TAs)** – small reference blocks printed along with the real parts to stabilize heat and track accuracy  
- **Position (Row and Column)** – where each part sits on the plate (center vs edge)

Each printed part is measured for several **dimensions**, and any part that falls outside the specified limits is considered **scrap**.

---

## 🧾 Datasets

The data used in this project comes from real pre-production builds.  
Each row in the dataset represents **one printed cup-shaped metal part**, along with all its process conditions and measurements.

### 1️⃣ Geometry & Quality Data

| Dataset | Description |
|----------|--------------|
| `AllData_PreEDM_Virgin_RowColIDs_correcteddates.csv` | Parts printed using **new (Virgin)** powder under multiple layouts |
| `AllData_PreEDM_Recycled_RowColIDs_correcteddates.csv` | Parts printed using **Recycled** powder |
| `AllData_PreEDM_Virgin_RowColIDs.csv` / `Recycled_RowColIDs.csv` | Original versions (date-uncorrected) |
| `Nonconformity` column | True/False flag — indicates whether that part failed dimensional limits |

**Columns include:**
- `PlateID` – identifier of the build (A–R)  
- `Layout` – 6×6, 6×6TA, or 11×11TA  
- `Powder` – Virgin or Recycled  
- `TA_Present` – whether test artifacts were used  
- `RowID`, `ColID` – position on plate  
- Dimensional features like:
  - `B3_DATUM_B_LOC` – Inner Diameter  
  - `B3_REF_OD` – Outer Diameter  
  - `C1_LOC_INSIDE_PLN` – Floor Height  
  - `C4_LOC_TOP_PLN` – Lip Height  
  - `B3_THICK1_WALL` … `B3_THICK4_WALL` – Lip thicknesses  

A part is considered **defective (scrap)** if *any* of its measurements fall outside the tolerance range.

---

### 2️⃣ Powder Particle-Size Distribution (PSD) Data

| Dataset | Description |
|----------|-------------|
| `virgin_volume_pt1.csv`, `virgin_volume_pt2.csv` | Particle size distribution for Virgin powder samples |
| `recycled_volume.csv` | Distribution for Recycled powder samples |

Each file records:
- `Size (µm)` – particle diameter classes  
- `Volume Density (%)` – percentage of particles of that size  

These datasets help test whether **reusing powder** changes particle size over time, which could affect print quality.

---

## 🎯 Project Goal (in plain English)

We want to answer three main questions:

1. 🧩 **Which settings make parts turn out good or bad?**  
   (layout, powder type, presence of test artifacts, or position)

2. 🤖 **Can we predict scrap rate?**  
   Build a model that tells us the probability that a new part, under given settings, will fail.

3. ⚙️ **What are the best combinations of settings?**  
   Suggest optimal recipes (like “Use virgin powder with 6×6 layout and test artifacts”) to reduce scrap.

4. 🧪 *(Bonus)* **Does reused powder behave differently?**  
   Compare particle size distributions between virgin and recycled powders.

---

## 🪜 Step-by-Step Process

### Step 1: Data Understanding & Cleaning
- Merge all virgin and recycled datasets into one combined dataset.  
- Ensure all measurements, positions, and layout details are consistent.  
- Create a new column `Scrap = 1 if Nonconformity = TRUE`.

---

### Step 2: Exploratory Data Analysis (EDA)
- Calculate **overall scrap rate**.  
- Compare scrap rate by:
  - Layout (6×6 vs 11×11)
  - Powder Type (Virgin vs Recycled)
  - TA Presence (With vs Without)
- Create heatmaps of scrap by **plate position (Row, Column)** to see if edges fail more often.
- Visualize measurement distributions for each dimension to spot bias or drift.

---

### Step 3: Statistical Modeling
- Run **ANOVA / Regression** to test which factors have significant effects on dimensional variation.
- Build a **Logistic Regression model** where:
  - Target = `Scrap`
  - Predictors = Layout, Powder, TA presence, and position.
- Interpret coefficients to understand which settings raise or lower the chance of scrap.

---

### Step 4: Optimization & Recommendations
- Use the model to simulate different combinations of settings.  
- Identify the combination that minimizes predicted scrap probability.
- Validate recommendations by comparing predicted vs observed results on unseen builds.

---

### Step 5: Particle Size Analysis (Extra Credit)
- Plot particle size curves for Virgin vs Recycled powders.
- Compute mean size and D50 (median particle diameter).
- Test whether the difference is statistically significant.
- Relate powder size shifts to scrap rate patterns.

---

## 📈 Expected Insights

From this project, we aim to discover:
- Whether **recycled powder** significantly increases the risk of scrap.  
- How **layout density (6×6 vs 11×11)** affects thermal uniformity.  
- Whether **adding test artifacts (TAs)** helps reduce build variability.  
- Which **plate locations** are more prone to dimensional errors.  

These insights can guide engineers to set up more reliable 3D printing builds and reduce costly waste.

---

## 🧠 Summary (in simple words)

This project is about **turning 3D printing into a data problem**.  
Instead of guessing which settings work best, we’re letting the data show us:
- Why some builds succeed and others fail,  
- What process choices matter the most,  
- And how we can tweak those choices to make every print better.

In short:
> We’re teaching a 3D printer how to learn from its own mistakes. 🧠🔧

