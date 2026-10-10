###  Description des variables

#### Variables pré-index : décrivent l’état clinique et l’utilisation des soins avant le traitement.

#### 1. Variables démographiques
- Âge à l’index : **index_age** : Âge du patient en années au jour du début du traitement
- Sexe féminin : **female** : Indicateur (0 ou 1) du sexe féminin
#### 2. Comorbidités pré-index (variables binaires 0/1)
- **acute_bronchitis** : si le patient a reçu un diagnostic de bronchite aiguë l'année précédente
- **acute_laryngitis** : si le patient a reçu un diagnostic de laryngite aiguë l'année précédente
- **gerd** : si le patient a reçu un diagnostic de reflux gastro-œsophagien l'année précédente
- **pneumonia** : si le patient a reçu un diagnostic de pneumonie l'année précédente
- **rhinitis** : si le patient a reçu un diagnostic de rhinite l'année précédente
- **sinusitis** : si le patient a reçu un diagnostic de sinusite l'année précédente
- **upper_respiratory_infection** : si le patient a reçu un diagnostic d'infection des voies respiratoires supérieures l'année précédente

#### 2. Intensité de la prise en charge et utilisation des traitements
- **total_pre_index_cannisters_365** : Nombre total de bonbonnes de SABA (Bêta-2 Agoniste à Courte Action) durant l'année précédant la date index. Les SABA sont administrés pour soulager rapidement les symptômes de l'asthme. Une utilisation accrue indique un asthme plus sévère. *Note : il s'agit d'une distribution tronquée en raison des critères de sélection des patients*

- **pre_asthma_days** : Nombre de jours de traitement de l’asthme au cours de l’année pré-index . 

#### 3. Coûts de santé pré-index
- **pre_asthma_charge** : Total des frais non pharmaceutiques liés à l'asthme durant les 6 mois précédant la date index
- **pre_asthma_pharma_charge** : Total des frais pharmaceutiques liés à l'asthme durant l'année précédant la date index
- **total_pre_index_charge** : Total des frais durant l'année précédant la date index

Transformations logarithmiques des coûts :
  - **log_charges** : Logarithme de total_pre_index_charge : réduit l’asymétrie 
  - **log_asthma_charge** : Logarithme de pre_asthma_charge : réduit l’asymétrie


#### 4. Variable post-index (Variable d'intérêt)

- **post_index_exacerbations365** : **Variable dépendante**. Nombre d'exacerbations durant l'année suivant le début du traitement

#### 5. Variable non decrite 
- **drug_s**
- **previous_asthma_drugs** : qui indique si le patient prenait déjà un traitement de  pour l’asthme avant la date index