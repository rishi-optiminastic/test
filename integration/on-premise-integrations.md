# On-Premise Integrations

**What it is:**\
On-premise integrations connect to monitoring systems for physical servers in data centers, enabling real-time tracking of energy consumption and hardware metrics.

**What we're doing:**

* Connecting to server monitoring tools (Prometheus, IPMI, PDU)
* Fetching CPU utilization, power consumption, and temperature data
* Calculating emissions based on energy usage and local grid intensity
* Supporting multiple connection methods for different hardware setups

#### Prometheus Integration

**Type:** Metrics aggregation system\
**Connection Method:** HTTP API with optional authentication

**How it works:**

1. User ensures node\_exporter is running on target servers
2. They provide Prometheus URL and authentication credentials
3. We query PromQL for CPU, RAM, and power metrics
4. We match servers by instance label
5. We calculate emissions from energy consumption over time

**Required Fields:**

* Prometheus URL

**Optional Fields:**

* Bearer Token
* Basic Auth (username/password)
* Instance Label (defaults to hostname)

**Metrics Queried:**

* `node_cpu_seconds_total` (CPU usage)
* `node_memory_MemTotal_bytes` / `node_memory_MemAvailable_bytes` (RAM)
* Custom power metrics if available
* Instantaneous power reading (watts)
* CPU temperature (Celsius)

**Metrics Fetched:**

* IPMI Username (defaults to 'admin')
* IPMI Password

**Optional Fields:**

* IPMI Host (BMC IP address)

**Required Fields:**

1. User provides BMC/iLO/iDRAC IP address and credentials
2. We use `ipmitool` to query power and temperature sensors
3. If ipmitool unavailable, we fall back to Redfish REST API
4. We fetch instantaneous power draw and CPU temperature
5. We calculate emissions from power consumption over time

**How it works:**

**Type:** Hardware management interface\
**Connection Method:** IPMI-over-LAN or Redfish API

#### IPMI Integration

**Type:** Hardware management interface\
**Connection Method:** IPMI-over-LAN or Redfish API

**How it works:**

1. User provides BMC/iLO/iDRAC IP address and credentials
2. We use `ipmitool` to query power and temperature sensors
3. If ipmitool unavailable, we fall back to Redfish REST API
4. We fetch instantaneous power draw and CPU temperature
5. We calculate emissions from power consumption over time

**Required Fields:**

* IPMI Host (BMC IP address)

**Optional Fields:**

* IPMI Username (defaults to 'admin')
* IPMI Password

**Metrics Fetched:**

* Instantaneous power reading (watts)
* CPU temperature (Celsius)
* SNMP Community String
* API Key/Token (for REST)
* Outlet ID (for per-outlet tracking)

**Optional Fields:**

* PDU Host (IP address)

**Required Fields:**

1. User provides PDU IP address and connection method (SNMP/REST)
2. For SNMP: we poll power consumption OIDs
3. For REST: we query vendor API endpoints
4. We track real-time power draw per outlet or total
5. We calculate emissions from measured energy consumption

**How it works:**

**Type:** Power Distribution Unit\
**Connection Method:** SNMP or REST API

#### PDU Integration

**Type:** Power Distribution Unit\
**Connection Method:** SNMP or REST API

**How it works:**

1. User provides PDU IP address and connection method (SNMP/REST)
2. For SNMP: we poll power consumption OIDs
3. For REST: we query vendor API endpoints
4. We track real-time power draw per outlet or total
5. We calculate emissions from measured energy consumption

**Required Fields:**

* PDU Host (IP address)

**Optional Fields:**

* SNMP Community String
* API Key/Token (for REST)
* Outlet ID (for per-outlet tracking)<br>

