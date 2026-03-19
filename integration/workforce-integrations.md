# Workforce Integrations

### Workforce Integrations

#### What it is

Workforce integrations connect to people, office, and commute data sources so we can quantify Scope 3 emissions driven by employee activity (remote work, commuting) and office energy use.

#### What we’re doing

* Connecting to workforce data sources (HR systems, building/meter systems, commute survey platforms)
* Fetching headcount + remote split, office energy readings, and commute behavior data
* Replacing estimates with measured data when an integration is connected
* Calculating monthly workforce emissions split into:
  * Remote work impact
  * Office energy impact
  * Commuting impact

#### Data model (what the page uses)

* Workforce config
  * Total employees
  * Remote percentage
  * Office locations: `name`, `sqm` (floor area), `country_code`
* If HR integration is connected
  * The UI uses live `last_total_employees` and `last_remote_percentage` instead of manual inputs.
* If building energy integration is connected
  * The UI can use live `last_energy_kwh` for an office (replacing area-based estimates).
* If commute survey integration is connected
  * The UI can use live commute metrics (emissions, avg distance, respondents).

***

### HR Platform Integration

Type: Headcount + remote split source\
Purpose: Keep employee totals and remote percentage accurate without manual updates.\
Connection method: Vendor API (BambooHR / Personio) or a custom REST API.

#### How it works

* User connects an HR platform.
* We ingest total employees and remote percentage (plus optional location mapping fields).
* Workforce calculations use the live values when available.

#### Supported platforms / modes

**BambooHR**

* Required fields
  * BambooHR Subdomain (`subdomain`)
  * API Key (`api_key`)
* Optional fields
  * Remote Field Name (`remote_field`, optional)
  * Location Field Name (`location_field`, optional)

**Personio**

* Required fields
  * Client ID (`client_id`)
  * Client Secret (`client_secret`)
* Optional fields
  * Remote Field Name (`remote_field`, optional)

**Custom REST API**

* Required fields
  * API URL (`api_url`)
* Optional fields
  * Bearer Token (`bearer_token`)
  * Remote Field Name (`remote_field`, optional)
  * Location Field Name (`location_field`, optional)

#### Metrics fetched (as stored/displayed)

* Total employees
* Remote percentage

***

### Building Energy Integration

Type: Office energy readings (kWh / power)\
Purpose: Replace office area-based estimates with real meter/BMS energy consumption.\
Connection method: BMS / meter REST API, Prometheus, or manual monthly kWh entry.

#### How it works

* User maps an office (name, country, optionally floor area).
* We pull or accept monthly energy consumed (kWh) for that office.
* Emissions are derived from energy consumption (instead of an area-only estimate).

#### Supported connection modes

**BMS / Smart Meter API (`rest_api`)**

* Required fields
  * BMS / Meter API URL (`bms_url`)
  * Office Name (`office_name`)
  * Country Code (`country_code`)
* Optional fields
  * BMS API Key (`bms_api_key`) or BMS API Token (`bms_api_token`) (depending on provider)
  * Floor Area m² (`square_meters`, optional)
  * Meter ID (`meter_id`, optional)

**Prometheus (`prometheus`)**

* Required fields
  * Prometheus URL (`prometheus_url`)
  * Office Name (`office_name`)
  * Country Code (`country_code`)
* Optional fields
  * Bearer Token (`bearer_token`)
  * Meter ID (`meter_id`, optional)
  * Floor Area m² (`square_meters`, optional)

**Manual kWh Entry (`manual_reading`)**

* Required fields
  * Office Name (`office_name`)
  * Country Code (`country_code`)
* Optional fields
  * Floor Area m² (`square_meters`, optional)
* Manual sync inputs (when syncing)
  * Energy Consumed (kWh) (`energy_kwh`) (required)
  * Period (days) (`period_days`) (optional)

#### Metrics fetched (as stored/displayed)

* Energy consumed (kWh)
* (Optionally) instantaneous power (watts), if provided by the source

***

### Commute Survey Integration

Type: Commute behavior and transport emissions\
Purpose: Replace average commute assumptions with actual mode split and distances.\
Connection method: Commute survey platform API or manual survey entry.

#### How it works

* User connects a commute data source (or enters survey results manually).
* We ingest respondents, average distance, and transport mode split.
* We compute commuting emissions from the collected commute patterns.

#### Supported connection modes

**Commute Platform API (`rest_api`)**

* Required fields
  * Survey API URL (`survey_api_url`)
  * API Key (`api_key`)

**Manual Survey Entry (`manual_survey`)**

* Required fields
  * None (data is entered during sync)
* Manual sync inputs (when syncing)
  * Respondents (`total_respondents`)
  * Avg Distance (km one-way) (`avg_one_way_km`)
  * Working Days / Month (`working_days_per_month`)
  * Mode split % (normalised if not exactly 100):
    * `pct_car`
    * `pct_public_transit`
    * `pct_bike`
    * `pct_train`

#### Metrics fetched (as stored/displayed)

* Commute emissions (kg)
* Avg commute distance (km)
* Respondents

***

### Emissions methodology (what the UI communicates)

* Baseline estimation (no integrations) uses fixed monthly factors:
  * Remote work: 52 kg CO₂ / employee / month
  * Commuting: 89 kg CO₂ / employee / month
  * Office energy: computed from office floor area (m²) × country factor (kg/m²/month), unless metered kWh is available
* With integrations
  * HR integration overrides headcount/remote split
  * Building energy integration overrides office energy estimate with real kWh
  * Commute survey replaces commute averages with collected commute patterns
