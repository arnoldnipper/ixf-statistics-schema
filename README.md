# IXP Aggregated Statistics Timeseries Schema

This repository contains the JSON Schema definition for exporting aggregated traffic and infrastructure statistics from Internet Exchange Points (IXPs). 

The goal of this schema is to establish a global, vendor-neutral standard within the **Internet Exchange Federation (IX-F)** to make time series data (e.g., bits per second, packets per second) easily shareable and comparable across IXPs, research institutions, and community tools like IXP Manager.

## 🚀 Key Features & Architecture

* **High Wire Efficiency:** Uses a compact positional array (tuple) format for `samples` to minimise payload size over HTTP when transmitting long timeseries.
* **Robust Column Metadata:** Avoids the fragility of classic tuples by defining a rigid `fields` descriptor array for each data source. Parsers always know exactly what each array position means.
* **Deterministic Gap Handling:** Standardises the use of JSON `null` to represent `RRD UNKNOWN` values (e.g., during poller gaps or interface downtime), ensuring strict type safety (`["number", "null"]`).
* **Interpretation Safety:** Mandates parameters like `direction_reference` (`ingress_to_fabric` vs. `egress_from_fabric`) and RRD `consolidation` functions (`AVERAGE`, `MAX`) so consumers never compare apples to oranges.

## 📂 Repository Structure

* `schema.json` — The authoritative IXP Statistics JSON Schema (compliant with Draft-07 strict validation).
* `examples/` — Concrete reference implementations.
  * `examples/valid-statistics.json` — A complete production example showing multi-granularity data and simulated poller gaps.

## 🛠️ Usage & Integration

### Schema Location
The latest stable version of the schema can be referenced or validated against using the root `schema.json` file in this repository.

### Consuming the Data (Parser Logic)
When writing a client to parse an export matching this schema, follow this standard workflow:
1. Read the `sources` object and look up the specific series key (e.g., the IX-F ID `ixf_ixid`).
2. Inspect `sources[key].fields` to learn the positional array structure. Position `0` is always the ISO 8601 `timestamp`.
3. Loop over the `samples[key]` array. Map each position of the sub-arrays to the fields you discovered in step 2.
4. Safely handle `null` values as missing data points without breaking your database ingestion pipeline.

## 🤝 Contributing & Governance

This project aims to become an official global standard maintained by the **Internet Exchange Federation (IX-F)**. 

### How to propose changes:
1. **Fork the Repository:** Create a feature branch for your schema modifications.
2. **Validate Examples:** If you add or modify fields, ensure that all files in the `examples/` directory are updated and validate successfully against your modified `schema.json`.
3. **Open a Pull Request:** Describe the engineering rationale behind the change, especially how it affects backward compatibility for existing parsers.

---
*Note: This schema is designed to complement the existing IX-F Member Export Schema by providing historical timeseries context to the static member registries.*

