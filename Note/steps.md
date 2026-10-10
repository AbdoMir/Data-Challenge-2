***Préparation des données :*** 
- diviser l'ensemble de données en deux parties : une pour l'entraînement et l'autre pour la validation et les tests
- gestion des valeurs 
- normalisation des caractéristiques et l'encodage des variables

***Formation des modèles :***
- Bagging 
- Entrainement du modele sur l ensemble des donnée d'entrainement 
- le modèle apprend à corréler les caractéristiques d'entrée avec la variable cible (**Exacerbation**). 

***Optimisation des hyperparamètres :*** 
- Les hyperparamètres sont des paramètres de configuration qui déterminent le fonctionnement d'un modèle d'apprentissage automatique et peuvent avoir un impact significatif sur ses performances.
- Méthode utilisé pour la recherche d'hyperparametre : **grid search, random search, or Bayesian optimization**

***Évaluation des performances du modèle :***
- Évaluez les performances du modèle sur l'ensemble de validation afin de déterminer son potentiel sur de nouvelles données (test)
- Les métriques couramment utilisées pour cette évaluation incluent l'accuracy, la précision, le rappel et le **score F1** pour les tâches de classification

***Choisir le meilleur modèle :***
- Choisir le modèle ou la configuration qui offre les meilleures performances sur l'ensemble de validation
- car le modèle le plus performant sur les données d'entraînement ne l'est pas forcément sur des données inconnues. 

***Phase finale de test :***
- Une fois le modèle sélectionné en fonction de ses performances lors des tests de validation, il est soumis à des tests à l'aide d'un ensemble de données de test afin d'évaluer son efficacité en situation réelle.