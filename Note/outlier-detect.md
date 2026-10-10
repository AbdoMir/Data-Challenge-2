[Outlier](https://mrmint.fr/outliers-machine-learning)

***Z-score (outliers)- extrêmes par rapport à moyenne***
```python
z_mask = None

for col in numeric_df.columns:

    mean = numeric_df.select(pl.col(col).mean()).item()
    std = numeric_df.select(pl.col(col).std()).item()

    cond = ((pl.col(col) - mean) / std).abs() > 3

    if z_mask is None:
        z_mask = cond
    else:
        z_mask = z_mask | cond

df_out_z = numeric_df.filter(z_mask)

z_summary = pl.DataFrame({
    "method": ["Z-score"],
    "n_outliers": [df_out_z.height],
    "pct_outliers": [df_out_z.height / df.height * 100]
})

z_summary
```


***IQR (outliers) - extrêmes robustes (quantiles)***

```python
iqr_mask  = None

for col in numeric_df.columns:

    q1 = numeric_df.select(pl.col(col).quantile(0.25)).item()
    q3 = numeric_df.select(pl.col(col).quantile(0.75)).item()
    iqr = q3 - q1

    lower = q1 - 1.5 * iqr
    upper = q3 + 1.5 * iqr

    cond = (pl.col(col) < lower) | (pl.col(col) > upper)

    if iqr_mask  is None:
        iqr_mask  = cond
    else:
        iqr_mask  = iqr_mask  | cond

df_out_iqr = numeric_df.filter(iqr_mask )

iqr_summary = pl.DataFrame({
    "method": ["IQR"],
    "n_outliers": [df_out_iqr.height],
    "pct_outliers": [df_out_iqr.height / df.height * 100]
})

iqr_summary
```

***Isolation forest (outlier) - anomalies structurelles multivariées***
```python
iso = IsolationForest(
    #n_estimators=200,
    #contamination=0.02,   # % estimé d'outliers
    random_state=42
)

iso.fit(numeric_df)

# -1 = outlier, 1 = normal
labels = iso.predict(numeric_df)

df_iso = numeric_df.with_columns(
    pl.Series("outlier_iso", labels)
)

n_outliers = (labels == -1).sum()

iso_summary = pl.DataFrame({
    "method": ["IsolationForest"],
    "n_outliers": [n_outliers],
    "pct_outliers": [n_outliers / len(labels) * 100]
})

iso_summary
```

***Comparaison***
```python
comparison = pl.concat([
    iso_summary,
    iqr_summary,
    z_summary
])

comparison
```

***Intersection***

```python
# -------- masque intersection --------
intersection_mask = (
    z_mask & 
    iqr_mask & 
    (pl.col("outlier_iso") == -1)
)

# dataframe des anomalies
df_intersection = df_iso.filter(intersection_mask)

# -------- résumé --------
summary = pl.DataFrame({
    "method": ["Z-score ∩ IQR ∩ IsolationForest"],
    "n_outliers": [df_intersection.height],
    "pct_outliers": [df_intersection.height / df.height * 100]
})

print(summary)
```

| Méthode                       | Principe                                                                                            | Type de distribution                 | Sensibilité aux outliers             | Type de données        | Avantages                                                   | Limites                                                                       |
| ----------------------------- | --------------------------------------------------------------------------------------------------- | ------------------------------------ | ------------------------------------ | ---------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------- |
| **Z-score**                   | Mesure la distance d’une observation à la moyenne en unités d’écart-type : ( z = (x - \mu)/\sigma ) | suppose une **distribution normale** | élevée                               | données **numériques** | simple, rapide, interprétation facile                       | fonctionne mal si la distribution est asymétrique ou avec beaucoup d’outliers |
| **IQR (Interquartile Range)** | Utilise les quartiles : outlier si (x < Q1 - 1.5×IQR) ou (x > Q3 + 1.5×IQR)                         | **aucune hypothèse de normalité**    | modérée                              | données **numériques** | robuste aux valeurs extrêmes, très utilisé en EDA           | moins efficace pour détecter anomalies complexes                              |
| **Isolation Forest**          | Algorithme ML qui **isole les observations rares** en construisant des arbres aléatoires            | aucune hypothèse                     | faible sensibilité aux distributions | **multivarié**         | détecte anomalies complexes, fonctionne sur grands datasets | plus difficile à interpréter, nécessite réglage de paramètres                 |
