# SQL Case Study: Citi Bike Operations

A stakeholder-driven PostgreSQL case study on monthly Citi Bike trip data. The queries answer when trips occur, which start stations lead, how member and casual trip types differ, how monthly activity changes, and which days are unusual.

## Data and source terms

Citi Bike publishes downloadable monthly trip histories on its [official system-data page](https://citibikenyc.com/system-data), under the NYCBS Data Use Policy. Fetch a month with:

```bash
python scripts/download_citibike.py --month 2024-06
```

The script downloads and extracts CSV files to `data/raw/`. For manual acquisition, follow the official page and place its extracted CSV file(s) there. Raw files are not committed.

## Load into PostgreSQL

Create the table with `psql -d citibike -f sql/01_schema.sql`, then load each modern-format CSV through psql. Replace `<extracted-csv>` with a file produced by the downloader:

```sql
\copy trips(ride_id, rideable_type, started_at, ended_at, start_station_name,
  start_station_id, end_station_name, end_station_id, start_lat, start_lng,
  end_lat, end_lng, member_casual) FROM 'data/raw/<extracted-csv>'
  WITH (FORMAT csv, HEADER true, NULL '');
```

Run the numbered SQL files in order. Results depend on the month and data vintage.

## Questions answered

1. What are the top three hours within each day of week?
2. Which start stations have high daily and rolling seven-observed-day activity?
3. How do trip volume and duration differ between member and casual ride types?
4. What are the monthly counts and changes?
5. Which days fall more than two standard deviations from mean daily trip count?

## Limitations

Modern public records expose ride IDs and rider type, but not persistent rider identities; individual “power users” cannot be identified from this source. Station names may be absent or change. A single month cannot establish seasonality. A global z-score can flag holidays, weather, service changes, and data issues alike, so investigate anomalies before treating them as operational incidents.
