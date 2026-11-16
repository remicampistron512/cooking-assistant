## **1. Présentation générale du projet**

### **1.1. Contexte**

Cooking Assistant est un projet d'application web destiné à faciliter la recherche, la création, la gestion et la préparation de recettes de cuisine.

### **1.2. Objectifs**

Il s'agit de créer une application web permettant combinant efficacement des fonctionnalités retrouvées sur des sites comme myfitnesspal, marmiton ou bien cuisine AZ

Les objectifs secondaires sont de :

1. Centraliser un catalogue de recettes et d'ingrédients.    
2. Calculer automatiquement les calories et macronutriments.    
3. Permettre la création de recettes à partir d'ingrédients existants.    
4. Proposer des recettes selon les ingrédients disponibles.    
5. Organiser les repas et générer des listes de courses.    
6. Faciliter l'enrichissement du catalogue par importation et intelligence artificielle.    
7. Proposer une interface responsive, accessible et entièrement francophone.
## 2. **Public cible et utilisateurs**

Trois profils d'utilisateurs sont envisagés.

| Profil              | Description                       | Principales possibilités                                                                |
| ------------------- | --------------------------------- | --------------------------------------------------------------------------------------- |
| Visiteur            | Utilisateur non authentifié       | Consulter et rechercher les recettes publiques, consulter les ingrédients               |
| Utilisateur inscrit | Utilisateur disposant d'un compte | Créer des recettes, gérer ses favoris, son garde-manger, ses repas et ses courses       |
| Administrateur      | Responsable de la plateforme      | Administrer les recettes, ingrédients, utilisateurs, imports et traitements automatisés |

Les visiteurs pourront consulter le contenu public sans obligation de créer un compte. Les fonctionnalités personnelles nécessiteront une authentification.

## **3. Périmètre fonctionnel**


Le projet sera développé de manière progressive, afin de distinguer les fonctionnalités indispensables des évolutions avancées.

**Lot 1 — Socle fonctionnel** (Prioritaire)

Gestion des utilisateurs, catalogue d'ingrédients, recherche de recettes, création et modification de recettes, étapes de préparation, calcul des calories et macronutriments, administration.

**Lot 2 — Assistance culinaire** (évolution)

Garde-manger, recherche de recettes réalisables, conversion des unités, adaptation des portions, favoris, planificateur de repas et listes de courses.

**Lot 3 — Automatisation et IA** (avancé)

Importation de recettes externes, rapprochement intelligent des ingrédients, génération d'images culinaires, création de recettes assistée par IA et reconnaissance de recettes à partir de photographies.

Cette répartition constitue une proposition de priorisation. Elle devra être validée avant la réalisation.

## **4. Spécifications fonctionnelles détaillées**

### **4.1. Authentification et comptes utilisateurs**

L'application doit permettre à un visiteur de créer un compte, de s'authentifier et de gérer ses informations personnelles.

Fonctionnalités attendues :

- Inscription et connexion.
    
- Déconnexion.
    
- Réinitialisation du mot de passe.
    
- Consultation et modification du profil.
    
- Gestion des recettes personnelles.
    
- Gestion des préférences et favoris.
    

**Règles de gestion  :
-  un utilisateur ne peut modifier ou supprimer que ses propres recettes, sauf s'il dispose des autorisations administratives nécessaires.
-  une recette privée ne peut être consultée que par son propriétaire ou par un administrateur autorisé.

### **4.2. Catalogue des ingrédients**

Le catalogue s'appuie sur les données nutritionnelles importées depuis CIQUAL (provisoire)

Chaque ingrédient se compose de :

- Identifiant unique de l'ingrédient
- Identifiant d'origine pour les aliments importés
- Désignation française de l'aliment
- Catégorie (Groupe alimentaire)
- Calories (Énergie en kcal pour 100 g)
- Protéines (Quantité en g pour 100 g)
- Glucides  (Quantité en g pour 100 g)
- Lipides (Quantité en g pour 100 g)
- Image (Illustration facultative de l'ingrédient)

Fonctionnalités

- Rechercher un ingrédient par son nom.
- Filtrer par catégorie.
- Consulter une fiche nutritionnelle.
- Sélectionner un ingrédient dans un formulaire de recette.
- Administrer les données.
- Importer les fichiers CIQUAL au format XLS.

**Règles de gestion  :
-  les données nutritionnelles doivent être exprimées dans des unités cohérentes, principalement pour 100 grammes.
- une importation ne doit pas supprimer les données existantes avant la validation du fichier source.
- le remplacement des ingrédients doit préserver l'intégrité des recettes qui les référencent. Le rapprochement par code CIQUAL et la mise à jour des enregistrements existants seront privilégiés à une suppression globale.

### **4.4. Calcul nutritionnel**

Le système calcule automatiquement les valeurs nutritionnelles à partir des quantités d'ingrédients utilisées.

Pour un ingrédient :
E = (M * C ) / 100

Avec :
- \(E\) : énergie de l'ingrédient en kcal.    
- \(M\) : masse de l'ingrédient en grammes.    
- \(C\) : énergie pour 100 g.    

L'énergie totale de la recette correspond à la somme des énergies de ses ingrédients.

Le même principe s'applique aux protéines, glucides et lipides.

**Règles de gestion :** 
- les calculs doivent être effectués côté serveur, afin de garantir leur cohérence.

### **4.5. Recherche et consultation des recettes**

Le visiteur peut rechercher des recettes à partir de leur titre, catégorie ou ingrédients.

Filtres envisagés :
- Temps de préparation.    
- Calories par portion.    
- Ingrédients présents.    
- Catégorie de recette.    
- Teneur en protéines/glucides/lipides   
- Recettes favorites, pour les utilisateurs authentifiés.    

Les résultats sont présentés sous forme de cartes contenant une image, un titre, une durée et des informations nutritionnelles.

### **4.6. Garde-manger personnel**

L'utilisateur peut enregistrer les ingrédients dont il dispose, ainsi que leurs quantités.

Le système doit permettre d'ajouter, modifier et retirer des ingrédients du garde-manger.

Une fonction « Que puis-je cuisiner ? » propose des recettes selon les ingrédients disponibles.

Les résultats distinguent les recettes entièrement réalisables et celles nécessitant des ingrédients supplémentaires.


### **4.7. Planification des repas**

Un calendrier hebdomadaire permet d'affecter des recettes aux différents repas de la semaine.

L'utilisateur peut :

- Naviguer entre les semaines.    
- Ajouter ou retirer une recette.    
- Modifier le nombre de portions prévues.    
- Consulter le récapitulatif des repas.    
- Générer une liste de courses à partir du planning.    

### **4.8. Listes de courses**

Les listes de courses peuvent être créées manuellement ou automatiquement à partir des recettes planifiées.

Le système doit regrouper les ingrédients identiques, convertir les unités compatibles et présenter les articles par catégorie.

L'utilisateur peut cocher les produits achetés, ajuster les quantités et supprimer des lignes.

### **4.9. Importation de recettes externes**

L'administrateur peut importer des recettes depuis une source externe autorisant cet usage, via une API documentée.

Le processus comprend :

1. Récupération des données de la recette.    
2. Extraction des ingrédients et quantités.    
3. Rapprochement avec le catalogue CIQUAL.    
4. Conversion des mesures.    
5. Création des étapes de préparation.    
6. Vérification et enregistrement.    

Le système conserve l'identifiant externe, la source et les informations d'attribution nécessaires.


**Règles de gestion :** 
* les recettes importées ne doivent pas être dupliquées lors de plusieurs exécutions.
- les droits de réutilisation des contenus importés doivent être vérifiés avant leur conservation et leur publication.

### **4.10. Génération d'images culinaires par IA**

Un utilisateur peut générer une illustration de sa recette à partir de son titre, de ses ingrédients et d'une description.

Le service de génération doit permettre de choisir un style photographique et de prévisualiser le résultat.

L'architecture doit rester indépendante du fournisseur afin de pouvoir intégrer différents modèles, notamment Ideogram ou FLUX.

L'image validée est associée à la recette et conservée dans un espace de stockage maîtrisé par l'application.

**Règles de gestion :** 
* une image générée doit être identifiable comme telle. Elle ne doit pas être présentée comme une photographie authentique du plat réellement préparé.

### **4.11. Administration**

L'administration doit permettre de gérer :

- Les utilisateurs et leurs droits.    
- Les ingrédients et leurs données nutritionnelles.    
- Les recettes et leur visibilité.    
- Les imports CIQUAL.    
- Les imports de recettes externes.    
- Les associations entre ingrédients externes et ingrédients CIQUAL.    
- Les générations d'images.    
- Les erreurs et historiques des traitements automatisés.   

Un tableau de bord administratif personnalisé pourra présenter les indicateurs suivants : nombre de recettes, nombre d'utilisateurs, nombre d'ingrédients, imports récents et traitements en échec.

Le socle d'administration s'appuiera sur Django Admin.

## **5. Arborescence du site**

- **Accueil**
    
- **Recettes**
    
    Catalogue et recherche
    
    Détail d'une recette
    
    Créer / modifier une recette
    
    Mes recettes et favoris
    
- **Ingrédients**
    
    Catalogue CIQUAL
    
    Fiche nutritionnelle
    
- **Garde-manger**
    
    Mes ingrédients
    
    Que puis-je cuisiner ?
    
- **Planificateur de repas**
    
- **Liste de courses**
    
- **Compte utilisateur**
    
- **Administration**

## **6. Architecture technique**

### **6.1. Technologies retenues**

| Composant                | Technologie                          |
| ------------------------ | ------------------------------------ |
| Langage backend          | Python 3.12 ou supérieur             |
| Framework                | Django 6                             |
| Interface                | HTML5, CSS3, JavaScript              |
| Framework CSS            | Bootstrap 5 avec `django-bootstrap5` |
| Moteur de rendu          | Django Templates                     |
| Base de données initiale | SQLite                               |
| Authentification         | Django Authentication                |
| Conteneurisation         | Docker et Docker Compose             |
| Import Excel             | pandas et xlrd                       |
| Gestion des versions     | Git                                  |
| Tests                    | Django TestCase                      |
| Maquettes                | Figma                                |

Pour une mise en production à plus grande échelle, une migration vers PostgreSQL pourra être étudiée.

### **6.2. Architecture applicative**

**Interface web responsive**

Bootstrap 5 · Django Templates · JavaScript

**Application Django**

Vues · Formulaires · Authentification · Services métier

**Base de données**

SQLite / PostgreSQL

**Stockage média**

Photos et fichiers

**CIQUAL**

Importation XLS

**Services externes**

Recettes · Images IA

Les traitements volumineux (importation de milliers d'ingrédients, génération d'images, rapprochement des ingrédients) pourront être exécutés en arrière-plan à l'aide d'une file de tâches, par exemple Celery et Redis.

### **6.3. Modèle de données prévisionnel**

Les principales entités sont :

|Entité|Rôle|
|---|---|
|User|Compte utilisateur|
|Ingredient|Référentiel des aliments|
|Recipe|Informations générales d'une recette|
|RecipeIngredient|Association entre recette et ingrédient, avec quantité et unité|
|RecipeStep|Étapes ordonnées de préparation|
|Favorite|Association entre utilisateur et recette favorite|
|PantryItem|Ingrédient disponible dans le garde-manger|
|MealPlan|Planification des repas|
|PlannedMeal|Recette affectée à un créneau|
|ShoppingList|Liste de courses|
|ShoppingListItem|Article d'une liste|
|IngredientAlias|Correspondances et synonymes d'ingrédients|
|ImageGeneration|Suivi des générations d'images|

Les entités du socle seront implémentées en priorité. Les autres seront introduites progressivement, selon les lots retenus.

## **7. Identité graphique et ergonomie**

L'identité visuelle doit évoquer une cuisine naturelle, accessible et contemporaine, inspirée des légumes frais et des couleurs méditerranéennes.

Vert botanique  \#476B2D

Vert feuille \#88A83F

Rouge tomate \#D74732

Orange carotte \#E78B35

Jaune doré \#E9BE43

Aubergine \#493446

Crème \#F7F1DE

Olive foncé \#283324

La typographie envisagée associe **Fraunces** pour les titres et **DM Sans** pour les textes d'interface.

Les principes ergonomiques sont les suivants : 
- interface responsive
- navigation claire
- photographies culinaires mises en valeur
- formulaires accessibles,
- hiérarchie visuelle cohérente 
- Respect des critères WCAG 2.2 AA dans la mesure du possible.

Les maquettes seront réalisées sous Figma, et déclinaisons desktop/mobile.

## 8. Hébergement et déploiement

### 8.1. Stratégie d'hébergement

Dans un premier temps, l'application Cooking Assistant sera hébergée sur un **serveur privé**, afin de disposer d'un environnement de déploiement maîtrisé et de limiter les coûts d'exploitation.

À moyen ou long terme, une migration vers une infrastructure cloud, notamment **Amazon Web Services (AWS)** avec une instance **EC2**, pourra être envisagée afin d'améliorer les capacités d'hébergement, la disponibilité et l'évolutivité de l'application.

Cette migration dépendra notamment de l'augmentation du nombre d'utilisateurs, du volume de données et des besoins en ressources matérielles.


### 8.2. Environnement technique

L'application sera déployée  sur un serveur Linux Ubuntu Server.

L'environnement d'hébergement comprendra :

- **Docker et Docker Compose** pour la conteneurisation et l'orchestration des services.    
- **Gunicorn** comme serveur d'application WSGI pour Django.    
- **Caddy** comme reverse proxy pour la gestion des requêtes HTTP/HTTPS et la distribution des fichiers statiques.    
- **SQLite** pour la phase initiale, avec une migration possible vers PostgreSQL pour un usage multiutilisateur plus important.    
- Un espace de stockage persistant pour les fichiers médias, notamment les photographies des recettes et des ingrédients.    

La configuration des différents services devra permettre leur mise à jour et leur redémarrage indépendamment lorsque cela est possible.

### 8.3. Sécurité

L'hébergement devra respecter les bonnes pratiques de sécurité :

- Mise en place d'un certificat TLS pour sécuriser les communications HTTPS.    
- Configuration du pare-feu afin de limiter les ports accessibles.    
- Accès administratif au serveur par SSH avec authentification par clé.    
- Stockage des informations sensibles (clés d'API, mots de passe, secrets Django) dans des variables d'environnement ou un gestionnaire de secrets.    
- Désactivation du mode DEBUG de Django en production.    
- Mise à jour régulière du système d'exploitation et des dépendances.    
- Restriction des accès à la base de données et aux services internes.    
- Sauvegardes régulières de la base de données et des fichiers médias.
### 8.4. Déploiement et maintenance

Le code source sera versionné avec Git.

Le déploiement de l'application reposera sur des conteneurs Docker afin de garantir la reproductibilité de l'environnement entre le développement et la production.

Une procédure de déploiement documentée devra permettre :

1. La récupération de la version souhaitée du code source.    
2. La construction ou la récupération des images Docker.    
3. La configuration des variables d'environnement.    
4. L'exécution des migrations de la base de données.    
5. La collecte des fichiers statiques Django.    
6. Le démarrage ou la mise à jour des services.    
7. La vérification du bon fonctionnement de l'application.    

Cette automatisation de déploiement s'implémentera grâce aux actions GitHub
À terme, une chaîne d'intégration et de déploiement continus (CI/CD) pourra automatiser les tests, la construction des images et le déploiement.

### 8.5. Sauvegarde et restauration

Une stratégie de sauvegarde sera mise en place afin de prévenir les pertes de données.

Elle devra couvrir :

- La base de données.    
- Les images et fichiers téléversés.    
- Les fichiers de configuration nécessaires à la restauration, à l'exclusion des secrets non chiffrés.  

Les sauvegardes devront être conservées sur un support distinct du serveur principal.
Une procédure de restauration sera documentée et testée périodiquement.

### 8.6. Évolutivité et migration vers AWS

L'architecture devra faciliter une éventuelle migration vers AWS.

Cette évolution pourra comprendre :

- **Amazon EC2** pour l'hébergement de l'application Django conteneurisée.    
- **Amazon RDS** pour l'hébergement d'une base PostgreSQL managée.
- **Amazon S3** pour le stockage des images et fichiers médias.    
- **Amazon CloudWatch** pour la centralisation des journaux et la supervision.    

La migration devra être réalisable avec un minimum de modifications du code applicatif, notamment grâce à la conteneurisation et à la séparation entre configuration, données et logique métier.
### 8.7. Supervision et disponibilité

Des mécanismes de supervision comme le logiciel Grafana devront permettre de vérifier le bon fonctionnement de l'application.

Les principaux éléments surveillés seront :

- La disponibilité du site.    
- L'utilisation du processeur et de la mémoire.    
- L'espace disque disponible.    
- Les erreurs applicatives Django.    
- L'état des conteneurs Docker.    
- Le bon déroulement des sauvegardes.    

En cas d'incident, les journaux techniques devront permettre d'identifier la cause du dysfonctionnement et de faciliter sa résolution.


## **9. Exigences non fonctionnelles**

- Responsive, le site doit fonctionner pour des résolutions allant du smartphone aux écrans 4k
- Le site doit être accessible avec assez de contraste, une navigation au clavier et des libellés de formulaires clairs
- L'application se doit d'être performante (audit lighthouse)
- Protection CSRF, validation serveur et contrôle des autorisations
- Transactions atomiques pour les imports critiques
- Gestion des erreurs et messages explicites
- Séparation entre vues, modèles et services métier
- Tests automatisés des règles métier essentielles
- Protection des données personnelles et contrôle de visibilité

## **10. Sécurité et conformité**

L'application devra appliquer les principes suivants :

- Stockage sécurisé des mots de passe via Django.    
- Contrôle d'accès aux recettes privées.    
- Protection contre les attaques CSRF et les injections.    
- Validation systématique des données reçues.    
- Vérification des fichiers téléchargés (taille, type, contenu).    
- Protection des clés d'API dans des variables d'environnement.    
- Respect des droits d'utilisation des recettes et photographies importées.    
- Gestion des données personnelles conformément aux obligations applicables du RGPD.    
- Sauvegarde et restauration de la base de données.    

Les contenus générés par IA devront être identifiables et soumis à une validation utilisateur avant leur publication.

## **11. Tests et recette fonctionnelle**

Les tests devront couvrir les principales fonctionnalités ainsi que les cas d'erreur.

| Scénario                                                    | Résultat attendu                                            |
| ----------------------------------------------------------- | ----------------------------------------------------------- |
| Un utilisateur s'inscrit                                    | Le compte est créé                                          |
| Un utilisateur crée une recette                             | La recette et ses relations sont enregistrées               |
| Un utilisateur sélectionne un ingrédient CIQUAL             | L'ingrédient existant est réutilisé                         |
| Une recette contient 200 g d'un ingrédient à 100 kcal/100 g | La contribution calculée est de 200 kcal                    |
| Le nombre de portions change                                | Les valeurs par portion sont recalculées                    |
| Un autre utilisateur tente de modifier une recette privée   | L'accès est refusé                                          |
| Un fichier CIQUAL invalide est importé                      | L'import échoue sans perte de données                       |
| Une recette externe est importée deux fois                  | Aucun doublon n'est créé                                    |
| Le garde-manger ne contient qu'une partie des ingrédients   | Les ingrédients manquants sont indiqués                     |
| Une liste de courses est générée                            | Les ingrédients identiques sont regroupés après conversion  |
| Une génération d'image échoue                               | Une erreur est affichée sans perte de recette               |
| L'application est utilisée sur mobile                       | L'interface reste fonctionnelle sans débordement horizontal |
## **12. Livrables attendus**

À l'issue du projet, les livrables envisagés sont :

1. Cahier des charges fonctionnel et technique validé.    
2. Maquettes Figma.    
3. Modèle conceptuel et logique des données.    
4. Code source versionné sous Git.    
5. Application Django fonctionnelle.    
6. Scripts d'importation CIQUAL.    
7. Tests automatisés et résultats de recette.    
8. Configuration Docker et Docker Compose.    
9. Documentation technique et guide d'installation.    
10. Guide utilisateur et documentation d'administration.    
11. Application déployée dans un environnement de démonstration.

## **13. Risques et contraintes**

|Risque|Impact|Mesure envisagée|
|---|---|---|
|Hétérogénéité des données CIQUAL|Erreurs d'importation|Normalisation, validation et tests|
|Ingrédients difficiles à rapprocher|Calculs incomplets|Synonymes, scores de correspondance et validation humaine|
|Conversion des unités imprécise|Erreurs nutritionnelles|Conversions spécifiques aux ingrédients|
|Droits de réutilisation des recettes|Risque juridique|Vérification des licences et conditions des API|
|Coût des API d'IA|Dépassement budgétaire|Quotas, journalisation et limites d'utilisation|
|Complexité du périmètre|Retard de livraison|Développement par lots et priorisation|
|Performances sur un grand catalogue|Recherche lente|Indexation et pagination|
|Dépendance aux services externes|Indisponibilité|Gestion des échecs et mécanismes de reprise|
## **14. Critères d'acceptation du projet**

Le **socle fonctionnel** pourra être considéré comme livré lorsque les conditions suivantes seront remplies :

- Un utilisateur peut s'inscrire et se connecter.    
- Le catalogue CIQUAL français est consultable et recherchable.    
- Un utilisateur peut créer une recette à partir d'ingrédients existants.    
- Les quantités et étapes sont correctement enregistrées.    
- Les calories et macronutriments sont calculés correctement.    
- Les recettes privées sont protégées.    
- Les recettes publiques sont consultables.    
- L'administration permet de gérer les données essentielles.    
- Les tests critiques sont exécutés avec succès.    
- L'application fonctionne dans un environnement Docker documenté.    
- L'interface est exploitable sur ordinateur et smartphone.