### Approche Hybrid synthetic generator

- 🟢 local interpolation (comme SMOTE amélioré)
- 🔵 global sampling (distribution multivariée)


Notre approche combine une génération locale basée sur les voisins pour préserver la structure des données, avec une génération globale et un bruit contrôlé pour améliorer la diversité, tout en maintenant des distributions proches des données originales.

Contrairement à Avatar, qui adopte une approche conservatrice centrée sur le réalisme local, mon approche introduit un mécanisme d’exploration contrôlée de l’espace des données. Cela permet d’enrichir la diversité des données synthétiques tout en conservant les propriétés statistiques essentielles, offrant ainsi un meilleur équilibre entre utilité et confidentialité.

Conclusion : 
Mon approche combine generation locale + globale 
- locale : on restes proche des données réelles (interpolation) - conserve coorélation et réalisme 
- globale : l'idée c est de generer des points réalistes sans utiliser les voisins mais en explorant espace globale des donnée.

Comment ? 

La génération globale consiste à créer des points synthétiques réalistes sans utiliser les voisins, en s’appuyant sur la structure globale des données. 
Cette structure est capturée par la moyenne (centre des données) et la matrice de covariance, qui encode les relations et corrélations entre les variables. 
Cela permet de générer de nouveaux points cohérents avec la distribution globale, tout en explorant des zones de l’espace des données qui ne sont pas nécessairement couvertes par les voisins locaux. 
Ainsi, cette approche augmente la diversité et permet de créer de nouveaux profils tout en conservant une cohérence statistique globale.

Un mécanisme probabiliste est utilisé pour alterner entre une génération locale, favorisant la fidélité aux données, et une génération globale, permettant d’augmenter la diversité, avec un contrôle assuré par le paramètre mix_ratio.



```python
def generate_synthetic_me(X, k=10, n_components=10, noise=0.02, mix_ratio=0.2):

    # projection PCA
    pca = PCA(n_components=min(n_components, X.shape[1]))
    X_pca = pca.fit_transform(X)
    
    # KNN
    # on trouve les k voisins les plus proches dans l'espace PCA pour chaque point
    nbrs = NearestNeighbors(n_neighbors=k).fit(X_pca)
    _, indices = nbrs.kneighbors(X_pca)

    # global stats
    # on calcule la moyenne et la covariance de l'ensemble des données pour pouvoir faire du sampling global
    mean = np.mean(X, axis=0)
    cov = np.cov(X.T)
    cov += np.eye(cov.shape[0]) * 1e-3

    synthetic = []

    for i in range(len(X)):
        # on décide aléatoirement de faire du sampling global ou local pour chaque point, en fonction du mix_ratio
        if np.random.rand() < mix_ratio:
            # global sampling
            # on génère un point synthétique en échantillonnant à partir d'une distribution normale multivariée définie par la moyenne et la covariance de l'ensemble des données
            point = np.random.multivariate_normal(mean, cov)

        else:
            # local generation
            # on génère un point synthétique en interpolant entre deux voisins aléatoires parmi les k plus proches voisins du point i dans l'espace PCA
            neighbors = X_pca[indices[i]]
            # on choisit deux voisins aléatoires parmi les k plus proches voisins
            i1, i2 = np.random.choice(k, 2, replace=False) # indices des deux voisins choisis
            alpha = np.random.rand() # coefficient d'interpolation aléatoire entre 0 et 1
            point_pca = alpha * neighbors[i1] + (1 - alpha) * neighbors[i2] 
            # interpolation linéaire entre les deux voisins dans l'espace PCA pour créer un point synthétique qui est une combinaison des caractéristiques des deux voisins, ce qui permet de capturer la structure locale des données tout en introduisant de la variabilité.

            # FIX
            point = pca.inverse_transform(point_pca.reshape(1, -1))[0]
            # on ramène le point synthétique dans l'espace original des données en utilisant la transformation inverse de PCA, ce qui permet de conserver les caractéristiques originales des données tout en ayant généré un point synthétique basé sur les voisins dans l'espace PCA.

        # bruit léger
        std = np.std(X, axis=0) # on calcule l'écart-type de chaque variable dans les données originales pour pouvoir ajouter un bruit proportionnel à la variabilité de chaque variable
        point += np.random.normal(0, noise * std) # on ajoute un bruit gaussien à chaque variable du point synthétique, où l'amplitude du bruit est proportionnelle à l'écart-type de la variable dans les données originales, ce qui permet d'introduire de la variabilité supplémentaire tout en respectant la distribution des données originales.

        synthetic.append(point)

    synthetic = np.array(synthetic) # on convertit la liste de points synthétiques en un tableau numpy pour faciliter les opérations ultérieures et l'intégration avec d'autres outils de traitement de données.

    feature_names = preprocessor.get_feature_names_out().tolist()

    return pl.DataFrame(synthetic, schema=feature_names)
```

```python
X_synth = generate_synthetic_me(X, k=10 , noise=0.02, mix_ratio=0.2)
synthetic_df = pl.DataFrame(X_synth, schema=feature_names.tolist())

results = evaluate(real, synthetic_df)
```

```python
grid_results = []

param_grid = {
    "k": [5, 10, 15],
    "n_components": [8, 10, 15],
    "noise": [0.005, 0.01, 0.02],
    "mix_ratio": [0.0, 0.1, 0.2]
}

combinations = list(itertools.product(
    param_grid["k"],
    param_grid["n_components"],
    param_grid["noise"],
    param_grid["mix_ratio"]
))

for k, n_comp, noise, mix_ratio in combinations:

    X_synth = generate_synthetic_me(
        X,
        k=k,
        n_components=n_comp,
        noise=noise,
        mix_ratio=mix_ratio
    )

    synth = pl.DataFrame(X_synth, schema=feature_names.tolist())

    w = compute_wasserstein(real, synth)
    corr = compute_correlation_diff(real, synth)
    dcr = compute_dcr(real, synth)

    grid_results.append({
        "k": k,
        "n_components": n_comp,
        "noise": noise,
        "mix_ratio": mix_ratio,
        "wasserstein": w,
        "corr": corr,
        "dcr": dcr
    })

results_me = pl.DataFrame(grid_results)
results_me.sort("dcr")

# trier par DCR pour trouver les meilleurs paramètres en termes de privacy (DCR plus élevé = meilleur)

```

```python
# wasserstein et corr doivent être minimisés, dcr maximisé

results_me = results_df.with_columns(
    score = -pl.col("wasserstein") - pl.col("corr") + pl.col("dcr")
)

# créer une nouvelle colonne "score" qui combine les trois métriques en donnant plus de poids à DCR (privacy)

# et en pénalisant les scores de Wasserstein et de corrélation (plus ils sont bas, mieux c'est)

best = results_me.sort("score", descending=True).head(1)
print(best)

```