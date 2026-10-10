
1. Intro
- Dans le domaine de la santé, l'émergence des entrepôts de données biomédicales et des
dossiers médicaux électroniques a accru l'attention portée à la sensibilité des données.

- Rocher et al. ont montré que 99,98 % des personnes pouvaient être réidentifiées dans n'importe quel ensemble de données pseudonymisées à l'aide de 15 attributs démographiques.


- Par exemple, en 2017, Culnane et al [12] ont réidentifié un patient à partir d'un
ensemble de données de santé ouvertes australiennes anonymisées

- D'autres études portant sur divers types de données, telles que la mobilité[3-6], les données de cartes de crédit[7] et les données de navigation8, ont montré que la dé-identification est insuffisante pour protéger les données personnelles[9-11].

- Depuis 2018, la mise en œuvre du Règlement général sur la protection des données (RGPD) en Europe a considérablement modifié le cadre réglementaire de la circulation et de l'utilisation des données personnelles,
en favorisant notamment une utilisation plus systématique des techniques d'anonymisation.

- Bien que le partage de données soit fondamental pour la recherche, la réidentification de la santé des patients[11] et des informations permettant de les identifier individuellement constitue une menace et un facteur limitant.

2. Les données pseudonymisées : 

Attention ne pas comfondre **anonymisation et pseudonymisation**
- Toutes les informations permettant d'identifier directement une personne (par exemple, nom, numéro de téléphone, numéro de sécurité sociale) sont supprimées afin de prévenir tout risque d'identification directe du patient.
- Cependant, le risque de réidentification demeure et est souvent non quantifié. La pseudonymisation n'est pas un type d'anonymisation.

3. Les données anonymes
Selon le RGPD : les données anonymes sont définies comme « des informations qui ne se rapportent pas à une personne physique identifiée ou identifiable ou à des données à caractère personnel rendues anonymes de telle sorte que la personne concernée ne soit pas ou ne soit plus identifiable. 

4. Evaluer robustesse de l anonymisation 

Le Comité européen de la protection des données (CEPD) propose trois principes pour évaluer la robustesse d'un processus d'anonymisation :

(1) identifier un patient au sein d'un ensemble de données, (2) faire correspondre des enregistrements provenant de différentes sources de données, et (3) déduire le véritable résultat pour le patient.

Pour répondre à ces exigences légales et de confidentialité, les techniques d'anonymisation constituent des solutions pertinentes pour la protection des données.

5. Techniques d anonyma 

La recherche scientifique a permis de développer diverses techniques d'anonymisation ***(ajout de bruit ;
substitution ; agrégation ou K-anonymat; L-diversité; confidentialité différentielle; hachage/tokenisation). Parmi elles, la confidentialité différentielle*** 

La ε-differential privacy fournit une garantie mathématique du niveau de confidentialité.
Limites :
- nécessite l’accès aux données originales
- principalement conçue pour produire des statistiques, pas des datasets complets.

6. Alternative : données synthétiques

Définies comme des données non issues de mesures réelles.
Générées aléatoirement via des modèles imitant :
- un dataset réel
- ou des observations individuelles

7. Avantages des données synthétiques

- Similarité structurelle
    - même nombre d’observations
    - mêmes variables et types
- Pertinence informationnelle
    - résultats analytiques comparables aux données réelles
- Indiscernabilité
    - ni experts ni algorithmes ne peuvent les distinguer des vraies données

8. Progrès récents
Amélioration grâce à :
- machine learning
- modélisation statistique avancée
Les données synthétiques reproduisent bien les propriétés statistiques

⚠️ Limite majeure :
- le risque de confidentialité est peu documenté
- difficile à quantifier car données générées aléatoirement

9. Méthode proposée : Avatar
- Nouvel algorithme : Avatar method
- Approche centrée patient : ***chaque observation réelle → simulation locale → un avatar synthétique***

👉 Permet une évaluation selon les critères de l’EDPB

### Résultat 

10. Préservation statistique (Avatar vs Original)

| Aspect              | Méthode            | Résultat                          | Interpration                                     |
| ------------------- | ------------------ | --------------------------------- | ------------------------------------------------ |
| Structure (FAMD)    | Avatar vs Original | Superposition totale (AIDS, WBCD) | Structure parfaitement conservée (même outliers) |
| Courbes de survie   | Avatar vs Original | Courbes superposées               | Même interprétation clinique                     |
| Hazard Ratio (AIDS) | Original           | HR = 0.49 (p = 1.22e-08)          | Référence                                        |
|                     | Avatar             | HR = 0.40 (p = 1.47e-11)          | Même conclusion (arm1 > arm0)                    |
| F-scores (WBCD)     | Avatar vs Original | Très similaires                   | Même variables importantes                       |
| AUC (WBCD)          | Original           | 99.46 ± 0.25                      | Référence                                        |
|                     | Avatar             | 99.84 ± 0.12                      | Performance équivalente (voire meilleure)        |
| Global              | Avatar             | Analyse équivalente               | Variance potentiellement réduite                 |

11. Comparaison des méthodes (Avatar vs Synthpop vs CT-GAN)

| Critère                    | Original    | Avatar        | Synthpop     | CT-GAN            | Interprétation              |
| -------------------------- | ----------- | ------------- | ------------ | ----------------- | --------------------------- |
| Conclusion clinique (AIDS) | arm1 > arm0 | ✔️            | ✔️           | ✔️                | Toutes cohérentes           |
| HR précision               | Référence   | ✔️ (dans IC)  | ✔️ (dans IC) | ❌ (sous-estimé)   | CT-GAN biaisé               |
| AUC (WBCD)                 | 99.46       | 99.84         | 99.24        | 99.95             | Toutes très bonnes          |
| F-score fidélité           | Référence   | ⭐ Très proche | Moyen        | ❌ biais variables | Avatar meilleur             |
| Biais variables            | -           | Faible        | Modéré       | Élevé             | CT-GAN surpondère variables |

12. Confidentialité (Privacy Metrics)

**DCR (Distance to Closest Record)**

| Dataset | Original | Avatar | Synthpop | CT-GAN | Interprétation                      |
| ------- | -------- | ------ | -------- | ------ | ----------------------------------- |
| AIDS    | 3        | 2.04   | 2.9      | 4.43   | CT-GAN = + privé, Avatar = + proche |
| WBCD    | 0.45     | 0      | 0.36     | 1.46   | Avatar très proche des données      |

**NNDR (Nearest Neighbor Distance Ratio)**

| Dataset | Original | Avatar | Synthpop | CT-GAN | Interprétation       |
| ------- | -------- | ------ | -------- | ------ | -------------------- |
| AIDS    | 0.91     | 0.8    | 0.9      | 0.95   | Toutes sécurisées    |
| WBCD    | 0.97     | 1      | 0.96     | 0.94   | Aucune fuite directe |


13. Méthode des avatars et évaluation du risque de réidentification avec une métrique centrée sur le patient

La méthode Avatar, grâce à son approche centrée sur le patient, permet de calculer des métriques spécifiques de confidentialité qui ne sont pas applicables aux méthodes de génération de données synthétiques basées sur des modèles globaux. Une de ces métriques est le local cloaking, qui mesure la distance entre une observation réelle et son avatar en termes de nombre d’avatars intermédiaires.

| Aspect                             | Dataset | Résultat                         | Interprétation                                              |
| ---------------------------------- | ------- | -------------------------------- | ----------------------------------------------------------- |
| Median local cloaking              | AIDS    | 11                               | En moyenne, 11 avatars entre un individu réel et son avatar |
|                                    | WBCD    | 24                               | Protection encore plus forte (distance plus grande)         |
| Hidden rate                        | AIDS    | 93%                              | 93% des individus sont bien protégés                        |
|                                    | WBCD    | 94%                              | Protection très élevée                                      |

Dans les deux datasets, moins de 7 % des individus présentent un risque potentiel (avatar identique ou très proche).

14. Analyse de robustesse (25 générations d’avatars)

Une analyse supplémentaire sur 25 générations indépendantes d’avatars montre que la majorité des individus ne produisent pas systématiquement des avatars identiques.

Pour le dataset AIDS, seuls 28,2 % des individus n’ont jamais un local cloaking nul, tandis que pour WBCD, cette proportion atteint 85,5 %. Les cas extrêmes sont très rares : seulement 0,1 % des individus (3 pour AIDS, 1 pour WBCD) ont généré un avatar identique au moins 10 fois sur 25 essais.

| Critère                     | Résultat                                     |
| --------------------------- | -------------------------------------------- |
| Quantification du risque    | ✔️ Possible (grâce au local cloaking)        |
| Protection individuelle     | ✔️ Assurée pour chaque patient               |
| Risque de ré-identification | Faible et mesurable                          |
| Comportement du modèle      | Aléatoire (non exploitable par un attaquant) |

Globalement, ces résultats montrent que le risque de ré-identification avec la méthode Avatar est à la fois quantifiable et faible, avec une protection assurée pour chaque individu. De plus, la génération d’un avatar très proche de l’individu réel est un phénomène aléatoire, imprévisible et non exploitable par un attaquant, ce qui renforce la robustesse du mécanisme de confidentialité.

15. Impact du paramètre k (nombre de voisins) sur la méthode Avatar

Le paramètre k (nombre de voisins) joue un rôle central dans la génération des avatars. Il influence directement le compromis entre fidélité statistique et protection des données.

Lorsque k est faible (ex. 0,2 % à 6 % des individus), les projections FAMD montrent que la structure des données est très bien conservée, similaire aux données originales. En revanche, lorsque k est élevé (50 % à 55 %), la structure devient moins nette : les frontières et l’organisation des données s’atténuent progressivement.

Concernant les performances statistiques, les estimations des indicateurs principaux (Hazard Ratio pour AIDS et AUC pour WBCD) restent dans l’intervalle de confiance des données originales pour une plage de k relativement large :

- entre 4 et 750 pour AIDS
- entre 4 et 150 pour WBCD

Cependant, il existe un biais lié à k :

- k faible → surestimation des effets
- k élevé → sous-estimation des effets

Du point de vue de la confidentialité, le local cloaking évolue avec k :

- k faible → cloaking faible et dense (avatars plus proches des données réelles)
- k élevé → cloaking plus élevé (meilleure protection)

Ainsi, une augmentation de k entraîne :

- une meilleure protection des individus
- mais une dégradation progressive de la structure des données et de la précision des estimations

Malgré cela, même pour des valeurs élevées de k, la pertinence statistique reste acceptable.


