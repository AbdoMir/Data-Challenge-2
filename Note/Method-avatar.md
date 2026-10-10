### Method Avatar

La méthode Avatar repose sur une approche centrée sur le patient, où chaque observation réelle génère 
sa propre simulation locale appelée avatar. Le point de départ est un dataset tabulaire pseudonymisé de 
dimension n × p, où n représente les individus et p les variables, pouvant être continues, catégorielles,
booléennes ou des dates. L’objectif est de produire un nouveau dataset synthétique de même taille et structure, 
mais contenant des valeurs différentes tout en restant statistiquement cohérentes.

La méthode se déroule en trois grandes étapes. 

- Premièrement, les données d’entrée sont projetées dans un espace multidimensionnel complet à l’aide de techniques d’analyse factorielle comme la PCA, la FAMD ou la MCA. Cette transformation permet de représenter les individus dans un espace mathématique où les distances sont significatives.

- Deuxièmement, pour chaque individu, on calcule les distances avec tous les autres afin d’identifier ses k plus  proches voisins à l’aide de l’algorithme KNN. Ces voisins définissent une zone locale autour de l’individu. Un avatar est ensuite généré en réalisant une combinaison pondérée de ces voisins. Les poids attribués à chaque voisin dépendent de trois composantes : l’inverse de la distance (favorisant les voisins proches), un facteur aléatoire suivant une distribution exponentielle, et un facteur de contribution décroissante basé sur un ordre aléatoire. Ces poids sont ensuite normalisés pour obtenir une somme égale à 1. L’avatar est finalement positionné au centre pondéré des voisins dans l’espace multidimensionnel.

- Troisièmement,les avatars générés dans l’espace multidimensionnel sont transformés inversement pour revenir au format initial des données (variables originales), en appliquant la transformation mathématique inverse de l’analyse factorielle utilisée. Les observations synthétiques sont ensuite mélangées (shuffling) afin de supprimer tout lien direct entre un individu réel et son avatar.

La méthode est contrôlée par deux types de paramètres. Les premiers concernent l’environnement local : choix de la distance (euclidienne ou Mahalanobis), nombre de voisins k, paramètres de projection (standardisation, nombre de dimensions retenues, pondération des variables). Les seconds contrôlent le niveau de stochasticité de la génération : distribution des poids entre voisins (équilibrée ou non) et degré de perturbation appliqué à chaque variable.

Ainsi, la méthode Avatar combine projection multidimensionnelle, modélisation locale et génération aléatoire contrôlée pour produire des données synthétiques réalistes, tout en permettant un ajustement fin du compromis entre fidélité statistique et protection de la vie privée.

### Metrics de confidentialité 
Après la génération des données synthétiques, il est nécessaire d’évaluer leur niveau de confidentialité à l’aide de métriques spécifiques. Deux métriques classiques issues de la littérature sont utilisées : le DCR (Distance to Closest Record) et le NNDR (Nearest Neighbor Distance Ratio).

Le DCR correspond à la distance euclidienne entre chaque observation synthétique et son voisin réel le plus proche. Plus cette distance est élevée, plus le niveau de confidentialité est important, car cela signifie que les données synthétiques sont éloignées des données originales.

Le NNDR, quant à lui, est le rapport entre la distance au plus proche voisin réel et la distance au deuxième plus proche voisin réel pour chaque observation synthétique. Cette métrique est comprise entre 0 et 1. Une valeur élevée indique une meilleure confidentialité, car elle signifie que l’observation synthétique n’est pas particulièrement proche d’un individu réel spécifique.

Parmi les critères définis par l’EDPB, le risque de singling out (identification d’un individu unique) représente le scénario d’attaque le plus critique. Pour répondre à ce problème, la méthode Avatar introduit deux métriques spécifiques : le local cloaking et le hidden rate, qui exploitent le caractère local de la génération des avatars.

Dans un scénario d’attaque par appartenance (membership attack), un attaquant cherche à déterminer si un individu fait partie d’un dataset en établissant un lien entre une observation réelle et une observation synthétique. L’attaque la plus probable est basée sur la distance (distance-based linkage attack).

Le local cloaking mesure, pour chaque individu réel, le nombre d’avatars qui sont plus proches de cet individu que son propre avatar généré. Plus cette valeur est élevée, plus il est difficile d’identifier correctement l’avatar correspondant à un individu donné, ce qui améliore la confidentialité.

Le hidden rate correspond au pourcentage d’individus pour lesquels leur avatar n’est pas l’observation synthétique la plus proche. Cette métrique évalue la probabilité qu’un attaquant se trompe en associant un avatar à un individu. Un taux élevé indique une meilleure protection.

Globalement, pour toutes ces métriques (DCR, NNDR, local cloaking et hidden rate), des valeurs élevées traduisent un meilleur niveau de confidentialité. Ensemble, elles permettent d’évaluer à la fois la distance globale entre données réelles et synthétiques, et la résistance à des attaques ciblées de ré-identification.