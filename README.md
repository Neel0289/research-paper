# Ranking Content Opportunities: A Leakage-Aware Framework for Search Intelligence & Review Prioritization

[![Paper Live](https://img.shields.io/badge/Research_Paper-Live_on_GitHub_Pages-63d8ff?style=for-the-badge&logo=github)](https://neel0289.github.io/research-paper/)
[![FlyRank ML](https://img.shields.io/badge/FlyRank-ML_Internship_Capstone-6b8cff?style=for-the-badge)](https://flyrank.ai)
[![License: MIT](https://img.shields.io/badge/License-MIT-6ee7b7?style=for-the-badge)](LICENSE)

An empirical machine learning capstone research project demonstrating a leakage-aware ranking framework for content opportunity prioritization, built on the **FlyRank Search Intelligence** dataset.

---

## 📄 Executive Summary

- **Published Research Paper:** [https://neel0289.github.io/research-paper/](https://neel0289.github.io/research-paper/)
- **Research Question:** *Can a leakage-aware machine-learning ranking model help SEO/content reviewers prioritize pages for human review using anonymized search, traffic, engagement, and content-freshness signals?*
- **Operational Decision Supported:** Which content pages should an editorial/SEO reviewer inspect first when review capacity is limited?
- **Selected Research Lane:** `CTR / Engagement Opportunity Scoring`

---

## 📊 Key Findings & Results

| Metric | Heuristic Baseline (Week 4) | Corrected Random Forest (Week 6/Final) | Directional Delta |
| :--- | :---: | :---: | :---: |
| **NDCG@20 (Held-Out Test Split)** | **`0.368183`** | **`0.857936`** | **`+0.489753`** (+133.0% relative) |
| **Validation Design** | 80/20 Grouped Client Split | 80/20 Grouped Client Split | 0 Client Overlap |
| **Evaluated Test Scope** | 4,564 pages (7 clients) | 4,564 pages (7 clients) | Strict Out-of-Sample |

> **⚠️ Required Interpretation Guardrail:**  
> These scores rank pages for human review under a constructed opportunity proxy target. They are **not** forecasts of traffic growth and do **not** establish causality.

---

## 🔍 Validation Audit & Leakage Remediation Lifecycle

```
[ Week 5 Initial Model ]  ───►  [ Week 6 Leakage Audit ]  ───►  [ Final Corrected Model ]
    NDCG@20: 0.9989                  Target Leakage Purged             NDCG@20: 0.8579
 (REJECTED AS EVIDENCE)             Independent Split Proxy          (HONEST OUT-OF-SAMPLE)
```

1. **Week 5 Model Invalidation (0.9989 NDCG@20):**  
   Initial experiments achieved an artificially high NDCG@20 of ~0.999. Rigorous validation auditing identified direct **target-feature leakage**: `engagement_rate`, `scroll_rate`, and `trend_pct` were used both to construct the target proxy and as model input features.
2. **Remediation & Governance:**  
   - Purged all target-defining components from the feature matrix.
   - Constructed the target proxy strictly independently inside train and test partitions.
   - Preserved a strict `GroupShuffleSplit` across client IDs (24 train clients, 7 test clients, 0 client overlap).
3. **Corrected Model Result (0.857936 NDCG@20):**  
   The leakage-free Random Forest regressor achieves 0.858 NDCG@20, confirming genuine ranking utility without data leakage.

---

## 📐 Methodology & Modeling Pipeline

- **Target Proxy Formulation:**  
  $$\text{Proxy Relevance} = 0.5 \times \text{pct}(\text{engagement\_rate}) + 0.2 \times \text{pct}(\text{scroll\_rate}) + 0.3 \times \text{pct}(\text{trend\_pct})$$
- **Heuristic Baseline Formulation:**  
  $$\text{Baseline Score} = 0.7 \times \text{norm}(\text{CTR gap}) + 0.3 \times \text{norm}(\log(1 + \text{search\_volume}))$$
- **Corrected Model Features (10 signals):**  
  `impressions_90d`, `clicks_90d`, `sessions_90d`, `engaged_sessions_90d`, `search_volume`, `ctr`, `avg_position`, `ai_traffic_pct`, `content_age_days`, `days_since_last_update`.
- **Model Architecture:**  
  `RandomForestRegressor(n_estimators=25, max_depth=10, min_samples_leaf=10, random_state=42)`

---

## 🎯 Content Action Playbook (Held-Out Queue of 4,564 Pages)

All ranked candidates are assigned structured diagnostic reason codes to assist editorial decisions:

| Reason Code | Count | Share | Recommended Human-Review Action |
| :--- | :---: | :---: | :--- |
| **`TREND_DECLINE`** | 2,317 | 50.8% | Review recent performance decline and check whether content needs refresh |
| **`HIGH_DEMAND_LOW_CTR`** | 1,369 | 30.0% | Review title, meta description, and search-intent alignment |
| **`STALE_REFRESH_CANDIDATE`** | 587 | 12.9% | Review content freshness, outdated information, and refresh opportunity |
| **`GENERAL_REVIEW`** | 291 | 6.4% | Perform general human review before selecting an optimization action |

### 🛑 Governance No-Go Rules
- ❌ **No automatic publishing or rewriting**
- ❌ **No automatic deletion or bulk redirection**
- ❌ **No automatic canonical or indexation modifications**
- ❌ **No guaranteed traffic or ranking claims**

---

## 📁 Repository Structure

```
research-paper/
├── work/
│   └── notebooks/
│       ├── w01_research_question.ipynb      # Week 1: Problem framing & lane definition
│       ├── w02_ml_task_framing.ipynb        # Week 2: ML task formulation
│       ├── w03_data_contract.ipynb          # Week 3: Schema & data contract
│       ├── w03_feature_leakage_check.ipynb  # Week 3: Initial feature vector & leakage check
│       ├── w04_baseline_score.ipynb         # Week 4: Heuristic baseline rule & scoring
│       ├── w04_signal_audit.ipynb           # Week 4: Distribution & signal audit
│       ├── w05_model.ipynb                  # Week 5: Initial modeling lane
│       ├── w06_validation_audit.ipynb       # Week 6: Leakage remediation & audit
│       ├── w07_action_playbook.ipynb        # Week 7: Operational action queue
│       └── capstone.ipynb                   # Capstone: Full end-to-end executable research
├── submission/
│   └── paper_url.txt                        # Exactly one line with public paper URL
├── index.html                               # Production research paper (GitHub Pages)
├── ResearchPaper.html                       # Standalone copy of research paper
├── README.md                                # Project documentation & audit log
└── assets/                                  # Research figures & visualization artifacts
    ├── baseline_vs_model.png
    ├── feature_importance.png
    ├── reason_code_distribution.png
    └── validation_audit.png
```

---

## 🔒 Public Safety & Privacy Compliance

This repository strictly adheres to data protection and public-safety requirements:
- **Zero Sensitive Entities:** No client names, private brand domains, URLs, search queries, or internal credentials exist in the codebase or paper.
- **Data Hygiene:** Raw dataset files (`*.csv`, `*.parquet`, etc.) are ignored via `.gitignore` and excluded from Git commits.
- **Anonymized Identifiers:** All content items are indexed exclusively by anonymized hash tokens (e.g., `content_b954bc4acaad`).

---

## 🚀 Local Reproduction & Execution

1. **Clone repository:**
   ```bash
   git clone https://github.com/Neel0289/research-paper.git
   cd research-paper
   ```

2. **Environment setup:**
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # On Windows: .venv\Scripts\activate
   pip install pandas numpy scikit-learn matplotlib
   ```

3. **Run Capstone Analysis:**
   Place `content_refresh_anonymized.csv` in `work/notebooks/` and execute `capstone.ipynb` top to bottom.

---

## 🏆 Acknowledgments & Data Credit

Built on the **[FlyRank ML Internship](https://flyrank.ai)** dataset.
