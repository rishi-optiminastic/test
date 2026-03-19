# CDN Integrations

**What it is:**\
CDN integrations connect to Content Delivery Network providers to track bandwidth usage and calculate emissions from data transfer across edge locations.

**What we're doing:**

* Fetching bandwidth metrics via CDN provider APIs
* Tracking data transfer by region/edge location
* Calculating emissions based on energy intensity of data centers
* Monitoring cache hit ratios to optimize delivery efficiency

#### Cloudflare Integration

**Provider:** Cloudflare\
**Connection Method:** API Token

**How it works:**

1. User creates an API token with Zone Analytics read permission
2. They provide the Zone ID from their domain's overview page
3. We use GraphQL Analytics API to fetch bandwidth data
4. We calculate emissions based on data transfer volume

**Required Fields:**

* API Token
* Zone ID

**Optional Fields:**

* Account ID (for multi-zone accounts)

#### AWS CloudFront Integration

**Provider:** AWS CloudFront\
**Connection Method:** IAM Role or Access Keys

**How it works:**

1. User creates IAM role with CloudFront and CloudWatch read permissions
2. They provide Distribution ID and IAM Role ARN
3. We fetch CloudWatch metrics for bandwidth and requests
4. We calculate emissions based on edge location data transfer

**Required Fields:**

* Distribution ID

**Optional Fields:**

* IAM Role ARN
* External ID
* Access Key ID / Secret Access Key

#### Akamai Integration

**Provider:** Akamai\
**Connection Method:** API Client Credentials

**How it works:**

1. User creates API client in Akamai Control Center
2. They grant Reporting API read access
3. They provide client token, secret, access token, and host
4. We query Reporting API for bandwidth and traffic data

**Required Fields:**

* Client Token
* Client Secret
* Access Token
* Host

**Optional Fields:**

* CP Code (for filtered results)

Fastly Integration

**Provider:** Fastly\
**Connection Method:** API Token

**How it works:**

1. User generates API token from Fastly account
2. They provide Service ID
3. We query Fastly Stats API for bandwidth metrics
4. We calculate emissions from data transfer

**Required Fields:**

* API Token
* Service ID

\
<br>
