# Analytics
Project Overview:

This project focuses on calculating and analyzing key account and email metrics with respect to various grouping fields such as date, country, send_interval, is_verified, and is_unsubscribed. The metrics are calculated separately for accounts and emails to avoid conflicts arising from different logic involving the date field. The project uses SQL with Common Table Expressions (CTEs) and window functions to calculate country-level rankings and aggregate values for key metrics, followed by combining results using a UNION operation.

Key Metrics Calculated:

The project calculates the following metrics both for accounts and emails:

Account Metrics:

account_cnt: Total number of created accounts.

sent_msg: Total number of emails sent.

open_msg: Total number of emails opened.

visit_msg: Total number of email link clicks.

Email Metrics:

sent_msg: Total number of emails sent.

open_msg: Total number of emails opened.

visit_msg: Total number of email link clicks.

Additional Country-Level Metrics:

total_country_account_cnt: Total number of created subscribers per country.

total_country_sent_cnt: Total number of emails sent per country.

Country Ranking Metrics:

rank_total_country_account_cnt: Country rank by total number of created subscribers.

rank_total_country_sent_cnt: Country rank by total number of emails sent.

Grouping Fields:

The metrics are grouped by the following fields:

date — Date (for accounts: account creation date; for emails: email send date).

country — Country.

send_interval — Interval for sending set by the account.

is_verified — Whether the account is verified.

is_unsubscribed — Whether the subscriber has unsubscribed.

SQL Query Approach:

The SQL query calculates account and email metrics separately and groups the results based on the aforementioned fields. The query utilizes Common Table Expressions (CTEs) to logically separate the calculations for different metrics. Window functions are used to calculate the rankings for countries based on the number of accounts and emails. The results are then combined using a UNION operation, ensuring the separation of account and email metrics, and filtered to include only the top 10 countries based on the rankings.

Final Output Columns:

The query outputs the following columns for each record:

date — Date.

country — Country.

send_interval — Sending interval.

is_verified — Verified account status.

is_unsubscribed — Unsubscribed status.

account_cnt — Number of created accounts.

sent_msg — Number of sent emails.

open_msg — Number of opened emails.

visit_msg — Number of email link clicks.

total_country_account_cnt — Total number of subscribers by country.

total_country_sent_cnt — Total number of sent emails by country.

rank_total_country_account_cnt — Ranking of countries by the number of created accounts.

rank_total_country_sent_cnt — Ranking of countries by the number of sent emails.













