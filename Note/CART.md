> CART est un acronyme qui sonne presque comme un chariot scientifique qui transporte des décisions. En réalité cela signifie Classification And Regression Trees. C’est l’algorithme classique utilisé pour construire des arbres de décision.

L’idée est très simple : on coupe l’espace des données en morceaux successifs pour rendre les observations de plus en plus homogènes.

Imagine un médecin qui pose des questions successives :

- âge > 50 ?
- tension > 140 ?
- fumeur ?

Chaque question sépare les patients en deux groupes. L’arbre se construit exactement comme ça.

### 1. Le principe de CART

CART construit un arbre binaire.

Chaque nœud se divise toujours en 2 branches seulement.

```python
          Age < 45 ?
         /          \
       oui          non
   BMI < 30 ?    Fumeur ?
   /     \        /     \
  A       B      C       D
```
> Chaque division cherche à créer les groupes les plus purs possible

### 2. CART pour la classification

Quand la variable cible est catégorielle.

Exemple :

- maladie = oui / non
- type de cancer
- classe d’image

Le critère utilisé est généralement l’indice de **Gini**.
Formule :  **Gini=1−∑pi**
où 𝑝𝑖 est la **proportion de chaque classe**.

Si un nœud contient :
| Classe | proportion |
| ------ | ---------- |
| A      | 0.8        |
| B      | 0.2        |

Alors : Gini=1−(0.82+0.22)=0.32

Plus Gini est petit, plus le nœud est pur.
L’algorithme cherche donc la séparation qui réduit le plus le Gini.

```python
from sklearn.tree import DecisionTreeClassifier

model = DecisionTreeClassifier(
    criterion="gini",
    max_depth=4,
    min_samples_leaf=20
)

model.fit(X_train, y_train)
```

### 3. CART pour la régression

Quand la cible est continue.

Exemple :

- prix
- âge
- taux biologique

Dans ce cas CART minimise **la variance ou l’erreur quadratique**

**MSE=n1​∑(yi​−yˉ​)2**
Chaque division essaie de créer deux groupes où les valeurs sont les plus proches possible.

```python
from sklearn.tree import DecisionTreeRegressor

model = DecisionTreeRegressor(
    criterion="squared_error"
)
```

### 4. Comment choisis t il la meilleur separation ? 
Pour chaque variable, CART teste plusieurs seuils.
Exemple :

variable âge
age < 20
age < 25
age < 30
age < 35
...

Pour chaque seuil il calcule :

- Gini (classification)
- MSE (régression)

Puis il choisit la meilleure séparation.

### 5. Pourquoi CART est important ?

Parce que beaucoup d’algorithmes célèbres sont basés dessus :

- Random Forest
- Gradient Boosting
- XGBoost
- LightGBM
- Bagging

Tous utilisent des **arbres CART** comme briques de base.

### 6. Les forces de CART

- fonctionne avec données non linéaires
- pas besoin de normalisation
- gère variables numériques et catégorielles
- interprétable

### 7. Les faiblesses de CART

Les arbres seuls ont un défaut important :

> forte variance.
> Si on change légèrement les données, l’arbre peut changer complètement.

C’est pour ça qu’on utilise :

- Random Forest (réduit la variance)
- Boosting (réduit le biais)

### 8. Idée amusante 
Une idée amusante pour comprendre l’arbre :
un arbre de décision ressemble à un jeu de devinette scientifique.

“Est-ce que la personne est plus grande que 1m70 ?”
“Est-ce qu’elle porte des lunettes ?”

Chaque question réduit l’incertitude jusqu’à ce qu’il reste une seule réponse probable.

La science adore ce genre de stratégie : couper le chaos en morceaux plus simples