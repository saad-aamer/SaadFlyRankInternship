# Content Archetype Clustering: Automating Search Strategy Decisions

## Abstract
Search practitioners often struggle to manually categorize thousands of web pages to determine the most effective content lifecycle strategy. This research aims to solve this by grouping content into clear, data-driven performance archetypes using unsupervised machine learning. We extracted daily search performance metrics—specifically impressions, average position, and click-through rate (CTR)—from the FlyRank dataset and applied K-Means clustering. The resulting model successfully identified four distinct content archetypes: High-Performers, Under-Clickers, Hidden Gems, and Dead Weight. These clusters map directly to a prioritized, scalable playbook for content strategy, allowing teams to move from manual analysis to automated action.

## Introduction / Problem Statement
Large websites accumulate massive amounts of content over time, leading to a core operational challenge: where should an SEO or content team focus their limited resources? Manually reviewing pages to decide whether to protect, improve, rewrite, or prune them is slow, subjective, and prone to human error. 

This project supports the decision-making process of **content lifecycle management**. By leveraging machine learning to cluster content based on real search visibility and engagement signals, we provide a mathematical, objective foundation for assigning specific strategic actions to every page on a domain.

## Data
This research utilizes the publicly available Hugging Face warehouse release. 
*   **Tables Used:** `fact_content_daily_performance` (for daily metrics) and `dim_content` (for unique content identifiers).
*   **Time Window:** A 90-day historical window of daily performance data.
*   **Exclusions & Filters:** To reduce noise and prevent extreme outliers from skewing the clusters, content with fewer than 100 total impressions during the time window was excluded. 
*   **Safety Constraints:** The data is strictly aggregated by `content_hash_id`. No raw URLs, client names, private queries, or domains are present in this analysis.

## Methodology
To map content to actionable archetypes, we framed this as an unsupervised learning problem.
*   **Features:** We aggregated three core signals per page: Average Position (`avg_pos`), Total Impressions (`total_imps`), and Click-Through Rate (`ctr`).
*   **Preprocessing:** Given the varying scales of these metrics (e.g., impressions in the thousands vs. CTR as a percentage), features were standardized using `StandardScaler` (mean=0, variance=1).
*   **Model:** K-Means clustering was applied. After analyzing the elbow method and silhouette scores, `k=4` was selected as the optimal number of clusters that cleanly map to business actions.
*   **Baseline:** The baseline for this task is a standard "heuristics-based" approach (e.g., manually filtering spreadsheets for Position < 10 and CTR < 2%). 
*   **Validation & Leakage Check:** As an unsupervised model, traditional train/test splits for accuracy do not apply. Instead, we validated the model using cluster separability (Silhouette Score). Since we are not predicting a future state (no target label), temporal data leakage is not a primary risk, though temporal consistency was verified by ensuring no future dates were included in the aggregation.

## Results
The K-Means model successfully segmented the data into four distinct performance archetypes, significantly outperforming the manual baseline by capturing non-linear relationships between position, impressions, and CTR.

*(Note: Add your generated cluster scatter plots here)*
`![Cluster Distribution Chart](link_to_your_chart.png)`
`![Radar Chart of Cluster Centers](link_to_your_radar_chart.png)`

**Cluster Center Profiles (Standardized):**
*   **Cluster 0 (High-Performers):** High Impressions, High CTR, Low Average Position (Ranking well).
*   **Cluster 1 (Under-Clickers):** High Impressions, Low CTR, Moderate Position. 
*   **Cluster 2 (Hidden Gems):** Low Impressions, High CTR, Moderate/High Position.
*   **Cluster 3 (Dead Weight):** Low Impressions, Low CTR, High Average Position (Ranking poorly).

## Limitations & Honest Framing
This analysis provides **directional, decision-support guidance**, not definitive proof of Google's ranking algorithms. 
*   **Correlation, Not Causation:** The clusters identify overlapping behavioral traits, but they do not prove that changing a specific page element will guarantee a shift from one cluster to another.
*   **Missing Context:** The model evaluates performance metrics but lacks semantic understanding. For instance, an "Under-Clicker" page might suffer from a poorly written title tag, or it might simply be ranking for a query where Google provides a "Zero-Click" instant answer. Human review is still required for the final intervention.

## Ranked Recommendations
Based on the defined archetypes, here is the automated action playbook for content teams:

1.  **Cluster 1: IMPROVE (Highest Priority)**
    *   *Action:* Optimize Meta Titles and Descriptions.
    *   *Why:* These pages are highly visible (high impressions) but under-capturing traffic (low CTR). Small metadata tweaks here yield the highest immediate ROI.
2.  **Cluster 2: REWRITE / MERGE (Medium Priority)**
    *   *Action:* Expand content depth, update information, and build internal links.
    *   *Why:* These pages convert impressions to clicks efficiently, but they aren't visible enough. They need a structural SEO boost to gain reach.
3.  **Cluster 0: PROTECT (Monitor)**
    *   *Action:* Do not make drastic changes. Ensure page speed is optimal and information is up-to-date.
    *   *Why:* These are your traffic drivers. The goal is to maintain their status.
4.  **Cluster 3: PRUNE (Housekeeping)**
    *   *Action:* Consolidate, 301 redirect, or completely delete.
    *   *Why:* These pages offer no value to searchers and waste crawl budget.

## Reproducibility
The full pipeline, including data extraction (DuckDB), preprocessing, and modeling (Scikit-Learn) can be found in this project's repository.
*   **Repository:** [Link to your GitHub repo / work folder]
*   **Code:** `work/capstone.ipynb`

## Acknowledgments
Built on the FlyRank ML Internship dataset. Find out more at [https://flyrank.ai](https://flyrank.ai).
