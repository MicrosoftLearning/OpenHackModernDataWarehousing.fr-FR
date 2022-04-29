---
ms.openlocfilehash: 90de39d497d783a37234b5696660513c8be86a0a
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765832"
---
## <a name="challenge-1-based-on-a-true-story"></a>Défi 1 : Basé sur une histoire vraie

Le premier défi est conçu pour établir un lac de données d’entreprise.
L’objectif de ce défi est d’aider l’équipe à choisir et à provisionner son stockage.
La solution recommandée consiste à provisionner un compte Stockage Azure avec l’espace de noms hiérarchique activé, par exemple [ADLS Gen 2](https://docs.microsoft.com/fr-fr/azure/storage/blobs/data-lake-storage-introduction).

> Azure Data Lake Storage Gen2 est un ensemble de fonctionnalités dédiées à l’analytique Big Data, qui s’appuie sur Stockage Blob Azure.
La préversion de Data Lake Storage Gen2 rassemble les fonctionnalités de nos deux services de stockage existants : Stockage Blob Azure et Azure Data Lake Storage Gen1.
Les fonctionnalités d’Azure Data Lake Storage Gen1, comme la sémantique des systèmes de fichiers, le répertoire, la sécurité au niveau du fichier et la mise à l'échelle, sont combinées à celles du stockage Blob Azure, comme le stockage hiérarchisé économique et la haute disponibilité/reprise après sinistre.

### <a name="creating-an-adls-gen-2-storage-account"></a>Création d’un compte de stockage ADLS Gen 2

Le guide de démarrage rapide dans la documentation du produit pour [Créer un compte de stockage Azure Data Lake Storage Gen2](https://docs.microsoft.com/fr-fr/azure/storage/blobs/data-lake-storage-quickstart-create-account) contient tous les détails permettant de créer le compte de stockage via le portail Azure, PowerShell ou Azure CLI.

Assurez-vous que le type de compte de stockage est StorageV2 et que l’espace de noms hiérarchique est activé.

### <a name="storing-any-file"></a>Stockage d’un fichier

Une façon d’y parvenir consiste à utiliser l’[Explorateur Stockage Azure](https://azure.microsoft.com/fr-fr/features/storage-explorer/).
Les fichiers peuvent être chargés et téléchargés, et la fonctionnalité « Gérer l’accès » prend en charge la spécification des ACL dans le fichier.

![Explorateur Stockage Azure - Movies](./images/storage-explorer-southridge-raw-movies.png)
