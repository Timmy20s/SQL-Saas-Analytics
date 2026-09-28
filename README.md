1. Does Support Load Predict Churn?
Case Study:
Evaluating Customer Support Load as a Churn Driver

Executive Summary: A core hypothesis in SaaS retention is that high customer support friction manifested through heavy ticket volume, delayed resolutions, or low satisfaction directly causes customer churn. Through a two-phase diagnostic analysis combining macro-level cohort comparisons and micro-level ticket diagnostics in SQL Server, we proved otherwise. Customer support performance and ticket load show no statistical correlation with account churn.

Phase 1 Macro-Level Cohort Analysis Hypothesis & Approach:
We began by analyzing overall support load per account to check if churned customers experienced disproportionately higher ticket volume, longer resolution wait times, or lower satisfaction scores before leaving.
Methodology Data Integration: Joined subscriptions and support tickets on account id using a LEFT JOIN to ensure accounts with zero tickets were retained.
Cohort Segmentation: Grouped the aggregated dataset by subscription status (active, churned, past due, trialing).
Metric Calculation: Calculated distinct account counts (COUNT(DISTINCT account id)), total tickets, average tickets per account, average resolution duration (AVG(resolution hours)), and average satisfaction score (AVG(satisfaction score)).

Initial Findings
Ticket Volume: Churned accounts averaged 4.47 tickets, virtually identical to active accounts (4.67 tickets).
Resolution Time: Average resolution times were nearly uniform across cohorts (~39.2 hours for churned vs. ~40.8 hours for active).CSAT Scores: Customer satisfaction scores remained consistently high across all statuses (4.07/5.00 for churned vs. 4.03/5.00 for active).

Phase 2: Deep Friction & Severity Diagnostic Why We Dug Deeper While high-level averages indicated no issue, macro metrics can easily hide critical localized blockers. A business might maintain high average satisfaction while a small subset of high-value accounts leaves due to unanswered urgent tickets or unresolved technical bugs. To eliminate false negatives, I performed a deeper diagnostic on ticket status and priority.
Methodology Conditional Aggregation: Utilized T-SQL CASE WHEN logic inside aggregate functions to segment tickets dynamically.
Backlog Analysis: Calculated the proportion of tickets that remained open/unresolved (resolved at IS NULL).Severity Weighting: Isolating high-impact operational blockers by filtering for priority = 'urgent'.
Account Penetration Rate: Evaluated the percentage of unique accounts within each status cohort that held at least one unresolved ticket.

SQL-- Core Logic Applied
COUNT (CASE WHEN st. resolved at IS NULL THEN 1 END) * 100.0 / NULLIF (COUNT (st. ticket_id), 0)
Detailed Diagnostic Results Account Status Total Accounts Total Tickets Unresolved Tickets (%) Urgent Tickets (%) Accounts with Unresolved Tickets (%) Active1,0304,8148.56%7.10%33.79%Churned863849.38%6.77%34.88%Past Due3817212.21%6.98%39.47%Trialing462306.96%3.91%34.78%Strategic Conclusion &

Takeaways
No Operational Friction Link: Unresolved tickets made up 9.38% of churned account tickets versus 8.56% for active accounts a negligible 0.82% difference. Furthermore, urgent tickets represented a lower share of tickets for churned accounts (6.77%) than for active ones (7.10%). Equal Account Exposure: Roughly 35% of accounts in both active and churned cohorts experienced at least one open support ticket, proving that unresolved issues were not disproportionately concentrated among churned clients.

The "Silent Churn" Finding: Because support quality (CSAT ~4.0) and resolution rates are healthy across all cohorts, churn is not driven by poor customer service or product bugs. Instead, the business faces Silent Churn—where disengaged customers stop using the platform and quietly cancel without ever reaching out to support.


2. What is Net revenue retention by plan tier.
NRR Analysis by Plan Tier
Every single plan tier has an NRR under 100%, meaning churn losses are outstripping expansion across the entire customer base.
Key Strategic Insights
Enterprise Leakage is the Biggest Revenue Risk: Enterprise accounts have the lowest retention rate (87.50%). Because Enterprise contracts are so large (~$144k baseline per account), losing just 1 or 2 accounts erodes hundreds of thousands of dollars in ARR.

Business Tier is Most Resilient: The Business tier retains revenue best (95.57%), suggesting the value proposition and pricing alignment fit this cohort better than the others.

Low-Tier Volume Churn: Starter has the highest account count (554) but low dollar amounts, resulting in a 91.08% NRR due to high turnover in smaller accounts.

3. Which industry has the worst satisfaction scores?
Support Diagnostic (Milestone 1 Findings)
Key Finding: Customer satisfaction (CSAT) scores across all industries are tightly clustered between 3.98 and 4.10 out of 5.00, with Logistics being the lowest (3.98) and Education the highest (4.10).

Core Insight ("Silent Churn"): Product support quality, ticket volume, and resolution time show zero correlation with customer churn. Customers are not quitting due to poor customer service or platform bugs; they are quietly leaving without filing complaints or reaching out to support.

Revenue Retention & Plan Mechanics (Milestone 2 Findings)
Every single plan tier currently operates with an NRR below the healthy 100% benchmark, indicating that expansion revenue is failing to offset churn and contraction losses across the board

Core Insight: Churn severity scales with plan complexity. Enterprise contracts carry the largest annual contract values (~$144k baseline per account), meaning losing even 1 or 2 Enterprise clients creates a massive ARR deficit that Starter/Growth tiers cannot cover.

4. How does Seat Utilization vary between started and Enterprise?
The Core Business Diagnosis ("Why Silent Churn Happens")
This completes the puzzle of why customers leave without contacting support:

The Shelf ware Trap: Enterprise clients buy massive 190+ seat packages during sales onboarding, but only deploy the platform to ~40% of their intended user base.
Value Disconnect: They aren't filing support tickets because the platform isn't broken. it's simply not being adopted by their staff.
Renewal Contraction: When renewal time arrives, finance and operations look at unused licenses, see low adoption, and quietly drop or heavily down scope the subscription.


Key Strategic Takeaways
Starter & Growth Churn is Non-Utilization Related: Notice how Starter (~59%) and Growth (~51–52%) have virtually identical utilization whether the account stayed active or churned. Small business churn is driven by budget cuts or business closure, not shelf ware.

Shelf ware Directs Enterprise & Business Churn:
In Business, utilization drops over 10 percentage points (from 42.43% down to 32.26%) prior to cancellation.

In Enterprise, utilization completely collapses—dropping 25.54 percentage points down to an abysmal 12.62%.

The Anatomy of a $150k Enterprise Loss: Look at row 4. That churned Enterprise account had 1,505 employees and bought 190 seats, but utilized only 12.62% of them (~24 active users). When renewal time came, their CFO audited software spending, saw 166 unused licenses, and canceled the contract entirely.

 
