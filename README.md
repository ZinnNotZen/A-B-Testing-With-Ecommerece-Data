# A-B-Testing-With-Ecommerece-Data
An end-to-end A/B testing project using real-world e-commerce behavior data to investigate whether prior brand exposure increases a consumer's likelihood of making a purchase.

## Background & Motivation
This project was built as a portfolio piece to demonstrate practical A/B testing skills using a large, real-world dataset. The core question mirrors a problem that comes up constantly in e-commerce and beyond: does familiarity drive conversion?
The Zillow analogy makes this tangible. A user who revisits the same property listing multiple times is behaving very differently from someone who scrolls past it once. Do those repeat visitors convert at a higher rate? Do they take longer to commit, or do they move faster? This project answers those same questions in an e-commerce context, where "listing" becomes "brand.

## Research Question & Hypothesis
Question: Does prior brand exposure increase a consumer's likelihood of purchasing from that brand?
Hypothesis: Users who have previously viewed or interacted with a brand (returning visitors) will convert at a higher rate than users encountering that brand for the first time (first-time visitors).
The intuition here is straightforward — if someone needs new cookware, they are far more likely to go straight to a brand they already know and trust than to spend time evaluating an unfamiliar one. Familiarity lowers the decision-making barrier.

## Data
There is no data in this github as the file was to big, please download it from kaggle its self. 
Source: eCommerce Behavior Data from Multi-Category Store — published on Kaggle by Michael Kechinov.
The original dataset contains roughly 42 million events across two months (October–November 2019) from a large multi-category online store.
Original columns:
* event_time - Timestamp of the event
* event_type - view, cart, remove_from_cart, or purchase
* product_id - Unique product identifier
* category_id - Numeric category identifier
* category_code - Human-readable category (e.g. electronics.smartphone)
* Brand - Brand name
* Price - Product price
* user_id - Permanent user identifier
* user_session - Session identifier

Columns retained for analysis: event_time, event_type, brand, user_id, user_session
Columns dropped and why:
category_code — dropped due to ~41% missing values. Rather than impute (which would risk introducing data leakage into an experiment where group integrity matters) or drop 41% of rows (which would severely hurt statistical power), the column was removed entirely since it plays no role in the analysis.
category_id — the numeric key for the same information as category_code. Dropped for the same reason.
product_id — not relevant to brand-level analysis.
price — retained initially but not used in the final analysis. A natural extension of this project would be to examine whether price moderates the brand familiarity effect.

## Methodology
**Group Assignment**
Each unique (user, brand) pair was treated as one observation. Groups were assigned at this level:
* Control — the user encountered this brand in only one session (first-time visitor)
* Treatment — the user encountered this brand across two or more sessions (returning visitor)
**Metrics**
* Primary: Purchase conversion rate — did this user-brand pair result in a purchase?
* Secondary: Sessions to first purchase — how many sessions did it take before the user bought?
**Statistical Tests**
* Two-proportion z-test (one-sided) for the primary metric. One-sided because the hypothesis is directional — we are testing whether treatment converts more, not just differently. This decision was made before examining results.
* Mann-Whitney U test (one-sided) for the secondary metric. A non-parametric test was chosen because sessions-to-purchase data is heavily right-skewed and does not meet the normality assumption required for a t-test.
**Power Analysis**
A power analysis was conducted prior to running the tests to confirm adequate sample size. Assuming a minimum detectable effect (MDE) of 2 percentage points, α = 0.05, and 80% power, the required sample size was approximately 1,044 per group. Both groups far exceeded this threshold.


## Results

<img width="713" height="439" alt="Conversion Rate by Group" src="https://github.com/user-attachments/assets/542190ad-4124-4f90-8f38-2f57a4f2d086" />

<img width="743" height="449" alt="Purchase vs Non purchase by group" src="https://github.com/user-attachments/assets/c97c019f-982c-42bb-85a4-2026cc4ac323" />

<img width="722" height="443" alt="Sessions to First Purchase" src="https://github.com/user-attachments/assets/27c81140-a40d-4ac6-ac00-b3ab14d7fd62" />

<img width="1205" height="213" alt="inital analysis" src="https://github.com/user-attachments/assets/fdc8dc7f-c10f-4270-b9a7-71d6ecea6bcb" />

<img width="1238" height="156" alt="secondary analysis " src="https://github.com/user-attachments/assets/d40369b3-76c7-46ea-98c0-f2443ea3139f" />

## Interpretation
Returning brand visitors convert at a dramatically higher rate than first-time visitors (12.91% vs 1.83%). However, they also take more sessions to get there — they are deliberate, high-intent shoppers who research carefully before committing rather than impulse buyers.
The Zillow parallel: This mirrors the behavior of serious home buyers who repeatedly revisit saved listings, check price history, and compare neighborhoods before making an inquiry — as opposed to casual browsers who scroll once and leave. The implication for re-engagement campaigns is that the goal should be nurturing high-intent users over time, not pushing for immediate conversion.

## Limitations
**Observational data & reverse causality**: This is the most important caveat. The magnitude of the lift (607%) likely reflects selection bias rather than a causal effect of brand familiarity. Returning visitors are probably higher-intent shoppers by nature — they came back because they intended to buy, not necessarily because they became more familiar with the brand. A true causal test would require randomized assignment, such as experimentally varying brand reminder touchpoints or re-engagement emails.
**Overpowered dataset**: With over 10 million user-brand pairs, even a trivially small difference would register as statistically significant. Throughout this analysis, effect size and confidence intervals were prioritized alongside p-values to distinguish statistical significance from practical significance.
**Sessions metric is partially confounded**: The treatment group has more sessions by definition (that is how they were classified as returning visitors), so the secondary metric should be interpreted cautiously.

## How to Run
**Dependencies**:
pip install pandas numpy scipy statsmodels matplotlib seaborn
File path: Update the cell with your actual file path:
df = pd.read_csv("your/path/to/2019-Oct.csv")
If you have both the October and November files, you can load both:
df = pd.concat([pd.read_csv("2019-Oct.csv"), pd.read_csv("2019-Nov.csv")])
Then run the notebook cells sequentially from top to bottom.

## Future Work
* Extend to the November file for a larger time window and seasonality check
* Subgroup analysis by category (does brand familiarity matter more for electronics vs. apparel?)
* Incorporate price as a covariate — does the familiarity effect hold across price tiers?
* Survival analysis on time-to-purchase using Kaplan-Meier curves for a more rigorous look at the sessions metric

