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
│   ├── Projet0/   EDA.ipynb         # Analyse exploratoire
│   ├── Projet1/                     # Méthodes d'ensemble (bagging, boosting, forêt aléatoire)
│   ├── Projet2/                     # Modèles de régression
│   └── Projet3/                     # Génération de données synthétiques (méthode Avatar)
├── Note/                            # Notes de recherche (markdown)
├── docs/                            # Visualisations
├── models/
│   └── final_model.pkl              # Modèle final sérialisé
└── outputs/                         # Fichiers de prédictions CSV
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
Comparaison de **Bagging**, **Boosting** (XGBoost) et **Forêt aléatoire** pour la prédiction d'exacerbations.

### Projet 2 : Régression
Modèles de régression linéaires et non linéaires, avec gestion du déséquilibre et optimisation des hyperparamètres (Optuna).

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

- Groupe 3 : Abdo, Louise, Flora
- Groupe 5 : Abdo, Talha, Flora
