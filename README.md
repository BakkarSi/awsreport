Execute - Parameters

python generate_report.py --month 2026-04

----


Prompt library — AWS cost reporting

1. Build the full pipeline (first time setup)

Build an AWS monthly cost finance report generator in Python for a single AWS account using resource tags. The tag key is Project (case-insensitive). Map multiple raw tag aliases to canonical project names as follows:

PWCT → PWCT, Project_PWCT
KGAC → PWCT_KGAC
SCZ → PWCT_ECA, PWCT_SCEZ, PWCT_SCZ, PWCT-SCZ
EPM → PWCT-EPM, PWCT_EPM
AES Development → AES_DEV, PWCT_AES, PWCT_DEV, PWCT-AES

Untagged and unrecognised tag values go to Shared Services and are split equally across all 5 projects. Use two Cost Explorer API calls — one grouped by SERVICE only (ground truth), one grouped by SERVICE + TAG — then reconcile the gap into Shared Services so no service is ever missing. Output a formatted Excel report with: detailed breakdown sheet, summary sheet, tag alias reference sheet. Support both live AWS mode and --csv-folder test mode.


2. Add a new account column to an existing Excel report

I have an existing AWS cost Excel sheet. Add a new column called [ACCOUNT_NAME] mapping the following service costs to exact matching service names only. Do not modify any existing data, formulas, or columns. Where a service has no match, show a dash. Keep all values exactly as provided — no formulas, no calculations.
Existing file: [ATTACH YOUR XLSX FILE]
New account data:
[PASTE RAW COST DATA HERE]


3. Build the finance report from a completed multi-account Excel file

Organise the attached Excel file as a finance report for consumed AWS services by project. Shared Services costs should be split equally across all 5 projects. The Total column should show grand total per account at the bottom. Remove any duplicate service rows. Output two sheets: detailed breakdown by project, and an executive summary.
Attached file: [ATTACH YOUR XLSX FILE]


4. Add a new tag alias to the Python script

Update generate_report.py to add the following new tag alias mappings. Do not change any other logic, formatting, or existing aliases:

[PROJECT_NAME] should also match: [NEW_ALIAS_1], [NEW_ALIAS_2]

Attached file: [ATTACH generate_report.py]


5. Debug missing services in the report

My AWS cost report is showing the correct total but some services are missing from the project columns. I am running against the live Cost Explorer API. Fix the fetch function to use two API calls — one grouped by SERVICE only to get ground truth totals, one grouped by SERVICE + TAG for project allocation — then reconcile the difference into Shared Services so every service appears in the report.
Attached file: [ATTACH generate_report.py]


6. Fix resource tag columns missing from Athena CUR table

My Athena table built from CUR ZIP files has no resource_tags_* columns at all. Diagnose why and give me the exact fix, including how to verify Include resource IDs is enabled in the CUR export config, how to activate the tag in Billing cost allocation tags, and how to re-run the Glue crawler after the fix to rebuild the schema.


7. Build QuickSight project cost dashboard from CUR

Build a step-by-step guide to create a QuickSight dashboard showing monthly AWS costs by project tag from CUR data stored as ZIP files in S3. Include: Lambda to unzip and convert to GZIP, Glue crawler config, Athena views filtering out Tax/Credit/RIFee line items, QuickSight dataset setup with SPICE import, and the exact visual types and field mappings for: monthly trend by project, cost split donut, top services bar chart, MoM variance pivot table, and untagged resource table.


8. Generate Athena project cost view

Write an Athena SQL view that groups CUR data by project tag and billing month. The tag column is resource_tags_user_project. Filter out Tax, Credit, Refund, and RIFee line item types. Show untagged resources as UNTAGGED. Include columns for: billing month, project, service, account ID, total unblended cost, resource count, and tag coverage percentage.


9. IAM policy for the cost report script

Give me the minimum IAM permission policy and trust policy to run the AWS cost report Python script that calls ce:GetCostAndUsage. The script runs on a [local machine / EC2 instance / Lambda function] in a single AWS account. Do not use root access in the trust policy — scope it to the specific IAM identity that runs the script. Include an ExternalId condition and a separate policy that limits what the runner identity can do.


10. Automate monthly report with EventBridge + Step Functions

Build a Step Functions state machine triggered by EventBridge on the 3rd of each month that: runs a Lambda to unzip new CUR files from S3, waits for Glue crawler to reach READY state, triggers QuickSight SPICE refresh via API, then sends an SNS notification. Include the EventBridge cron rule, IAM roles for each step, and the state machine definition in ASL JSON.


Quick reference — tag alias format
When adding new aliases, always use this format in the prompt:
ProjectName → raw_tag_1, raw_tag_2, raw_tag_3
Matching is always case-insensitive in the script. Hyphens and underscores are treated as distinct characters — PWCT-EPM and PWCT_EPM must both be listed explicitly.


TAG_ALIASES = {
    "PWCT":            ["PWCT", "Project_PWCT"],
    "KGAC":            ["PWCT_KGAC"],
    "SCZ":             ["PWCT_ECA", "PWCT_SCEZ", "PWCT_SCZ", "PWCT-SCZ"],
    "EPM":             ["PWCT-EPM", "PWCT_EPM"],
    "AES Development": ["AES_DEV", "PWCT_AES", "PWCT_DEV", "PWCT-AES"],
}



