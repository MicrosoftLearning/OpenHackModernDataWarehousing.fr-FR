---
ms.openlocfilehash: d737846d49262559d7a71c6a931cb0dd29556b3f
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765771"
---
# <a name="modern-data-warehousing-openhack-solutions"></a>Solutions modernes d’entreposage de données OpenHack

## <a name="introduction"></a>Introduction

L’entreposage de données moderne OpenHack se compose d’une série de défis qui reflètent une implémentation progressive d’un entrepôt de données moderne, tout en adoptant et en appliquant certaines pratiques DevOps.
En général, les derniers défis sont plus difficiles que les premiers, mais ce n’est pas toujours le cas.
Par exemple, le défi 4 implique du code pour transformer et conformer des données extraites.
Certains peuvent le trouver plus complexe que le défi 5, où un schéma en étoile est rempli.
Toutefois, étant donné le paradigme ascendant de l’entreposage de données moderne, il est avantageux de traiter les anomalies de données « irrécupérables » et de créer un jeu de données intermédiaire.
Le cas d’usage spécifique de servir des rapports à partir d’un schéma en étoile peut ensuite tirer parti du jeu de données intermédiaire, tandis que les équipes de scientifiques des données l’explorent aussi pour découvrir les inconnues inconnues.

Les défis sont les suivants :

1. Sélectionner et provisionner des ressources de stockage dans le but de créer un lac de données d’entreprise
1. Extraire des données de bases de données Azure SQL dans le lac de données
1. Extraire des données de bases de données locales dans le lac de données ; s’assurer que les ressources de la solution sont conservées dans le contrôle de code source
1. Conformer les données extraites dans un jeu de données intermédiaire qui peut répondre à divers besoins métier et de personnages utilisateur ; s’assurer que la solution de contrôle de code source nécessite une révision et une approbation avant la fusion
1. Tirer parti du jeu de données intermédiaire pour remplir un schéma en étoile permettant de répondre aux besoins de reporting ; s’assurer que la solution prend en charge les tests unitaires automatisés
1. Implémenter un traitement incrémentiel quotidien ; s’assurer que la télémétrie et les métriques font l’objet de collectes et de rapports
1. Implémenter des déploiements automatisés dans un nouvel environnement de test et exiger des approbations explicites avant de promouvoir la solution en production

Les deux derniers défis sont considérés comme des défis bonus.
Ils apparaissent simultanément après la fin des cinq principaux défis et peuvent alors être exécutés dans n’importe quel ordre.
