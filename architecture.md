# Architecture Deep Dive

## Lakeflow Pipeline DAG

```
bronze_trips ──────────────────┐
bronze_vehicles ──► silver_vehicles ──┐
                                      ├──► silver_trips ──► gold_daily_trip_summary
bronze_routes ───► silver_routes ─────┘                 ──► gold_route_performance
                                                         ──► gold_vehicle_utilization

bronze_events ──► silver_events ──────────────────────► gold_incident_impact
```

## Data Quality Strategy

| Layer  | Strategy | Tool |
|--------|----------|------|
| Bronze | Accept all — preserve raw | Auto Loader schema inference |
| Silver | Validate + flag/drop | `@dlt.expect`, `@dlt.expect_or_drop` |
| Gold   | Aggregated — inherits Silver quality | Business logic filters |

## DQ Expectations Applied

### silver_trips
- `trip_id IS NOT NULL`
- `fare_amount > 0`
- `status IN ('completed', 'cancelled', 'in_progress')` → **drop on fail**
- `delay_min >= 0`

### silver_vehicles
- `vehicle_id IS NOT NULL`
- `capacity > 0`
- `is_active = true` → **drop on fail** (only active fleet in Silver)

### silver_routes
- `route_id IS NOT NULL`
- `distance_km > 0`

### silver_events
- `event_id IS NOT NULL`

## Auto Loader (cloudFiles) Benefits
- **Exactly-once ingestion** — tracks processed files via RocksDB checkpoint
- **Schema evolution** — auto-detects new columns
- **Scalable** — handles millions of files without listing overhead
- **Incremental** — only processes new/changed files per trigger
