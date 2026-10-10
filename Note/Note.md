La plupart des estimateurs scikit-learn ne gèrent pas les valeurs manquantes ; quand c’est le cas (ex. impute.SimpleImputer), NaN est la représentation recommandée dans les tableaux float, mais elle est impossible dans les tableaux int. Dans ce cas, on peut définir une autre valeur via missing_values si l’imputation/l’apprentissage peut se faire en espace entier, et les données non étiquetées sont un cas particulier de valeurs manquantes dans la cible.

- Cas classique avec NaN (float)
```python
imputer = SimpleImputer(strategy="mean")
X_imputed = imputer.fit_transform(X)
``` 
- Ici np.nan est accepté car le tableau est en float.
La valeur manquante est remplacée par la moyenne de la colonne.

- Cas avec tableau entier (int)
- Un tableau int ne peut pas contenir NaN.
    - imputer = SimpleImputer(missing_values=-1, strategy="mean")
    - X_imputed = imputer.fit_transform(X)
- On doit spécifier que -1 est la valeur manquante

- Cas particulier : cible non étiquetée
- y = np.array([1, 0, np.nan, 1])
- Le np.nan dans y signifie que cette observation n’a pas de label.


Les jeux de données réels contiennent souvent des valeurs manquantes (NaN, cellules vides, etc.), mais les modèles scikit-learn exigent des données entièrement numériques et complètes.
Supprimer les lignes ou colonnes avec des valeurs manquantes est possible, mais cela entraîne une perte d’information.
Une approche plus appropriée consiste à imputer les valeurs manquantes, ***c’est-à-dire les estimer à partir des données disponibles.***

Avant d’imputer, il faut se demander :

- Missing Completely At Random (MCAR) ?
- Missing At Random (MAR) ?
- Missing Not At Random (MNAR) ?


###  *Echelle* 
Les données standardisées sont transformées de sorte que leur moyenne soit égale à 0 (centrage) et leur variance égale à 1 (réduction).
Cela permet de mettre toutes les variables sur la même échelle et d’éviter qu’une variable domine artificiellement l’apprentissage du modèle.

- *StandardScaler* ne sait travailler qu’avec des floats réels. Pas des chaînes

- *StandardScaler* est sensible aux valeurs aberrantes, et les features peuvent etre normaliser différemment les unes des autres en présence de ces valeurs aberrantes. 


*“Normalisé”* ne veut pas dire :
- que les données suivent une loi normale (gaussienne)
- ni qu’elles deviennent symétriques

*On centre et on réduit.*
- On ne change pas la forme de la distribution.
- Si la variable est asymétrique , elle restera asymetrique 


TransformerAPI permettant de calculer la moyenne et l'écart type sur un ensemble d'entraînement afin de pouvoir ensuite appliquer la même transformation à l'ensemble de test.

- Les données normalisées ont une moyenne nulle et une variance unitaire 



```python
def cast_numeric_int(s):  # user-defined function
    return s.cast(pl.Int64, strict=False)
```

# construct a data transformer from a user-defined function
```python
cast_int_transformer = skl.preprocessing.FunctionTransformer(cast_numeric_int)

int_transformer = skl.pipeline.Pipeline(
    steps=[
        ('caster', cast_int_transformer),
        ('scaler', skl.preprocessing.StandardScaler()),
    ]
)
```


```python
def cast_numeric_float(s):
    return s.cast(pl.Float64, strict=False)

cast_float_transformer = skl.preprocessing.FunctionTransformer(cast_numeric_float)

float_transformer = skl.pipeline.Pipeline(
    steps=[
        ('caster', cast_float_transformer),
        ('scaler', skl.preprocessing.StandardScaler()),
    ]
)
```


```python
categorical_transformer = skl.pipeline.Pipeline(
    steps=[
        ('encoder', skl.preprocessing.OneHotEncoder()),  # OrdinalEncoder() ?
    ]
)
```


```python
preprocessor = skl.compose.ColumnTransformer(  # apply transformers to columns
    transformers=[
        ('num_float', float_transformer, numeric_features_float),
        ('num_int', int_transformer, numeric_features_int),
        ('cat', categorical_transformer, categorical_features),
    ]
)
```