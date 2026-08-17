# Voice360 Demo Data Setup (Excel Fallback)

Use this guide when Dataverse is unavailable. Excel in OneDrive is the approved capstone fallback.

## Step 1: Create the workbook

1. Open OneDrive or SharePoint.
2. Create a new Excel workbook named `Voice360DemoData.xlsx`.
3. Store it in a team-controlled location (not personal OneDrive).

## Step 2: Create the Customers table

1. Rename Sheet1 to `Customers`.
2. Add the following headers in row 1:

| customer_id | first_name | last_name | policy_number | date_of_birth | registered_phone_last4 | preferred_language | policy_status |
|---|---|---|---|---|---|---|---|

3. Paste the data from `customers.csv` (rows below headers).
4. Select all data including headers → Insert → Table → check "My table has headers" → name it `Customers`.

## Step 3: Create the Claims table

1. Add a new sheet named `Claims`.
2. Add headers:

| claim_number | customer_id | policy_number | claim_type | incident_date | filed_date | status | status_explanation | missing_documents | next_action | expected_update_date | last_updated | escalation_eligible |
|---|---|---|---|---|---|---|---|---|---|---|---|---|

3. Paste data from `claims.csv`.
4. Format as a table named `Claims`.

## Step 4: Create the Callbacks table

1. Add a new sheet named `Callbacks`.
2. Add headers:

| callback_reference | customer_id | claim_number | callback_reason | preferred_date | preferred_window | masked_phone | status | created_at | correlation_id |
|---|---|---|---|---|---|---|---|---|---|

3. Paste the one example row from `callbacks.csv`.
4. Format as a table named `Callbacks`.

## Step 5: Connect to Power Automate

1. In each flow, use the **Excel Online (Business)** connector.
2. Select the workbook location, file, and table name.
3. Use **List rows present in a table** to read data.
4. Use **Add a row into a table** for callback creation.

## Important

- Do NOT share this workbook publicly or add it as Copilot Studio knowledge.
- Access it only through authenticated Power Automate flows.
- The workbook contains fictional data only.
