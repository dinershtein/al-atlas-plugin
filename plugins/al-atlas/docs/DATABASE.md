# PostgreSQL: rnal

Verified 2026-09-22. One database, five application schemas. Socket
`/tmp/rnal-pgsocket`, port `55432`; persistent files `/var/lib/rnal-postgres/data`.

## Tables (17)

| Schema | Table | Content |
| --- | --- | --- |
| public | establishments | 111,616 RNAL registrations; names, addresses, type, capacity, coordinates |
| public | holders | Holder observations linked to registrations: identity and legal role |
| public | holder_contacts | Collected holder contact information |
| public | insurance_policies | Collected insurance records |
| public | property_authorizations | Property use/municipal authorization details |
| public | rnal_snapshots | Retrieved RNAL pages, raw HTML and fetch/parse outcomes |
| public | rnal_progress | Per-registration collection status, attempts, last error |
| public | sources | Source URLs and retrieval metadata |
| rnal_research | detail_observations | 111,588 parsed details: holders, location, capacity, parser metadata |
| al_atlas | company_profiles | 25 individually researched company profiles |
| al_atlas | research_batches | Research batches/pilot selections |
| al_atlas | orders | Product orders and delivery workflow |
| al_atlas | order_events | Order event history |
| al_atlas | stripe_events | Payment webhook processing records |
| al_atlas | request_limits | Product request rate counters |
| al_atlas | dataset_runs | Product dataset build/run metadata |
| rnal_mcp_auth | records | Encrypted OAuth clients, pending grants and token metadata |

## Materialized views (2)

These store query results and require an explicit refresh after ingestion.

- `al_atlas.companies`: 14,859 corporate identifier candidates and portfolio metrics.
- `rnal_mcp.company_links`: 43,083 distinct company/property/role triples;
  43,078 distinct company/property pairs.

## Ordinary views (7)

- `public.asset_light_operator_metrics`: research metrics for operator selection.
- `rnal_research.holders_all_roles`: holder-role data for analytical use.
- `rnal_research.email_links`: email-based links for research.
- `al_atlas.company_properties`: company-to-registration links for the product.
- `rnal_mcp.companies`: corporate aggregates plus reviewed profile information.
- `rnal_mcp.properties`: selected registration/detail fields for MCP.
- `rnal_mcp.dataset`: source dates and live counts.

Companies are deduplicated by corporate identifier; `holders` is not a unique
company directory and includes individuals. The corporate heuristic uses NIPCs
starting with 5. A company can link to multiple registrations, a registration can
link to multiple holders/roles. RNAL registrations are not necessarily separate
apartments, active listings or management contracts.

For analysis start with `al_atlas.companies` and `al_atlas.company_properties`.
For complete collected details use the `public` and `rnal_research` tables; MCP
exposes only selected read-only fields, not every field held in PostgreSQL.

## Selected fields and join keys

| Relation | Key / important fields |
| --- | --- |
| `public.establishments` | `rnal_number`, `name`, `modality`, `guests`, `registration_date`, `public_opening_date`, `address`, `postal_code`, `locality`, `municipality`, `parish`, `district`, `lat`, `lon`, `geocode_reliability`, `source_id` |
| `public.holders` | `holder_id`, `rnal_number`, `nif_nipc`, `legal_name`, `role`, `source_url`, `retrieved_at` |
| `rnal_research.detail_observations` | `rnal_number`, `snapshot_id`, `snapshot_fetched_at`, `parser_version`, `parsed_at`; JSON fields `holders`, `location`, `capacity`, `warnings` |
| `al_atlas.companies` | `tax_id` (deduplicated NIPC), `legal_name`, `property_count`, `municipalities`, `latest_snapshot` |
| `al_atlas.company_profiles` | `tax_id`, `brand`, `website`, `contact_url`, `activity_url`, `summary`, `operator_verified`, `source_url`, `evidence_note`, `checked_at` |
| `rnal_mcp.company_links` | Distinct triple `tax_id`, `rnal_number`, `role` |
| `rnal_mcp.properties` | Registration fields plus `registered_address`, `registered_capacity`, `coordinates_marked_reliable`, `base_snapshot_at`, `detail_snapshot_at`, `source_url` |

Join companies to profiles on `tax_id`. Join companies to `company_links` on
`tax_id`, then registrations on `rnal_number`. Count `DISTINCT rnal_number` after
joining roles, otherwise a registration with multiple roles is counted repeatedly.
`holders.nif_nipc` is the source identifier; the analytical views expose corporate
identifiers as `tax_id`. Prefer `rnal_research.holders_all_roles` when researching
all parsed roles rather than assuming the older `public.holders` is exhaustive.

In MCP company search, `registration_count` is the output name for the company-wide
`property_count`. Company detail returns that field inside `company` and a separate
`holder_roles` breakdown. Property search returns a summary; use `get_property`
for full address and capacity. Search pages have `items`, `total`, `offset`, `limit`
and nullable `next_offset`. Company/property detail has `found` and the record.

## SQL examples for an authorized database analyst

These illustrate the model. MCP does **not** accept SQL; use its named tools.

```sql
-- Company-wide top ten corporate candidates.
SELECT tax_id, legal_name, property_count
FROM al_atlas.companies
ORDER BY property_count DESC, tax_id LIMIT 10;

-- Top ten by leased registrations specifically (not all roles).
SELECT c.tax_id, c.legal_name, count(DISTINCT l.rnal_number) AS leased_registrations
FROM rnal_mcp.company_links l
JOIN rnal_mcp.companies c USING (tax_id)
WHERE l.role = 'Arrendatário'
GROUP BY c.tax_id, c.legal_name
ORDER BY leased_registrations DESC, c.tax_id LIMIT 10;

-- Reliable-coordinate subset for a Cascais map.
SELECT rnal_number, name, lat, lon, geocode_reliability
FROM rnal_mcp.properties
WHERE municipality = 'Cascais' AND coordinates_marked_reliable;
```
