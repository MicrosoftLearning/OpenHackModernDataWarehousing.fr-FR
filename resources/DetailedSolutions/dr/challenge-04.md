---
ms.openlocfilehash: 07c90bf4f4ddc091584c13e865ba7d9b619bd69d
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765818"
---
## <a name="challenge-4-more-than-meets-the-eye"></a>Défi 4 : Plus qu’il n’y paraît

Ce défi porte sur la création d’un « tout-en-un » pour les données d’un lac de données. Différents personnages tentant de répondre aux différents besoins métier peuvent avoir différents critères pour leur traitement en aval, mais une chose dont ils peuvent **tous** bénéficier est un jeu de données intermédiaire qui arrondit les angles des systèmes de données disparates.

En particulier, si l’objectif en aval est BI ou ML, toutes les équipes aimeraient

- utiliser un jeu de données unifié qui représente l’intégralité des données de l’entreprise
- continuer à utiliser le même jeu de données quel que soit le nombre de magasins futurs nécessaires ; autrement dit, éviter la duplication d’efforts pour réunir les nouvelles données dans le jeu de données complet
- avoir un schéma commun et conforme ; par exemple, si différents systèmes ont des noms différents ou utilisent des types de données différents pour le même concept, l’équipe en aval ne devrait pas avoir à porter ce poids mental

### <a name="target-datasets"></a>Jeux de données cibles

Finalement, nous pouvons voir l’opportunité de cinq jeux de données :

1. [Catalogue](./notebooks/Conformed%20Catalog.ipynb) : Tous les films et acteurs des trois systèmes sources
1. Clients : Toutes les personnes et adresses des trois systèmes sources
1. Ventes : Toutes les commandes et tous les détails de commande de Southridge CloudSales
1. Streaming : Toutes les informations de transaction de Southridge CloudStreaming
1. Locations : Toutes les informations de transaction de Fourth Coffee et Van Arsdel, Ltd.

### <a name="to-join-cleanse-drop-duplicates-etc--or-not"></a>Joindre, nettoyer, supprimer les doublons, etc. ... ou pas

À ce stade, nous voulons nous concentrer sur les anomalies irrécupérables qui pourraient entraîner des exceptions dans le traitement en aval, par exemple des types ou des formats de données incohérents.
Si nous chargions ces données directement dans un schéma de rapport final, nous aurions certainement besoin d’un nettoyage supplémentaire, comme par exemple :

- Rechercher et éliminer les fautes de frappe, par ex. PGg au lieu de PG
- Normaliser la mise en majuscules des titres, noms, classements, etc.
- Rechercher et résoudre les conflits dans les films mis en correspondance, par exemple Southridge classe Cube comme un film familial tous publics, tandis que VanArsdel, Ltd. le classe comme une comédie Interdit au moins de 13 ans
- Rechercher les variations dans les noms d’acteur et en choisir un à utiliser de manière cohérente dans le schéma de reporting, par exemple Vivica A. Fox ou Vivica Fox
- Supprimer les doublons

Si nous effectuons ces opérations maintenant, nous passerons peut-être à côté de l’opportunité de découvrir une valeur non reconnue dans les données.
Par exemple, pensez à la possibilité que certains acteurs et certaines actrices utilisent parfois l’initiale de leur second prénom, mais pas toujours.
Imaginez à présent que les scientifiques des données dévoilent une tendance dans laquelle les films sont plus commercialisables lorsque le casting utilise justement cette initiale.
Imaginez également que cela est seulement vrai pour le genre Drame, pas pour les films familiaux.
Si nous avons déjà appliqué une règle métier pour normaliser le nom de scène « habituel » des personnes, les scientifiques des données n’auraient jamais été en mesure de le voir.

### <a name="establishing-a-branch-policy"></a>Établissement d’une stratégie de branche

> TODO : Les références sont assez claires sur ce point, mais ajoutez directement des conseils ici.