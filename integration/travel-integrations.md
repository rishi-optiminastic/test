# Travel Integrations

### Travel Integrations

#### What it is

Travel integrations connect to expense, booking, and calendar systems to automatically import business travel activity and calculate Scope 3 emissions for flights, ground transport, and accommodation.

#### What we’re doing

* Connecting to travel data sources (expense platforms, TMC/booking tools, calendars)
* Syncing trips and/or travel spend for the selected month/year
* Normalising travel into trips (flight / rail / road / accommodation)
* Calculating emissions using distance-based and night-based factors (with radiative forcing for flights)
* Supporting multiple connection methods (OAuth2, API keys, generic REST, and manual submission)

***

### Integration types (sources we support)

#### 1) Expense Platform Integration

Type: Expense system\
Purpose: Pull travel-related spend and translate to trip activity where possible.\
Connection method: OAuth2 client credentials or API key, depending on provider.

Supported platforms

* SAP Concur (`concur`)
* Brex (`brex`)
* Navan / TripActions (`navan`)
* Generic REST (`generic_rest`)

Required fields (by platform)

* SAP Concur
  * Client ID (`client_id`)
  * Client Secret (`client_secret`)
* Brex
  * API Key (`api_key`)
* Navan
  * API Key (`api_key`)
* Generic REST
  * API Endpoint URL (`api_url`)

Optional fields (Generic REST)

* Bearer Token (`bearer_token`)
* API Key (`api_key`) (alternate auth)

***

#### 2) TMC / Booking Tool Integration

Type: Travel Management Company / booking tool\
Purpose: Pull booked itineraries (usually the most “trip-accurate” source).\
Connection method: OAuth2 client credentials or API key.

Supported platforms

* Egencia / Amex GBT (`egencia`)
* CWT (Carlson Wagonlit) (`cwt`)
* Generic REST (`generic_rest`)

Required fields (by platform)

* Egencia
  * Client ID (`client_id`)
  * Client Secret (`client_secret`)
* CWT
  * API Key (`api_key`)
* Generic REST
  * API Endpoint URL (`api_url`)

Optional fields (Generic REST)

* Bearer Token (`bearer_token`)
* API Key (`api_key`)

***

#### 3) Calendar Travel Integration

Type: Calendar inference\
Purpose: Infer travel from calendar events (useful when booking/expense tools aren’t available).\
Connection method: OAuth2 (Google/Microsoft) or manual entry.

Supported platforms

* Google Calendar (`google`)
* Microsoft Outlook (`outlook`)
* Manual Entry (`manual`)

Required fields (by platform)

* Google Calendar
  * Client ID (`client_id`)
  * Client Secret (`client_secret`)
  * OAuth2 Refresh Token (`refresh_token`)
* Outlook
  * Azure Tenant ID (`tenant_id`)
  * Client ID (`client_id`)
  * Client Secret (`client_secret`)
* Manual Entry
  * None (trip data is provided at sync time)

Optional fields

* Google
  * Calendar ID (`calendar_id`)
  * Service Account JSON (`service_account_json`)
* Outlook
  * User Email (`user_email`)

***

### How syncing works

* User adds an integration (type + platform + credentials).
* Connection is verified at setup time.
* On Sync (or Sync All) for a given month/year, we fetch and store:
  * Trip count
  * Total distance (km)
  * Flight count
  * Hotel nights
  * Total emissions (kg CO₂e)
* The UI surfaces these as “last sync” metrics on each integration record.

***

### Emissions methodology (as implemented in the travel page)

#### Travel activity we model

* Flights: distance-based, by class and domestic vs international
* Rail: distance-based (electric vs diesel)
* Road: distance-based (petrol/diesel/hybrid/electric + bus/taxi options)
* Accommodation: night-based (budget/average/luxury)

#### Key calculation rules

* Flights apply radiative forcing multiplier: RF × 1.9
* Factors are applied as:
  * Flight: ( \text{km} \times \text{kg/km factor} \times \text{passengers} \times 1.9 )
  * Rail/Road: ( \text{km} \times \text{kg/km factor} \times \text{passengers} )
  * Hotel: ( \text{nights} \times \text{kg/night factor} )

#### Example factors used (high level)

* Domestic flight: 0.255 kg/km (then ×1.9 RF)
* Intl economy flight: 0.195 kg/km (then ×1.9 RF)
* Electric rail: 0.041 kg/km
* Petrol car: 0.192 kg/km
* Average hotel: 30 kg/night

***

### Metrics stored/displayed per integration

* Trip count (`last_trip_count`)
* Total distance km (`last_total_distance_km`)
* Total emissions kg CO₂e (`last_total_emissions_kg`)
* Flight count (`last_flight_count`)
* Hotel nights (`last_hotel_nights`)
* Connection status + last sync timestamp/error
