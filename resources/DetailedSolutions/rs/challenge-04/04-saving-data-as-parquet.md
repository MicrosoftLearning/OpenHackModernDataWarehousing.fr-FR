---
ms.openlocfilehash: 6dfbf1f20e20d04bb9ebbe80b8a11543210bf1b1
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765846"
---
# <a name="4---saving-the-data-as-parquet-to-the-data-lake"></a>4 - Enregistrement des données au format Parquet dans le lac de données

Cette méthode d’enregistrement des dataframes Pandas sous forme de fichier Parquet dans le lac de données fonctionne de façon identique pour les trois objets métier (`Addresses`, `Customers` et `Movies and Actors`).

Par conséquent, le code suivant s’affiche avec des espaces réservés pour chacun de ces objets métier :

```python
parquet_path = mountPoint + '/parquet/<Business Object name>.parquet'

saveAsParquet(all_<business object name>, parquet_path)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

```python
parquet_path = mountPoint + '/parquet/<Business Object name>.parquet'
```

> Cette ligne de code combine les deux chaînes pour créer le chemin complet du fichier Parquet à enregistrer dans le lac de données.

```python
saveAsParquet(all_<business object name>, parquet_path)
```

> La ligne de code ci-dessus utilise la fonction `saveAsParquet` définie dans la première cellule pour enregistrer le résultat de la transformation sous forme de fichier Parquet dans le lac de données.