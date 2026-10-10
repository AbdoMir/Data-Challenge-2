```python
# Transformation en log
log_cols = [
    "adherence",
    "pre_asthma_pharma_charge",

]

numeric_df = numeric_df.with_columns(
    pl.col(log_cols).log1p()
)

numeric_df.sample(4)
```

```python
# Histogramme variable log 
df_plot = numeric_df.to_pandas()

# discrétisation pour la visualisation
bins = [-1, 0, 5, 10, 20, float("inf")]
labels = ["0", "1-5", "6-10", "11-20", ">20"]

df_plot["pre_asthma_days_cat"] = pd.cut(
    df_plot["pre_asthma_days"],
    bins=bins,
    labels=labels
)

# types de variables
count_vars = ["total_pre_index_cannisters_365"]
cat_vars = ["pre_asthma_days_cat"]
age_vars = ["index_age"]
continuous_vars = [c for c in df_plot.columns if c not in count_vars + cat_vars + age_vars + ["pre_asthma_days"]]

fig, axes = plt.subplots(3, 3, figsize=(15,10))
axes = axes.flatten()

i = 0

# variables continues
for col in continuous_vars:
    sns.histplot(df_plot[col], bins=25, kde=True, ax=axes[i])
    axes[i].set_title(col)
    i += 1

# variable discrétisée
sns.countplot(x="pre_asthma_days_cat", data=df_plot, ax=axes[i])
axes[i].set_title("pre_asthma_days")
i += 1

# variable de comptage (déjà entière)
sns.countplot(x="total_pre_index_cannisters_365", data=df_plot, ax=axes[i])
axes[i].set_title("total_pre_index_cannisters_365")
i += 1

# âge
sns.histplot(df_plot["index_age"], bins=20, kde=True, ax=axes[i])
axes[i].set_title("index_age")

# supprimer les axes inutilisés
for j in range(i, len(axes)):
    fig.delaxes(axes[j])

plt.tight_layout()
plt.show()
```

```python
# Boxplot variable log 
df_plot = numeric_df.to_pandas()
cols = df_plot.columns

fig, axes = plt.subplots(3, 3, figsize=(15,10))
axes = axes.flatten()

for i, col in enumerate(cols):
    sns.boxplot(y=df_plot[col], ax=axes[i])
    axes[i].set_title(col)

# supprimer les axes inutilisés
for j in range(i, len(axes)):
    fig.delaxes(axes[j])
    
plt.tight_layout()
plt.show()
```

### Constat
| Variable                       | Avant transformation                         | Après transformation log           | Effet principal                                           |
| ------------------------------ | -------------------------------------------- | ---------------------------------- | --------------------------------------------------------- |
| index_age                      | Distribution déjà assez normale              | Distribution légèrement compressée | Peu d’effet, transformation inutile                       |
| total_pre_index_cannisters_365 | Variable discrète (0,1,2)                    | Presque identique                  | Le log n’apporte rien sur une variable de comptage faible |
| pre_asthma_days                | Forte asymétrie avec beaucoup d’outliers     | Valeurs extrêmes comprimées        | Distribution plus compacte                                |
| adherence                      | Très asymétrique vers 0                      | Légèrement stabilisée              | Structure globale conservée                               |
| total_pre_index_charge         | Extrêmement asymétrique, énorme queue droite | Distribution quasi gaussienne      | Transformation très efficace                              |
| pre_asthma_charge              | Pic massif + valeurs extrêmes                | Structure plus lisible             | Les extrêmes deviennent gérables                          |
| pre_asthma_pharma_charge       | Forte dispersion et outliers                 | Distribution recentrée             | Variance stabilisée                                       |
| log_charges                    | :                                            | Distribution proche d’une cloche   | Bon comportement statistique                              |
| log_asthma_charge              | :                                            | Distribution plus régulière        | Améliore la modélisation                                  |

