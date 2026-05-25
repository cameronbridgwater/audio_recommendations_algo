# 🎵 Audio Recommendation with Lyrical Clusters

Analyzes song lyrics and metadata to discover **theme-based clusters** and recommend tracks by mood instead of just genre.  
**Core idea: use unsupervised clustering on lyrical topic scores to build a simple recommendation engine.** [file:119]

---

## 🎯 Project Overview

Built an unsupervised workflow to group songs into clusters based on **lyrical themes and song age**, then used those clusters to generate recommendations. **Key ideas:** [file:119][file:123]

- **Lyrics are features**: topic scores (e.g., violence, feelings, family, sadness) drive the clusters.  
- **Age matters**: an `age` score from 0–1 lets the model group older vs. newer songs.  
- **Clusters are human-interpretable**: examples include “life/feelings themes” and “violence-heavy”.  
- **Recommendations = “songs like this theme”**: users pick a cluster that matches their mood, and get songs from that cluster. [file:119][file:120]

---

## 📊 Data Overview

**Source**: Song dataset (1950–2019) with lyrics-derived topic scores and metadata. [file:119]

**Goal**: Discover meaningful clusters and use them to recommend songs by lyrical mood.

### 🎛 Key Columns

| Column | Type | Description | Notes |
|--------|------|-------------|-------|
| `artistname` | Categorical | Artist name | Used for interpretability only |
| `trackname` | Categorical | Song title | Used for interpretability only |
| `genre` | Categorical | Song genre | Helpful for describing clusters |
| `violence` | Numeric (0–1) | Likelihood lyrics involve violence | Key for “violence-heavy” clusters |
| `worldlife` | Numeric (0–1) | Likelihood lyrics involve life/world themes | Helps define “life/feelings” songs |
| `familygospel`, `familyspiritual` | Numeric (0–1) | Family/gospel/spiritual content | Important for family/spiritual clusters |
| `sadness`, `feelings` | Numeric (0–1) | Emotional tone of lyrics | Drives emotional / reflective clusters |
| `obscene`, `dating`, `romantic`, `communication`, etc. | Numeric (0–1) | Other lyrical themes | Capture different content dimensions |
| `age` | Numeric (0–1) | 1 = oldest, 0 = newest | Separates older vs newer songs in clusters |
| `topic` | Categorical | Overall topic label | Used mainly for interpretation | [file:119]

---

## 📁 Project Structure

Matches the final repo layout:

### 📂 `code/`

- `step1-explore.ipynb` — Lyrical EDA and topic exploration. [file:122][file:119]  
- `step2-transform.ipynb` — Cleaning, feature selection, and scaling; outputs `cleaned_features.csv`. [file:124]  
- `step3-model.ipynb` — Clustering model training, cluster profiling, and saving clustered data to `trained_with_clusters.csv` and recommendations to `recs.csv`. [file:123][file:120]

### 📂 `data/`

- `train.csv` — Original training dataset with topic scores, age, and metadata. [file:121][file:119]  
- `cleaned_features.csv` — Feature set after cleaning and preprocessing, used as input to the clustering model. [file:124]  
- `trained_with_clusters.csv` — Full training data with an added `cluster` label for each song. [file:123]  
- `recs.csv` — Sample recommendation set with `artist_name`, `track_name`, `genre`, and `cluster` for selected songs. [file:120]

### 📂 `report/`

- `step4-report.ipynb` — Written answers, cluster descriptions, and recommendation examples. [file:125]

### `README.md`

- High-level description of the project, workflow, and example recommendations (this file).

---

## 🔧 Preprocessing & Feature Choices

**Goals:** keep features that capture *themes and time*, drop fields that don’t help clustering. [file:119][file:124]

- **Dropped**: Identifier-like or unused columns that don’t describe lyrical content or age. [file:124]  
- **Kept**: Lyrical topic scores and `age` to drive theme-based clusters. [file:119][file:124]  
- **Scaling**: Standardized numeric features so that no single topic or the `age` column dominated the clustering space. [file:123]  
- **Dimensionality review**: Checked for highly correlated topic scores and considered whether to reduce or combine them for more stable clusters. [file:119][file:123]

---

## 🤖 Clustering & Recommendation Logic

| Component | Purpose | Notes |
|----------|---------|-------|
| **Clustering model (e.g., K-Means)** | Group songs into theme-based clusters | Uses topic scores + age as inputs |
| **Cluster profiling** | Summarize each cluster’s average topic scores and age | Used to name clusters (e.g., “violence-heavy”) |
| **Recommendation step** | Given a cluster, return songs from that cluster | Basis for mood-based recommendations |

**Evaluation focus:** cluster interpretability (clear themes), reasonable cluster separation, and whether clusters produce coherent recommendation sets when inspected manually. [file:119][file:123][file:125]

---

## 🎧 Example Cluster-Based Recommendations

These examples use `recs.csv`, which contains songs with their assigned cluster labels. [file:120]

### Cluster 0 – “Life/feelings themes”

```python
target_cluster = 0  # life/feelings themes
life_recs = recs[recs["cluster"] == target_cluster].copy()

life_recs[["artist_name", "track_name", "genre", "cluster_name"]].head(10)
```

Sample output includes songs like: [file:120]

- **Godsmack – “immune”** (rock)  
- **Rage Against the Machine – “pistol grip pump”** (rock)  
- **Randy Travis – “messin' with my mind”** (country)

Even though the artists and genres differ, these songs share similar patterns in life‑ and feelings‑related topic scores and song age, so the model groups them into the same “life/feelings themes” cluster. [file:119][file:120]

### Cluster 4 – “Violence-heavy”

```python
target_cluster = 4  # violence-heavy
violence_recs = recs[recs["cluster"] == target_cluster].copy()

violence_recs[["artist_name", "track_name", "genre", "cluster_name"]].head(10)
```

Sample output includes songs like: [file:120]

- **The Black Crowes – “sister luck”** (pop)  
- **Noro Morales – “silencio”** (jazz)  
- **Paramore – “playing god”** (pop)

Again, the genres are varied, but the tracks share higher violence‑related and intense thematic scores, which is what drives their assignment to this “violence-heavy” cluster. [file:119][file:120]

**Takeaway:** recommendations are built around **shared lyrical themes and age**, not just genre labels.

---

## 🛠️ Tech Stack

- **pandas** — Data manipulation  
- **scikit-learn** — Preprocessing and clustering  
- **seaborn** — Visualization  
- **matplotlib** — Charts  
- **numpy** — Numerical operations  
- **jupyter** — Notebook workflow  

---

## 🎯 Modeling Notes

1. **Interpretability first** — clusters are only useful if they map to understandable themes (e.g., “life/feelings” vs. “violence-heavy”). [file:123][file:125]  
2. **Features drive meaning** — topic scores and age determine what each cluster represents; removing noisy or redundant features helps clarify themes. [file:119][file:124][file:123]  
3. **Evaluation is qualitative and quantitative** — cluster counts and inertia matter, but so do sample songs and human descriptions of each cluster. [file:123][file:125]  
4. **Recommendations should match mood** — the system is designed for “pick a lyrical vibe, get songs with that vibe,” rather than predicting a numeric score. [file:119][file:120][file:125]

---

## 📈 Next Steps

- [ ] Add more cluster examples (e.g., family/spiritual with sadness). [file:119]  
- [ ] Experiment with alternative clustering algorithms (e.g., Gaussian Mixture, HDBSCAN). [file:119]  
- [ ] Combine lyrical clusters with acoustic features (tempo, energy) for richer recommendations. [file:119]  
- [ ] Build a simple UI or API: user picks a mood, system returns cluster-based recs. [file:119]

---

**Built by Cameron Bridgwater | The Knowledge House Data Analytics Fellow | NYC | 2026**