# Data Challenge 2 : Prédiction des exacerbations de l'asthme

Projet académique de M1 combinant **modélisation prédictive** et **génération de données synthétiques** sur un jeu de données de patients asthmatiques.

---

## Objectif

Prédire le nombre d'exacerbations d'asthme dans l'année suivant le début d'un traitement (`post_index_exacerbations365`), à partir de variables cliniques, démographiques et économiques pré-index.

Une seconde problématique porte sur la **confidentialité des données médicales** : générer des données synthétiques via la méthode Avatar qui préservent les propriétés statistiques tout en garantissant la vie privée des patients.

---

## Structure du projet

```
├── Data/
│   ├── trainDataDC2.csv.gz          # Jeu de données (2 089 patients)
│   └── description_variables.md     # Dictionnaire des variables
├── notebook/
│   ├── Projet0/EDA.ipynb                                   # Analyse exploratoire
│   ├── Projet1/Projet1-DC2-G6-comparaison.ipynb            # Bagging, boosting et forêt aléatoire comparés
│   ├── Projet2/Prediction_nbr_exacerbation_annnee.ipynb    # Modèles de comptage et boosting Poisson
│   └── Projet3/                                            # Données synthétiques (méthode Avatar)
├── Note/                            # Notes de recherche (markdown)
├── docs/                            # Visualisations du pipeline
├── image/                           # Figures et schémas
├── models/
│   ├── best_model_projet1_comparaison.pkl   # Modèle retenu au Projet 1
│   └── final_model.pkl                      # Modèle final sérialisé
└── outputs/                         # Prédictions CSV et rendu HTML des pipelines
```

---

## Données

| Variable | Description |
|---|---|
| `index_age` | Âge du patient au début du traitement |
| `female` | Sexe féminin (0/1) |
| `acute_bronchitis`, `gerd`, `rhinitis`, … | Comorbidités pré-index (binaires) |
| `total_pre_index_cannisters_365` | Nombre de bonbonnes SABA utilisées (proxy de sévérité) |
| `pre_asthma_days` | Jours de traitement antiasthmatique |
| `pre_asthma_charge`, `log_asthma_charge` | Coûts de santé liés à l'asthme |
| `total_pre_index_charge`, `log_charges` | Coûts totaux pré-index |
| **`post_index_exacerbations365`** | **Variable cible** : exacerbations dans l'année suivante |

---

## Phases du projet

### Projet 0 : Analyse exploratoire (EDA)
Distribution des variables, corrélations, valeurs manquantes, déséquilibre de classes.

### Projet 1 : Méthodes d'ensemble
Classification binaire : survenue ou non d'au moins une exacerbation dans l'année. Les trois notebooks d'origine (bagging, boosting, forêt aléatoire) ont été réunis en un seul notebook qui applique aux trois méthodes le même prétraitement et le même protocole d'évaluation, afin que la comparaison porte sur la méthode et non sur des différences de mise en œuvre.

- Méthodes comparées, toutes adaptées au déséquilibre des classes : `BalancedBaggingClassifier`, `EasyEnsembleClassifier` et `BalancedRandomForestClassifier` (imbalanced-learn).
- Protocole commun : recherche d'hyperparamètres en validation croisée, réglage du seuil de décision par `TunedThresholdClassifierCV`, puis évaluation unique sur un jeu de test de 20 % mis de côté dès le départ.
- Le modèle retenu est choisi sur le F1 en validation croisée, jamais sur le jeu de test.

| Méthode | F1 CV + seuil | F1 test | Rappel test | AUC test | Durée (min) |
|---|---|---|---|---|---|
| Bagging | 0,222 | 0,236 | 0,610 | 0,559 | 24,5 |
| Boosting | 0,221 | 0,238 | 0,610 | 0,562 | 86,5 |
| Forêt aléatoire | 0,222 | 0,236 | 0,615 | 0,559 | 12,6 |

Les trois méthodes obtiennent des performances quasi identiques, avec une AUC proche de 0,56 : la limite tient au faible pouvoir discriminant des variables disponibles plutôt qu'au choix de l'algorithme. Le bagging est retenu de justesse et exporté dans `models/best_model_projet1_comparaison.pkl`.

### Projet 2 : Régression de comptage
Prédiction du nombre d'exacerbations dans l'année. La cible compte environ 89 % de zéros, pour une moyenne voisine de 0,17 et une variance voisine de 0,37. Après étude de la dispersion, plusieurs familles de modèles ont été comparées sur la déviance de Poisson :

| Modèle | Déviance |
|---|---|
| Dummy (moyenne) | 0,950 |
| Poisson | 0,940 |
| Zero-Inflated Poisson | 0,941 |
| Binomiale négative | 0,941 |
| Zero-Inflated Negative Binomial | 0,943 |
| HistGradientBoosting (loss Poisson) | 0,938 |
| Modèle en deux parties (hurdle) | 0,938 |
| **XGBoost (objectif Poisson)** | **0,931** |

Les modèles linéaires généralisés plafonnent autour de 0,94, quelle que soit la loi retenue, ce qui indique que la limite vient de la forme linéaire de la relation plutôt que de la distribution. Les modèles à base d'arbres captent mieux les interactions et les effets non linéaires, et XGBoost avec objectif Poisson est retenu comme modèle final.

### Projet 3 : Données synthétiques (méthode Avatar)
Génération de patients synthétiques préservant les propriétés statistiques du jeu original, évalués sur des métriques de confidentialité :
- **DCR** (Distance to Closest Record)
- **NNDR** (Nearest Neighbour Distance Ratio)
- Local cloaking & hidden rate

> Référence : Guillaudeux et al. 2023 : *Patient-centered synthetic data generation*

---

## Stack technique

- **Python 3.12** : gestionnaire de paquets : `uv`
- **ML :** scikit-learn, XGBoost, imbalanced-learn, Optuna
- **Data :** pandas, polars, numpy
- **Stats :** statsmodels, scipy
- **Visualisation :** seaborn, altair, plotly
- **Réduction de dimension :** prince (MCA / PCA)

---

## Lancer le projet

```bash
# Installer les dépendances
uv sync

# Lancer Jupyter
uv run --with notebook jupyter notebook --notebook-dir=notebook
```

---

## Équipe

Projet réalisé en groupe dans le cadre du Master 1 : S2.

