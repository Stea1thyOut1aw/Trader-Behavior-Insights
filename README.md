# Trader-Behavior-Insights
This repository contains data and notebook analyzes the relationship between trading performance from a Hyperliquid dataset and the cryptocurrency Fear &amp; Greed Index. The analysis is performed in several steps, focusing on data loading, preprocessing, merging, aggregation, statistical testing, and visualization.

# 1. Data Loading and Initial Setup:
Necessary libraries for data manipulation, analysis, and visualization are imported, including pandas, numpy, matplotlib, and seaborn.
The scikit-posthocs library is installed for post-hoc analysis.
The Hyperliquid historical data (historical_data.csv) is loaded into a pandas DataFrame named hd.
The Fear & Greed Index data (fear_greed_index.csv) is loaded into a pandas DataFrame named fgp.

# 2. Data Preprocessing and Merging:
Copies of the loaded dataframes (hd and fgp) are created as hl and fg respectively.
Column names in both dataframes are standardized to lowercase with underscores for consistency.
Specific columns in the hl dataframe are renamed to ensure consistent naming (e.g., 'coin' to 'symbol', 'timestamp ist' to 'time', 'closed_pnl' to 'closedpnl').
The 'time' column in the hl dataframe is converted to datetime objects and filtered for valid dates. A 'trade_date' column (UTC date) is created for merging.
If an 'event' column exists in the hl dataframe, the data is filtered to keep only executed trades/fills.
A 'size' column is created in hl, prioritizing 'size_tokens' and falling back to 'size_usd'.
Numeric columns in hl ('execution_price', 'closedpnl', 'start_position') are converted to numeric types. The 'leverage' column was not present and was excluded from analysis.
The 'side' column is normalized, and a binary 'side_binary' column is created (1 for Buy, 0 for Sell).
The 'date' column in the fg dataframe is converted to datetime objects and then to UTC dates.
The primary classification column in fg is identified and standardized.
The 'classification' column in fg is converted to an ordered categorical type with scores assigned based on the fear and greed levels.
Duplicate entries in fg based on date are removed, keeping the latest entry for each date.
The hl and fg dataframes are merged based on the 'trade_date' and 'date' columns, respectively.

# 3. Trade-Level Features and Aggregation:
A 'pnl' column is created in the merged dataframe from 'closedpnl'.
A binary 'pnl_bin' column is created indicating whether a trade was profitable.
A 'norm_pnl' column is calculated by normalizing 'pnl' by the absolute trade size.
The merged data is grouped by 'account', 'trade_date', 'classification', and 'sentiment_score' to create an account-day aggregated dataframe (agg).
Various metrics are aggregated at the account-day level, including total trades, total PnL, profit rate, median trade PnL, tail risk (5th percentile PnL), total and average trade size, buy ratio, and number of unique symbols traded.
A Herfindahl index for symbol concentration is calculated for each account-day.
The aggregated metrics and symbol concentration are merged into a panel dataframe (panel).

# 4. Descriptive Analysis and Visualization:
The panel dataframe is grouped by 'classification' to calculate descriptive statistics for each sentiment class (by_sent). These statistics include the number of unique accounts and days, median total PnL, interquartile range of total PnL, median profit rate, median normalized trade PnL, median buy ratio, and median total trades.
Box plots are generated to visualize the distribution of 'total_pnl', 'profit_rate', and 'avg_trade_size' across the different sentiment classifications.

# 5. Statistical Testing:
Kruskal–Wallis tests are performed on 'profit_rate' and 'total_pnl' to determine if there are statistically significant differences across the sentiment classifications.
Dunn's post-hoc tests with Bonferroni correction are conducted on 'profit_rate' and 'total_pnl' to identify which specific sentiment class pairs have statistically significant differences.

# 6. Logistic Regression Analysis:
A logistic regression model is built at the trade level to predict the probability of a profitable trade ('pnl_bin') based on 'sentiment_score', 'size_scaled', and 'side_binary'. The 'pnl_bin' column is recreated within this cell to ensure its availability.

# 7. Market-Wide Aggregates and Participation:
Market-wide aggregates per day are calculated from the merged dataframe, including unique accounts, total turnover (absolute sum of size), and total trades, grouped by 'trade_date' and 'classification'. The 'pnl' column is recreated within this cell for this aggregation.
A line plot is generated to visualize the number of unique accounts participating each day, colored by the sentiment classification.

# 8. Data Export:
The processed dataframes (merged, panel, and by_sent) are saved to CSV files for further use or analysis.
The filenames of the saved data and generated figures are printed.
Overall, this notebook provides a comprehensive analysis of how the Fear & Greed Index relates to various aspects of trading performance at both the account-day and market-wide levels, employing descriptive statistics, visualizations, non-parametric statistical tests, and a logistic regression model.
