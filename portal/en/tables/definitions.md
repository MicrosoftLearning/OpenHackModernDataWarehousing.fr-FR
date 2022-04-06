---
ms.openlocfilehash: d1cccfaf74aaed2bf6ade4aa4a8a8475b8210568
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765834"
---
# <a name="table-definitions"></a>Définitions des tables

- [DimActors](#DimActors)
- [DimCategories](#DimCategories)
- [DimCustomers](#DimCustomers)
- [DimDate](#DimDate)
- [DimLocations](#DimLocations)
- [DimMovieActors](#DimMovieActors)
- [DimMovies](#DimMovies)
- [DimRatings](#DimRatings)
- [DimTime](#DimTime)
- [FactRentals](#FactRentals)
- [FactSales](#FactSales)
- [FactStreaming](#FactStreaming)

## <a name="dimactors"></a>DimActors

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
ActorSK | Entier 32 bits | Non | Clé de type entier séquentielle
ActorID | GUID | Non | Identificateur unique du système source
ActorName | String | Non | Nom de l’acteur (81 caractères max.)
ActorGender | String | Non | Représentation à un seul caractère du sexe de l’acteur

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Les données doivent être la somme des listes d’acteurs de tous les systèmes sources
- Les doublons doivent être supprimés et comptabilisés avant leur chargement dans l’entrepôt
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `ActorSK`
    - Index unique - `ActorID`

[Retour au début](#Table-Definitions)

## <a name="dimcategories"></a>DimCategories

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
MovieCategorySK | Entier 8 bits | Non | Clé de type entier séquentielle
MovieCategoryDescription | String | Non | Description de la catégorie de films

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Les données doivent être la somme des catégories de films de tous les systèmes sources
- Les doublons doivent être supprimés et comptabilisés avant leur chargement dans l’entrepôt
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `MovieCategorySK`
    - Index unique - `MovieCategoryDescription`

[Retour au début](#Table-Definitions)

## <a name="dimcustomers"></a>DimCustomers

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
CustomerSK | Entier 32 bits | Non | Clé de type entier séquentielle
CustomerID | GUID | Non | Identificateur unique du système source
LastName | String | Non | Nom du client (50 caractères max.)
FirstName | String | Non | Prénom du client (50 caractères max.)
AddressLine1 | String | Non | Adresse du client - Ligne 1 (50 caractères max.)
AddressLine2 | String | Oui | Adresse du client - Ligne 2 (50 caractères max.)
City | String | Non | Ville du client (30 caractères max.)
State | String | Non | Code du département du client (exactement 2 caractères)
Code postal | String | Non | Code postal du client (exactement 5 chiffres)
PhoneNumber | String | Non | Numéro de téléphone du client (exactement 10 chiffres)
RecordStartDate | Date | Non | Date à laquelle cette version de l’enregistrement du client est devenue active
RecordEndDate | Date | Oui | Date à laquelle cette version de l’enregistrement du client est devenue inactive
ActiveFlag | Boolean | Non | Indicateur défini sur true pour l’enregistrement actif d’un client donné

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Il s’agit d’une dimension de type 2 - les modifications apportées aux enregistrements doivent entraîner l’inactivité de l’enregistrement précédent
- Les données doivent être la somme des enregistrements client de tous les systèmes sources
- Les doublons doivent être supprimés et comptabilisés avant leur chargement dans l’entrepôt
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `CustomerSK`
    - Index unique - `CustomerID`

[Retour au début](#Table-Definitions)

## <a name="dimdate"></a>DimDate

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
DateSK | Entier 32 bits | Non | Clé de type entier séquentielle
ValDate | Date | Non | Valeur de date (aucune heure)
DateYear | Entier 16 bits | Non | Partie Année de DateValue
DateMonth | Entier de 8 bits | Non | Partie Mois de DateValue
DateDay | Entier de 8 bits | Non | Partie Jour de DateValue
DateDayOfWeek | Entier de 8 bits | Non | Jour de la semaine de DateValue (dimanche = 1)
DateDayOfYear | Entier de 16 bits | Non | Jour de l’année de DateValue
DateWeekOfYear | Entier de 8 bits | Non | Semaine de l’année de DateValue

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Le premier enregistrement de date doit être `2017-01-01`
- Pour le chargement initial des données (en bloc), inclure un enregistrement `DimDate` pour chaque date en cours de chargement
- Pour le chargement quotidien (incrémentiel) des données, ajouter un enregistrement `DimDate` pour le jour en cours de chargement
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `DateSK`
    - Index unique - `DateValue`

[Retour au début](#Table-Definitions)

## <a name="dimlocations"></a>DimLocations

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
LocationSK | Entier 16 bits | Non | Clé de type entier séquentielle
LocationName | String | Non | Nom de la localisation (50 caractères max.)
Diffusion en continu | Boolean | Non | True si la localisation prend en charge le streaming
Locations | Boolean | Non | True si la localisation prend en charge les locations
Sales | Boolean | Non | True si la localisation prend en charge les ventes

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Créer un enregistrement par localisation qui fournit les données sources
- Cette table doit être initialement chargée avec un enregistrement pour chaque localisation de source de données
    - À mesure que des localisations sont ajoutées, ajouter un enregistrement à cette table pour chacune d’entre elles
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `LocationSK`
    - Index unique - `LocationName`

[Retour au début](#Table-Definitions)

## <a name="dimmovieactors"></a>DimMovieActors

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
MovieID | GUID | Non | Réf. du film
ActorID | GUID | Non | Réf. de l’acteur

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Il s’agit d’une table d’associations plusieurs-à-plusieurs entre films et acteurs
- Les données doivent être la somme des classements de films de tous les systèmes sources
- Les doublons doivent être supprimés et comptabilisés avant leur chargement dans l’entrepôt
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `MovieID`, `ActorID`

[Retour au début](#Table-Definitions)

## <a name="dimmovies"></a>DimMovies

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
MovieSK | Entier 32 bits | Non | Clé de type entier séquentielle
MovieID | GUID | Non | Identificateur unique du système source
MovieTitle | String | Non | Titre du film (255 caractères max.)
MovieCategorySK | Entier 8 bits | Non | Clé pointant sur la catégorie du film (à partir de la table DimCategories)
MovieRatingSK | Entier 8 bits | Non | Clé pointant sur le classement du film (à partir de la table DimRatings)
MovieRunTimeMin | Entier 16 bits | Non | Durée du film en minutes

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Les données doivent être la somme des listes de films de tous les systèmes sources
- Les doublons doivent être supprimés et comptabilisés avant leur chargement dans l’entrepôt
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `MovieSK`
    - Index unique - `MovieID`
    - Clé étrangère - `MovieCategorySK` (clé étrangère pointant sur la table `DimCategories`)
    - Clé étrangère - `MovieRatingSK` (clé étrangère pointant sur la table `DimRatings`)

[Retour au début](#Table-Definitions)

## <a name="dimratings"></a>DimRatings

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
MovieRatingSK | Entier 8 bits | Non | Clé de type entier séquentielle
MovieRatingDescription | String | Non | Description du classement du film (5 caractères max.)

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Les données doivent être la somme des classements de films de tous les systèmes sources
- Les doublons doivent être supprimés et comptabilisés avant leur chargement dans l’entrepôt
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `MovieRatingSK`
    - Index unique - `MovieRatingDescription`

[Retour au début](#Table-Definitions)

## <a name="dimtime"></a>DimTime

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
TimeSK | Entier 32 bits | Non | Clé de type entier séquentielle
TimeValue | Heure | Non | Valeur de temps (aucune date ; précision de 1 seconde)
TimeHour | Entier 8 bits | Non | Partie heure de TimeValue
TimeMinute | Entier 8 bits | Non | Partie minute de TimeValue
TimeSecond | Entier 8 bits | Non | Partie seconde de TimeValue
TimeMinuteOfDay | Entier 16 bits | Non | Minute du jour (minuit = 0)
TimeSecondOfDay | Entier 32 bits | Non | Seconde du jour (minuit = 0)

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Créer un enregistrement par seconde du jour (86 400 enregistrements au total)
- Il s’agit d’une table chargée une seule fois. Les données ne doivent pas être ajoutées ou modifiées
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `TimeSK`
    - Index unique - `TimeValue`

[Retour au début](#Table-Definitions)

## <a name="factrentals"></a>FactRentals

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
RentalSK | Entier 32 bits | Non | Clé de type entier séquentielle
TransactionID | GUID | Non | ID de transaction du système source
CustomerSK | Entier 32 bits | Non | Clé de substitution de la dimension Customers pour le client
LocationSK | Entier 16 bits | Non | Clé de substitution de la dimension Locations pour la localisation des données sources
MovieSK | Entier 32 bits | Non | Clé de substitution de la dimension Movies pour le film
RentalDateSK | Entier 32 bits | Non | Clé de substitution de la dimension Date pour la date à laquelle le film a été loué
ReturnDateSK | Entier 32 bits | Oui | Clé de substitution de la dimension Date pour la date à laquelle le film a été rendu
RentalDuration | Entier 8 bits | Oui | Nombre de jours entre le moment où un film a été loué et le moment où il a été rendu
RentalCost | Devise | Non | Coût de la location de base
LateFee | Devise | Oui | Frais de retard pour la location
TotalCost | Devise | Oui | RentalCost + LateFee
RewindFlag | Boolean | Oui | Le film a-t-il été rembobiné ?

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Il s’agit d’une table de faits représentant les locations physiques de films
- Les données doivent être la somme des enregistrements client de tous les systèmes sources qui concernent les locations de films.
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `RentalSK`
    - Index unique - `TransactionID`
    - Clé étrangère - `RentalDateSK` ; référence `DimDate` (`DateSK`)
    - Clé étrangère - `ReturnDateSK` ; référence `DimDate` (`DateSK`)
    - Clé étrangère - `CustomerSK` ; référence `DimCustomers` (`CustomerSK`)
    - Clé étrangère - `LocationSK` ; référence `DimLocations` (`LocationSK`) - Clé étrangère - `MovieSK` ; référence `DimMovies` (`MovieSK`)

[Retour au début](#Table-Definitions)

## <a name="factsales"></a>FactSales

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
SalesSK | Entier 32 bits | Non | Clé de type entier séquentielle
OrderID | GUID | Non | ID de commande du système source
LineNumber | Entier 8 bits | Non | Nombre de lignes de commande du système source
OrderDateSK | Entier 32 bits | Non | Clé de substitution de la dimension Date pour la date à laquelle la commande a été passée
ShipDateSK | Entier 32 bits | Oui | Clé de substitution de la dimension Date pour la date à laquelle la commande a été livrée
CustomerSK | Entier 32 bits | Non | Clé de substitution de la dimension Customers pour le client
LocationSK | Entier 16 bits | Non | Clé de substitution de la dimension Locations pour la localisation des données sources
MovieSK | Entier 32 bits | Non | Clé de substitution de la dimension Movies pour le film
DaysToShip | Entier 8 bits | Oui | Nombre de jours entre le moment où une commande a été passée et le moment où elle a été livrée
quantité ; | Entier 8 bits | Non | Quantité achetée pour cet article
UnitCost | Devise | Non | Prix d’une seule quantité achetée pour cet article
ExtendedCost | Devise | Non | Quantity * UnitCost

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Il s’agit d’une table de faits représentant les ventes physiques de films
- Les données doivent être la somme des enregistrements client de tous les systèmes sources qui concernent les ventes de films.
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `SalesSK`
    - Index unique - `OrderID` & `LineNumber`
    - Clé étrangère - `OrderDateSK` ; référence `DimDate` (`DateSK`)
    - Clé étrangère - `ShipDateSK` ; référence `DimDate` (`DateSK`)
    - Clé étrangère - `CustomerSK` ; référence `DimCustomers` (`CustomerSK`)
    - Clé étrangère - `LocationSK` ; référence `DimLocations` (`LocationSK`) - Clé étrangère - `MovieSK` ; référence `DimMovies` (`MovieSK`)

[Retour au début](#Table-Definitions)

## <a name="factstreaming"></a>FactStreaming

Nom de la colonne | Type de données | Valeurs Null | Règles
---|---|---|---
StreamingSK | Entier 32 bits | Non | Clé de type entier séquentielle
TransactionID | GUID | Non | ID de transaction du système source
CustomerSK | Entier 32 bits | Non | Clé de substitution de la dimension Customers pour le client
MovieSK | Entier 32 bits | Non | Clé de substitution de la dimension Movies pour le film
StreamStartDateSK | Entier 32 bits | Non | Clé de substitution de la dimension Date pour la date à laquelle le streaming a commencé
StreamStartDateSK | Entier 32 bits | Non | Clé de substitution de la dimension Time pour l’heure à laquelle le streaming a commencé
StreamEndDateSK | Entier 32 bits | Oui | Clé de substitution de la dimension Date pour la date à laquelle le streaming s’est terminé
StreamEndDateSK | Entier 32 bits | Oui | Clé de substitution de la dimension Time pour l’heure à laquelle le streaming s’est terminé
StreamDurationSec | Entier 32 bits | Oui | Durée (en secondes) de la session de streaming
StreamDurationMin | Decimal | Oui | Durée (en minutes + fraction de minutes) de la session de streaming (précision à 4 décimales)

### <a name="data-generationpopulation-rules"></a>Règles de génération/remplissage des données

- Il s’agit d’une table de faits représentant les sessions de streaming de films
- Les données doivent être la somme des enregistrements client de tous les systèmes sources qui concernent le streaming de films.
- Si votre plateforme de base de données prend en charge l’intégrité référentielle et les clés, vous pouvez utiliser les règles suivantes :
    - Clé primaire - `StreamingSK`
    - Index unique - `TransactionID`
    - Clé étrangère - `StreamStartDateSK` ; référence `DimDate` (`DateSK`)
    - Clé étrangère - `StreamStartTimeSK` ; référence `DimTime` (`TimeSK`)
    - Clé étrangère - `StreamEndDateSK` ; référence `DimDate` (`DateSK`)
    - Clé étrangère - `StreamEndTimeSK` ; référence `DimTime` (`TimeSK`)
    - Clé étrangère - `CustomerSK` ; référence `DimCustomers` (`CustomerSK`) - Clé étrangère - `MovieSK` ; référence `DimMovies` (`MovieSK`)

[Retour au début](#Table-Definitions)
