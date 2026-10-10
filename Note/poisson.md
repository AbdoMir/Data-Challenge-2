Une distribution “Poisson-like” signifie simplement que les données ressemblent au comportement statistique d’une loi de Poisson. C’est une distribution utilisée pour modéliser des événements qui se comptent.

L’idée de base est simple : on compte combien de fois quelque chose arrive pendant un intervalle de temps ou d’espace.

Exemples classiques :

- nombre d’appels reçus dans un centre par minute
- nombre de mutations dans un gène
- nombre de visites aux urgences par jour
- nombre de crises d’asthme dans un mois

pre_asthma_days : compte le nombre de jours avec symptômes avant l’index. C’est exactement le type de variable que la statistique appelle variable de comptage.

1. Forme typique d’une distribution de Poisson

Elle a une forme très reconnaissable :

- beaucoup de petites valeurs
- peu de grandes valeurs
- asymétrie vers la droite

0  █████████████████
1  █████████████
2  █████████
3  █████
4  ███
5  ██
6  █

Dans notre cas pre_asthma_days : 
- >0–5 jours : majorité
- >6–10 : peu
- >10 : très rare

c’est exactement le comportement Poisson-like.

2. Propriété mathématique intéressante

Dans une vraie loi de Poisson : moyenne ≈ variance

3. Petite subtilité intéressante

Dans les données médicales réelles, les distributions sont rarement une Poisson parfaite. On observe souvent : variance > moyenne
on appelle cela overdispersion.

Les statisticiens utilisent alors une distribution appelée : Negative Binomial qui est une version plus flexible de la Poisson.

Les données de maladies chroniques (comme l’asthme) suivent très souvent ce type de comportement

4. Vérifier le ratio dispersion
Si 
| Ratio | Interprétation  |
| ----- | --------------- |
| ≈ 1   | Poisson         |
| > 1   | Overdispersion  |
| < 1   | Underdispersion |

```python
stats = numeric_df.select(
    pl.col("pre_asthma_days").mean().alias("mean"),
    pl.col("pre_asthma_days").var().alias("var")
)

stats.with_columns(
    (pl.col("var") / pl.col("mean")).alias("dispersion_ratio")
)

```