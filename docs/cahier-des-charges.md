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
5. Faciliter l'enrichissement du catalogue par l'importation de recettes externes.
6. Proposer une interface responsive, accessible et entièrement francophone.
## 2. **Public cible et utilisateurs**

Trois profils d'utilisateurs sont envisagés.

| Profil              | Description                       | Principales possibilités                                                                |
| ------------------- | --------------------------------- | --------------------------------------------------------------------------------------- |
| Visiteur            | Utilisateur non authentifié       | Consulter et rechercher les recettes publiques, consulter les ingrédients               |
| Utilisateur inscrit | Utilisateur disposant d'un compte | Créer des recettes, gérer ses favoris et son garde-manger                               |
| Administrateur      | Responsable de la plateforme      | Administrer les recettes, ingrédients, utilisateurs, imports et traitements automatisés |

Les visiteurs pourront consulter le contenu public sans obligation de créer un compte. Les fonctionnalités personnelles nécessiteront une authentification.

## **3. Périmètre fonctionnel**


Le projet sera développé de manière progressive, afin de distinguer les fonctionnalités indispensables des évolutions avancées.

**Lot 1 — Socle fonctionnel** (Prioritaire)

Gestion des utilisateurs, catalogue d'ingrédients, recherche de recettes, création et modification de recettes, étapes de préparation, saisie des quantités en grammes ou en kilogrammes, calcul des calories et macronutriments, administration.

**Lot 2 — Assistance culinaire** (évolution)

Garde-manger, recherche de recettes réalisables, conversions au-delà des unités de masse du lot 1, adaptation des portions et favoris.

**Lot 3 — Importation de recettes externes** (avancé)

Importation de recettes externes et rapprochement de leurs ingrédients avec le catalogue CIQUAL.

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
    

### **4.2. Catalogue des ingrédients**

Le catalogue s'appuie sur les données nutritionnelles importées depuis CIQUAL.

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
- Importer et mettre à jour le catalogue CIQUAL depuis l'administration.

#### **4.2.1. Format du fichier d'import**

- Le lot 1 accepte un classeur Excel `.xls` préparé à partir des données CIQUAL. Le format d'entrée de l'application comporte une feuille `Ingredients`, une ligne d'en-têtes et une ligne par ingrédient. L'administrateur renseigne la version ou la date de publication des données source lors de l'import.
- Les colonnes requises sont `code_ciqual`, `designation_fr`, `groupe_alimentaire`, `energie_kcal_100g`, `proteines_g_100g`, `glucides_g_100g` et `lipides_g_100g`. L'ordre des colonnes est libre. Le code CIQUAL est traité comme un texte afin de conserver ses éventuels zéros initiaux.
- Le code, la désignation et le groupe doivent être renseignés. Les colonnes nutritionnelles doivent être présentes et peuvent contenir une valeur inconnue selon la section 4.3.3. L'énergie est exprimée en kcal et les macronutriments en grammes, dans les deux cas pour 100 g d'aliment.

#### **4.2.2. Validation, lignes invalides et doublons**

- Le serveur valide tout le classeur avant d'écrire dans le catalogue. Un fichier illisible, une feuille ou une colonne requise manquante, un en-tête requis dupliqué ou l'absence de lignes de données bloque l'import. Les lignes entièrement vides sont ignorées.
- Une ligne est invalide si un champ d'identification requis est vide, si une valeur nutritionnelle est négative ou si elle contient un texte que l'application ne reconnaît ni comme une valeur numérique ni comme une valeur inconnue. Le rapport indique le numéro de ligne Excel, le champ concerné et le motif de l'erreur.
- Les doublons sont identifiés par code CIQUAL après suppression des espaces en début et en fin de champ. Si les données normalisées sont identiques, une seule ligne est retenue et le doublon est signalé dans le rapport. Si les données diffèrent, l'import est bloqué et les lignes en conflit sont signalées.
- Toute erreur bloquante laisse le catalogue intact. Après validation, les changements sont appliqués dans une seule transaction. Le rapport indique la version de la source, le résultat et le nombre d'ingrédients créés, mis à jour, réactivés ou désactivés, ainsi que les doublons identiques ignorés.

#### **4.2.3. Mise à jour et ingrédients absents**

- Un code CIQUAL inconnu crée un ingrédient. Un code existant met à jour l'ingrédient associé en conservant son identifiant interne, sa photo et ses liens avec les recettes. Réimporter le même fichier dans le même mode ne crée aucun doublon et ne modifie pas les données déjà à jour.
- Par défaut, l'import est partiel. Les ingrédients absents du fichier conservent leur état et leurs données. L'administrateur peut choisir explicitement une mise à jour complète lorsqu'il dispose de l'intégralité du catalogue CIQUAL. Le rapport de validation indique combien d'ingrédients seront désactivés avant confirmation.
- Lors d'une mise à jour complète, les ingrédients CIQUAL absents sont désactivés et conservés en base avec leurs dernières valeurs nutritionnelles. Ils restent visibles dans les recettes qui les utilisent et ces recettes peuvent être modifiées en conservant leurs lignes existantes. Les ingrédients désactivés disparaissent du catalogue public et de la liste de sélection pour les nouvelles lignes. Si leur code réapparaît lors d'un import validé, ils sont mis à jour et réactivés.

### **4.3. Calcul nutritionnel**

#### **4.3.1. Unités autorisées au lot 1**

- Chaque ligne d'ingrédient accepte une quantité décimale strictement positive en grammes (g) ou en kilogrammes (kg). Le serveur convertit les kilogrammes en grammes sur la base de `1 kg = 1 000 g`.
- Les unités de volume, comme le millilitre ou la cuillère, et les quantités à la pièce sont refusées au lot 1. Leur conversion dépend de l'ingrédient et relève du lot 2.

#### **4.3.2. Calcul et arrondis**

- Le serveur calcule séparément l'énergie (kcal), les protéines, les glucides et les lipides (g). Pour chaque ligne et pour chaque valeur, la contribution est égale à `masse en g × valeur CIQUAL pour 100 g ÷ 100`.
- Le total de la recette est la somme des contributions de ses ingrédients. La valeur par portion est obtenue en divisant ce total par le nombre de portions saisi dans la recette.
- Le calcul utilise une arithmétique décimale. Les contributions et les totaux intermédiaires sont conservés sans arrondi d'affichage. Le total de la recette et la valeur par portion sont arrondis séparément à l'affichage, à l'unité pour les kcal et au dixième de gramme pour les macronutriments. À mi-chemin, on arrondit vers la valeur supérieure, par exemple `12,5 kcal → 13 kcal` et `1,25 g → 1,3 g`.
- Après la validation d'une mise à jour des valeurs CIQUAL, les calculs des recettes concernées utilisent les nouvelles valeurs.

#### **4.3.3. Valeurs CIQUAL manquantes**

- Une valeur absente ou sans quantité exacte exploitable, par exemple une cellule vide, une mention « traces » ou une valeur indiquée sous un seuil, est traitée comme inconnue. Une valeur numérique égale à zéro est conservée comme telle.
- Si une valeur est inconnue pour au moins un ingrédient, la valeur nutritionnelle correspondante est affichée comme « Non disponible » pour la recette entière et par portion. Les autres valeurs restent calculées si leurs données sont complètes. L'enregistrement de la recette reste possible.
- Lorsqu'un filtre nutritionnel est actif, une recette dont la valeur filtrée est indisponible est exclue des résultats.

### **4.4. Recherche et consultation des recettes**

Le catalogue public et sa recherche sont accessibles sans compte. Seules les recettes publiques y figurent, y compris lorsque l'utilisateur est connecté. Le propriétaire retrouve ses recettes privées dans « Mes recettes ».

#### **4.4.1. Critères disponibles au lot 1**

- **Titre.** La recherche textuelle accepte un titre complet ou partiel, sans distinction de casse ni d'accents.
- **Catégorie.** L'utilisateur peut sélectionner une catégorie dans la liste proposée par l'application.
- **Ingrédients présents.** L'utilisateur peut sélectionner un ou plusieurs ingrédients dans le catalogue CIQUAL. La recherche utilise leurs identifiants et vérifie leur présence dans la recette.
- **Temps de préparation.** L'utilisateur peut fixer une durée maximale en minutes. Le temps de cuisson est traité séparément sur la fiche et n'entre pas dans ce filtre.
- **Valeurs nutritionnelles par portion.** L'utilisateur peut renseigner une borne minimale, maximale ou les deux pour l'énergie en kcal et pour les protéines, glucides et lipides en grammes.

Le filtre « Mes favoris » est prévu au lot 2 pour les utilisateurs connectés. Il limite la recherche publique aux recettes publiques qu'ils ont ajoutées à leurs favoris. L'espace « Mes favoris » est décrit à la section 4.9.

#### **4.4.2. Combinaison des critères**

- Une recette doit satisfaire tous les critères renseignés. Si plusieurs ingrédients sont sélectionnés, elle doit les contenir tous. Les critères laissés vides sont ignorés.
- Les bornes de durée et de nutrition sont inclusives. Les filtres nutritionnels utilisent les valeurs avant arrondi d'affichage. Une recette dont la valeur filtrée est indisponible est exclue des résultats, conformément à la section 4.3.3.
- Sans critère actif, la recherche affiche le catalogue des recettes publiques. Si aucun résultat ne correspond, un message l'indique et l'utilisateur peut réinitialiser les critères.

#### **4.4.3. Tri et présentation des résultats**

- Le tri par défaut est alphabétique sur le titre, de A à Z, sans distinction de casse ni d'accents. L'utilisateur peut choisir un tri par temps de préparation croissant ou par calories par portion croissantes. Pour le tri calorique, les valeurs avant arrondi sont utilisées et les recettes dont l'énergie est indisponible apparaissent à la fin.
- Les égalités sur la durée ou les calories sont départagées par le titre, puis par l'identifiant de la recette dans l'ordre croissant. Pour le tri alphabétique, les titres équivalents sont également départagés par l'identifiant. La pagination conserve les critères et le tri sélectionnés.
- Les résultats sont présentés sous forme de cartes avec une image, le titre, le temps de préparation et les informations nutritionnelles par portion.

### **4.5. Gestion des recettes**

Un utilisateur connecté peut créer une recette et retrouver ses propres recettes dans l'espace « Mes recettes ».

#### **4.5.1. Propriétés d'une recette**

- **Titre** (obligatoire). Nom affiché sur la fiche et dans la recherche.
- **Description** (facultative). Court texte de présentation de la recette.
- **Catégorie** (obligatoire). Catégorie choisie dans la liste proposée par l'application, par exemple entrée, plat ou dessert.
- **Temps de préparation** (obligatoire). Durée en minutes, exprimée par un entier positif ou nul.
- **Temps de cuisson** (facultatif). Durée en minutes, exprimée par un entier positif ou nul. Le champ peut rester vide si la recette ne nécessite pas de cuisson.
- **Nombre de portions** (obligatoire). Entier strictement positif indiquant le nombre de portions obtenu avec les quantités saisies.
- **Ingrédients** (obligatoires). Au moins une ligne. Chaque ligne contient un ingrédient du catalogue CIQUAL, une quantité strictement positive et une unité autorisée au lot concerné (g ou kg au lot 1, voir section 4.3.1). Les quantités saisies correspondent à l'ensemble de la recette.
- **Étapes de préparation** (obligatoires). Au moins une étape. Chaque étape comporte une instruction et une position dans la liste.
- **Photo** (facultative). Image ajoutée par l'utilisateur pour illustrer la recette.
- **Visibilité** (obligatoire). Le formulaire présélectionne « Privée » à la création. L'utilisateur peut choisir « Publique » avant l'enregistrement ou changer la visibilité par la suite.
- **Identifiant et propriétaire** (attribués par le système). L'identifiant est unique et le compte qui crée la recette en devient le propriétaire.
- **Valeurs nutritionnelles** (calculées par le système). Énergie en kcal et quantités de protéines, glucides et lipides en grammes, pour la recette entière et par portion. Les valeurs par portion sont obtenues en divisant les totaux par le nombre de portions.
- **Pays d'origine** (facultatif). L'utilisateur peut sélectionner un seul pays dans une liste de noms en français ou laisser le champ vide.

#### **4.5.2. Actions dans « Mes recettes »**

- **Ajouter une recette.** Le formulaire permet de renseigner les propriétés, d'ajouter les lignes d'ingrédients et de saisir les étapes dans l'ordre. Après l'enregistrement, l'utilisateur accède à la fiche de la recette.
- **Modifier une recette.** Le formulaire reprend les données enregistrées. L'utilisateur peut les corriger, ajouter ou retirer des ingrédients, ainsi qu'ajouter, modifier, retirer ou réordonner les étapes. Les valeurs nutritionnelles sont recalculées côté serveur lorsque les ingrédients, les quantités, les unités ou le nombre de portions changent.
- **Gérer la visibilité.** Passer une recette de privée à publique ou de publique à privée depuis « Mes recettes ».
- **Supprimer une recette.** L'application demande une confirmation avant de supprimer la recette. Après confirmation, la recette est retirée de « Mes recettes » et, si elle était publique, du catalogue public.

#### **4.5.3. Règles de gestion**

- Seuls le propriétaire de la recette et les administrateurs autorisés peuvent la modifier, changer sa visibilité ou la supprimer.
- Seuls le propriétaire et les administrateurs autorisés peuvent consulter une recette privée, y compris par accès direct à sa fiche.
- Le serveur applique les règles de saisie décrites dans la liste des propriétés. En cas d'erreur, le formulaire indique les champs à corriger et conserve les données saisies. Pour une recette existante, la version précédemment enregistrée reste disponible jusqu'à la validation de la modification.

#### **4.5.4. Affichage de la fiche recette**

- La fiche affiche le titre, la catégorie, le temps de préparation, le nombre de portions, les ingrédients avec leurs quantités et unités, les étapes dans l'ordre et les valeurs nutritionnelles pour la recette entière et par portion.
- La description, le temps de cuisson et le pays d'origine s'affichent lorsqu'ils sont renseignés.
- Si aucune photo n'est fournie, une illustration par défaut s'affiche sur la fiche et sur les cartes de recettes.
- Le propriétaire voit la visibilité actuelle de sa recette et peut accéder aux actions de modification et de suppression depuis la fiche.
- Au lot 2, la fiche permet de choisir un nombre de portions et d'afficher les quantités correspondantes selon la section 4.5.6.

#### **4.5.5. Effets d'un changement de visibilité ou d'une suppression**

- **Passage en privé.** La recette est retirée du catalogue et de la recherche publics. Sa photo est conservée et soumise aux mêmes droits d'accès que la fiche, y compris par son URL directe. Les favoris existants sont conservés, mais la recette est masquée dans les listes des utilisateurs qui n'y ont plus accès.
- **Passage en public.** La recette et sa photo deviennent accessibles publiquement. La recette redevient visible dans les listes de favoris des utilisateurs qui l'avaient enregistrée.
- **Suppression confirmée.** La fiche devient inaccessible. Les associations de favoris et la photo téléversée sont supprimées. L'illustration par défaut reste disponible pour les autres recettes.

#### **4.5.6. Adaptation des portions au lot 2**

- À l'ouverture de la fiche, le sélecteur reprend le nombre de portions enregistré. L'utilisateur peut choisir un entier strictement positif et revenir au nombre de portions d'origine. Une valeur invalide déclenche un message et les résultats restent affichés pour le dernier nombre valide.
- Chaque quantité est recalculée à partir de la recette enregistrée selon la formule `quantité adaptée = quantité d'origine × portions choisies ÷ portions d'origine`. Par exemple, 300 g pour 4 portions donnent 450 g pour 6 portions ou 150 g pour 2 portions. L'affichage conserve l'unité d'origine et peut montrer des fractions, y compris pour un ingrédient à la pièce.
- Les quantités affichées peuvent être arrondies. Le recalcul et la comparaison avec le stock utilisent les quantités avant arrondi d'affichage. Chaque changement repart des quantités enregistrées.
- Le total nutritionnel affiché est ajusté dans la même proportion. Les valeurs par portion et les mentions « Non disponible » sont conservées. Les règles d'arrondi de la section 4.3.2 s'appliquent à l'affichage. Les étapes et les durées restent celles de la recette enregistrée.
- Le choix est temporaire et accessible aux visiteurs pour les recettes publiques. Il ne modifie ni la recette enregistrée ni le stock. Pour un utilisateur connecté, le bilan du garde-manger sur la fiche est recalculé avec les quantités adaptées.

### **4.6. Garde-manger personnel**

Le garde-manger et la fonction « Que puis-je cuisiner ? » sont disponibles au lot 2. Chaque utilisateur connecté accède uniquement à son propre stock.

#### **4.6.1. Gestion du stock**

- L'utilisateur ajoute un ingrédient en le sélectionnant dans le catalogue CIQUAL et en renseignant une quantité décimale strictement positive et une unité autorisée. Le garde-manger affiche le nom de chaque ingrédient, sa quantité et son unité.
- Le garde-manger contient une seule entrée par ingrédient CIQUAL. Si l'ingrédient est déjà présent, l'application ouvre la modification de son stock. La quantité enregistrée représente le stock total disponible.
- L'utilisateur peut modifier une quantité ou retirer l'ingrédient de son garde-manger lorsqu'il n'en a plus. Un ingrédient désactivé dans le catalogue peut rester dans le stock existant et être pris en compte dans les comparaisons.

#### **4.6.2. Comparaison avec les recettes**

- La fonction examine les recettes publiques et les recettes privées de l'utilisateur. Par défaut, elle utilise le nombre de portions enregistré pour chaque recette. Si l'utilisateur saisit un nombre de portions dans « Que puis-je cuisiner ? », ce nombre s'applique à toutes les recettes évaluées et est repris à l'ouverture d'une fiche depuis les résultats. Effacer ce choix rétablit le nombre de portions d'origine de chaque recette.
- Le garde-manger est comparé aux besoins ajustés selon la section 4.5.6. Les suggestions et les quantités manquantes sont recalculées à chaque changement valide du nombre de portions, à partir du stock disponible. La recherche laisse les quantités du garde-manger intactes.
- Les ingrédients sont rapprochés par leur identifiant CIQUAL. Si un ingrédient figure sur plusieurs lignes d'une recette, les quantités requises sont additionnées après conversion dans une unité commune.
- Les masses sont comparées en grammes. Une unité de volume ou une quantité à la pièce peut être convertie si une équivalence est définie pour l'ingrédient concerné. Si une quantité disponible ou requise ne peut pas être convertie dans une unité commune, l'ingrédient est signalé comme « Quantité à vérifier » et la recette ne peut pas être classée comme entièrement réalisable.
- Un ingrédient absent du garde-manger a un stock nul. Quand les quantités sont comparables, la quantité manquante est la différence entre le besoin et le stock disponible si le besoin est supérieur. Un stock égal à la quantité requise suffit.

#### **4.6.3. Affichage des suggestions et des manques**

- Les recettes entièrement réalisables sont affichées en premier. Elles disposent de quantités suffisantes pour chaque ingrédient. Les recettes nécessitant un complément ou une vérification apparaissent ensuite si elles partagent au moins un ingrédient avec le garde-manger.
- Pour chaque ingrédient manquant, le résultat affiche son nom, la quantité requise, la quantité disponible et le complément nécessaire dans une unité commune. Par exemple, si une recette demande 500 g de farine et que le stock en contient 200 g, le résultat indique « 300 g manquants ». Si la farine est absente, le résultat indique « 500 g manquants ».
- Pour un ingrédient dont les quantités ne sont pas comparables, le résultat affiche les quantités avec leurs unités d'origine et la mention « Quantité à vérifier ».
- Si le garde-manger est vide, l'application invite l'utilisateur à ajouter des ingrédients. Si aucune recette ne contient un ingrédient du stock, elle indique qu'aucune suggestion ne correspond.


### **4.7. Importation de recettes externes**

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

### **4.8. Administration**

L'administration doit permettre de gérer :

- Les utilisateurs et leurs droits.    
- Les ingrédients et leurs données nutritionnelles.    
- Les recettes et leur visibilité.    
- Les imports CIQUAL.    
- Les imports de recettes externes.    
- Les associations entre ingrédients externes et ingrédients CIQUAL.    
- Les erreurs et historiques des traitements automatisés.   

Un tableau de bord administratif personnalisé pourra présenter les indicateurs suivants : nombre de recettes, nombre d'utilisateurs, nombre d'ingrédients, imports récents et traitements en échec.

Le socle d'administration s'appuiera sur Django Admin.

### **4.9. Gestion des favoris**

Les favoris sont disponibles au lot 2. Ils sont associés au compte et accessibles après connexion. Chaque utilisateur peut consulter et gérer sa propre liste.

#### **4.9.1. Ajouter ou retirer un favori**

- Depuis la fiche d'une recette publique ou de l'une de ses propres recettes privées, l'utilisateur connecté peut choisir « Ajouter aux favoris ». Pour un visiteur, l'action ouvre la page de connexion.
- Une recette apparaît au plus une fois dans les favoris d'un même compte, même si l'ajout est demandé plusieurs fois. Après l'ajout, la fiche affiche l'action « Retirer des favoris ».
- L'utilisateur peut retirer la recette depuis sa fiche ou directement depuis « Mes favoris ». Après le retrait, la recette disparaît de la liste et l'action « Ajouter aux favoris » est à nouveau disponible sur la fiche.

#### **4.9.2. Affichage dans « Mes favoris »**

- L'espace « Mes favoris » affiche les recettes publiques enregistrées et les recettes privées enregistrées dont l'utilisateur est propriétaire. Chaque carte affiche le titre, la photo ou l'illustration par défaut et le temps de préparation. Elle donne accès à la fiche et à l'action de retrait.
- Les favoris sont affichés du plus récemment ajouté au plus ancien. Si aucune recette n'est visible, la page affiche « Aucun favori à afficher » et propose un lien vers le catalogue public.
- Quand la recette d'un autre utilisateur devient privée, elle est masquée dans « Mes favoris ». Elle réapparaît si elle redevient publique. Quand une recette est supprimée, les favoris associés sont supprimés conformément à la section 4.5.5.

## **5. Arborescence du site**

- **Accueil**
    
- **Recettes**
    
    Catalogue et recherche
    
    Détail d'une recette
    
    Créer / modifier une recette
    
    Mes recettes

    Mes favoris
    
- **Ingrédients**
    
    Catalogue CIQUAL
    
    Fiche nutritionnelle
    
- **Garde-manger**
    
    Mes ingrédients
    
    Que puis-je cuisiner ?
    
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

Sources de recettes externes

Les traitements volumineux (importation de milliers d'ingrédients, rapprochement des ingrédients) pourront être exécutés en arrière-plan à l'aide d'une file de tâches, par exemple Celery et Redis.

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
|IngredientAlias|Correspondances et synonymes d'ingrédients|

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

## **11. Tests et recette fonctionnelle**

Les tests devront couvrir les principales fonctionnalités ainsi que les cas d'erreur.

| Scénario                                                    | Résultat attendu                                            |
| ----------------------------------------------------------- | ----------------------------------------------------------- |
| Un utilisateur s'inscrit                                    | Le compte est créé                                          |
| Un utilisateur crée une recette                             | La recette et ses relations sont enregistrées               |
| Le propriétaire modifie les ingrédients ou leurs quantités | Les modifications sont enregistrées et les valeurs nutritionnelles sont recalculées |
| Un utilisateur enregistre une recette sans photo et garde la visibilité présélectionnée | La recette est privée et sa fiche affiche l'illustration par défaut |
| Le propriétaire passe une recette publique en privé         | La recette disparaît de la recherche publique et des favoris des autres utilisateurs. La fiche et la photo sont protégées par les mêmes droits d'accès |
| Le propriétaire rend à nouveau publique une recette privée  | La fiche et sa photo sont accessibles publiquement et la recette réapparaît dans les favoris existants |
| Le propriétaire confirme la suppression d'une recette      | La fiche devient inaccessible, les favoris associés et la photo téléversée sont supprimés |
| Un utilisateur connecté ajoute deux fois la même recette à ses favoris | Une seule carte apparaît dans « Mes favoris » et la fiche propose « Retirer des favoris » |
| Un utilisateur retire son dernier favori depuis « Mes favoris » | La carte disparaît, la page affiche « Aucun favori à afficher » et la fiche propose de nouveau l'ajout |
| Un utilisateur ajoute une de ses recettes privées à ses favoris | La recette apparaît dans son espace « Mes favoris » et reste absente du catalogue public, même avec le filtre « Mes favoris » |
| Un utilisateur sélectionne un ingrédient CIQUAL             | L'ingrédient existant est réutilisé                         |
| Un visiteur cherche « crepe », sélectionne la farine et les œufs, puis fixe un maximum de 20 minutes | Seules les recettes publiques dont le titre correspond, qui contiennent les deux ingrédients et qui demandent au plus 20 minutes de préparation sont affichées |
| Un visiteur filtre les recettes à 500 kcal maximum par portion | Une recette à exactement 500 kcal est incluse. Une recette à 500,4 kcal et une recette sans valeur énergétique sont exclues |
| Un visiteur trie les recettes par calories sans filtre nutritionnel | Les valeurs disponibles sont classées par ordre croissant, les égalités sont départagées par le titre puis l'identifiant, et les valeurs indisponibles figurent à la fin |
| Une recette contient 200 g d'un ingrédient à 100 kcal/100 g | La contribution calculée est de 200 kcal                    |
| Une recette contient 0,2 kg d'un ingrédient à 100 kcal/100 g | La contribution calculée est de 200 kcal |
| Un utilisateur saisit une quantité en millilitres au lot 1 | Le formulaire refuse cette unité |
| Deux ingrédients apportent chacun 0,25 kcal et la recette donne deux portions | L'affichage indique 1 kcal pour la recette et 0 kcal par portion, calculées à partir du total de 0,50 kcal |
| Une recette apporte 2,5 g de protéines pour deux portions | L'affichage indique 2,5 g pour la recette et 1,3 g par portion |
| La valeur CIQUAL des lipides manque pour un ingrédient et les autres valeurs sont complètes | La recette peut être enregistrée. Seuls les lipides sont affichés comme « Non disponible » et un filtre sur les lipides exclut la recette |
| Tous les ingrédients ont une valeur CIQUAL de 0 g de glucides pour 100 g | La recette et chaque portion affichent 0,0 g de glucides |
| Une mise à jour CIQUAL modifie une valeur utilisée dans une recette | Les valeurs nutritionnelles affichées reflètent la donnée mise à jour |
| Le propriétaire enregistre un nouveau nombre de portions sans modifier les quantités d'une recette | Le total nutritionnel reste identique et les valeurs par portion sont recalculées |
| Une fiche indique 300 g pour 4 portions et le visiteur choisit 6 portions puis 2 | La fiche affiche successivement 450 g et 150 g. Le total nutritionnel s'adapte et la recette enregistrée reste inchangée |
| L'utilisateur a 400 g de farine en stock et la recette en demande 300 g pour 4 portions | La farine est couverte pour 4 portions. À 6 portions, le bilan indique 50 g manquants et le stock reste à 400 g |
| L'utilisateur saisit zéro ou un nombre fractionnaire dans le sélecteur de portions | Un message indique que le nombre doit être un entier strictement positif et le dernier résultat valide reste affiché |
| Un autre utilisateur tente de modifier une recette privée   | L'accès est refusé                                          |
| Un fichier CIQUAL invalide est importé                      | L'import échoue sans perte de données                       |
| Une ligne contient une valeur nutritionnelle négative et les autres lignes sont valides | Le rapport localise l'erreur et le catalogue reste intact |
| Un code CIQUAL apparaît deux fois dans le même fichier | Un doublon identique est ignoré et signalé. Des lignes contradictoires bloquent l'import |
| Un fichier CIQUAL valide est importé deux fois dans le même mode | Le second import ne crée aucun doublon et ne modifie pas les données déjà à jour |
| Un ingrédient utilisé dans une recette est absent d'une mise à jour CIQUAL | En mode partiel, l'ingrédient reste inchangé. En mode complet, il est désactivé pour les nouvelles sélections et la recette conserve le lien et les dernières valeurs nutritionnelles |
| Un code CIQUAL désactivé réapparaît dans un fichier valide | L'ingrédient est réactivé avec le même identifiant et ses valeurs sont mises à jour |
| Une recette externe est importée deux fois                  | Aucun doublon n'est créé                                    |
| Le garde-manger contient 0,2 kg de farine et la recette en demande 500 g | La recette apparaît parmi celles nécessitant un complément et indique 300 g de farine manquants |
| Une recette contient deux lignes de 100 g et 150 g du même ingrédient et le garde-manger en contient 250 g | Les besoins sont additionnés et l'ingrédient est entièrement couvert |
| Une recette contient un ingrédient absent du garde-manger et un autre présent en quantité suffisante | Le résultat indique que la quantité totale du premier ingrédient manque |
| Une recette demande une quantité en grammes alors que le stock est indiqué en pièces sans équivalence | Le résultat affiche « Quantité à vérifier » et la recette ne figure pas parmi celles entièrement réalisables |
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
|Complexité du périmètre|Retard de livraison|Développement par lots et priorisation|
|Performances sur un grand catalogue|Recherche lente|Indexation et pagination|
|Dépendance aux services externes|Indisponibilité|Gestion des échecs et mécanismes de reprise|
## **14. Critères d'acceptation du projet**

Le **socle fonctionnel** pourra être considéré comme livré lorsque les conditions suivantes seront remplies :

- Un utilisateur peut s'inscrire et se connecter.    
- Le catalogue CIQUAL français est consultable et recherchable.    
- Un utilisateur peut créer une recette à partir d'ingrédients existants, puis la modifier, changer sa visibilité ou la supprimer.
- Les quantités et étapes sont correctement enregistrées.    
- Les calories et macronutriments sont calculés correctement.    
- Les recettes privées sont protégées.    
- Les recettes publiques sont consultables.    
- L'administration permet de gérer les données essentielles.    
- Les tests critiques sont exécutés avec succès.    
- L'application fonctionne dans un environnement Docker documenté.    
- L'interface est exploitable sur ordinateur et smartphone.
