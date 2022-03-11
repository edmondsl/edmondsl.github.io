## DE Zoomcamp 1.2.6

#### SQL Refresher

- Ingest the taxi zone look up data from [here](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

```python
df_zones = pd.read_csv('taxi+_zone_lookup.csv') 
df_zones.to_sql(name='zones', con=engine, if_exists ='replace')
```
