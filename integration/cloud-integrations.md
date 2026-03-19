# Cloud Integrations

**What it is:**\
Cloud integrations connect to major cloud providers (AWS, GCP, Azure) to automatically fetch compute usage, storage, and networking data, then calculate the associated carbon emissions.

**What we're doing:**

* Setting up secure API connections using IAM roles or service accounts
* Fetching usage data from cost management and carbon footprint APIs
* Applying region-specific emission factors to calculate CO2e
* Supporting both real-time API connections and CSV upload fallbacks

#### AWS Integration

**Provider:** Amazon Web Services\
**Connection Method:** IAM Cross-Account Role with External ID

**How it works:**

1. User creates an IAM role in their AWS account with read-only permissions
2. They provide the Role ARN to CarbonCut
3. We assume that role using AWS STS to fetch data
4. We query AWS Cost Explorer and Carbon Footprint Tool APIs
5. We calculate emissions based on usage and AWS region grid intensity

**Required Permissions:**

* [ce:GetCostAndUsage](https://vscode-file/vscode-app/Users/rishi/Downloads/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) - for cost/usage data
* [ce:GetCostForecast](https://vscode-file/vscode-app/Users/rishi/Downloads/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html) - for projections
* `sustainability:GetCarbonFootprintSummary` - for carbon data (where available)

#### GCP Integration

**Provider:** Google Cloud Platform\
**Connection Method:** Service Account Key (JSON)

**How it works:**

1. User creates a service account with BigQuery read permissions
2. They enable Carbon Footprint export in Cloud Console
3. They download and upload the JSON key file
4. We use the key to authenticate with Google Cloud APIs
5. We query BigQuery for carbon footprint data exported by GCP

**Required Permissions:**

* [bigquery.datasets.get](https://vscode-file/vscode-app/Users/rishi/Downloads/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html)
* `bigquery.tables.getData`
* [bigquery.jobs.create](https://vscode-file/vscode-app/Users/rishi/Downloads/Visual%20Studio%20Code.app/Contents/Resources/app/out/vs/code/electron-browser/workbench/workbench.html)

**Required Fields:**

* Project ID
* Service Account Key (JSON file)

#### Azure Integration

**Provider:** Microsoft Azure\
**Connection Method:** App Registration with OAuth

**How it works:**

1. User creates an App Registration in Azure Portal
2. They create a client secret for the app
3. They assign "Cost Management Reader" role to the app at subscription level
4. They provide Tenant ID, Subscription ID, Client ID, and Client Secret
5. We authenticate using OAuth 2.0 client credentials flow
6. We query Azure Carbon Optimization API and Cost Management API

**Required Permissions:**

* `Microsoft.CarbonOptimization/carbonEmissionReports/action`
* `Microsoft.CostManagement/query/action`

**Required Fields:**

* Tenant ID
* Subscription ID
* Application (Client) ID
* Client Secret
