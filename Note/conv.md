# "NA" et chaînes vides → null (colonnes string)
```python
df = df.with_columns(
    pl.col(pl.Utf8).replace(["NA", ""], None)
)
```

# NaN → null (colonnes numériques)
```python
df = df.with_columns(
    pl.col(pl.NUMERIC_DTYPES).fill_nan(None)
)
```

```python
df = df.with_columns([

    # Identifiant
    pl.col("patid").cast(pl.Int64),

    # Variables entières
    pl.col("index_age").cast(pl.Int64),
    pl.col("total_pre_index_cannisters_365").cast(pl.Int64),
    pl.col("pre_asthma_days").cast(pl.Int64),
    pl.col("post_index_exacerbations365").cast(pl.Int64),

    # Variables binaires (0/1)
    pl.col("female").cast(pl.Int64),
    pl.col("previous_asthma_drugs").cast(pl.Int64),
    pl.col("acute_bronchitis").cast(pl.Int64),
    pl.col("acute_laryngitis").cast(pl.Int64),
    pl.col("gerd").cast(pl.Int64),
    pl.col("pneumonia").cast(pl.Int64),
    pl.col("rhinitis").cast(pl.Int64),
    pl.col("sinusitis").cast(pl.Int64),
    pl.col("upper_respiratory_infection").cast(pl.Int64),
    pl.col("drug_s").cast(pl.Int64),

    # Variables continues
    pl.col("pre_asthma_charge").cast(pl.Float64),
    pl.col("pre_asthma_pharma_charge").cast(pl.Float64),
    pl.col("total_pre_index_charge").cast(pl.Float64),
    pl.col("log_charges").cast(pl.Float64),
    pl.col("log_asthma_charge").cast(pl.Float64),
    pl.col("adherence").cast(pl.Float64),

])
```
