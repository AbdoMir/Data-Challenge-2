**La régression de Poisson** est une méthode statistique spécialement conçue pour ***les données de comptage***. 
> Contrairement à la régression linéaire, qui peut prédire des valeurs négatives, la régression de Poisson garantit que les prédictions restent des nombres entiers non négatifs. Il est donc particulièrement utile dans tous les domaines où le comptage des événements est essentiel à la prise de décision.

> Les données de comptage présentent plusieurs propriétés particulières qui rendent la régression linéaire ordinaire inappropriée :

- Les entiers non négatifs seulement: Les nombres ne peuvent pas être négatifs ou fractionnaires. 
- Distribution asymétrique: La plupart des ensembles de données de comptage présentent de nombreuses valeurs faibles avec des valeurs élevées occasionnelles, créant ainsi une queue droite caractéristique.
- La variance augmente avec la moyenne: Plus le nombre moyen d'échantillons est élevé, plus la variabilité augmente. 

> La régression de Poisson s'appuie sur la distribution de probabilité de Poisson, qui décrit naturellement les données de comptage. La distribution de Poisson a un seul paramètre (lambda) qui représente à la fois la moyenne et la variance des comptages. Cette propriété d'égalité de la moyenne-variance, appelée équidispersion, est une hypothèse clé que nous devrons vérifier dans la pratique.

La distribution excelle dans la modélisation d'événements "rares" - pas nécessairement peu fréquents, mais des événements dont chaque occurrence est indépendante et dont le taux reste relativement constant dans des conditions similaires.