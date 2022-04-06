---
ms.openlocfilehash: b3880118cb83c85b0f5b91ce7e196296c81fbb81
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765816"
---
# <a name="21---loading-southridge-addresses"></a>2.1 - Chargement des adresses Southridge

Le code suivant charge les adresses Southridge :

```python
sr_sales_addresses_parquet = mountPoint + "/raw/cloudsales/Addresses.parquet"
sr_streaming_addresses_parquet = mountPoint + "/raw/cloudstreaming/Addresses.parquet"

sr_sales_addresses = sqlContext.read.parquet(sr_sales_addresses_parquet)
sr_streaming_addresses = sqlContext.read.parquet(sr_streaming_addresses_parquet)

sr_sales_addresses = sr_sales_addresses.toPandas()
sr_streaming_addresses = sr_streaming_addresses.toPandas()

sr_addresses_frame = [sr_sales_addresses, sr_streaming_addresses]
sr_addresses = pd.concat(sr_addresses_frame)

sr_addresses['CreatedDate'] = pd.to_datetime(sr_addresses['CreatedDate'], errors='coerce')
sr_addresses['UpdatedDate'] = pd.to_datetime(sr_addresses['UpdatedDate'], errors='coerce')

sr_addresses.AddressLine2 = sr_addresses.AddressLine2.astype(str)
sr_addresses.ZipCode = sr_addresses.ZipCode.astype(str)

display(sr_addresses)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Voici des informations détaillées sur ce code :

```python
sr_sales_addresses_parquet = mountPoint + "/raw/cloudsales/Addresses.parquet"
sr_streaming_addresses_parquet = mountPoint + "/raw/cloudstreaming/Addresses.parquet"

sr_sales_addresses = sqlContext.read.parquet(sr_sales_addresses_parquet)
sr_streaming_addresses = sqlContext.read.parquet(sr_streaming_addresses_parquet)
```

> Comme Southridge a deux sources pour les adresses (Cloud Sales et Cloud Streaming), vous devez charger les deux pour les fusionner ensuite. Le code ci-dessus déclare le chemin des deux fichiers et les charge également sous forme de dataframes à partir du format **Parquet**.

```python
sr_sales_addresses = sr_sales_addresses.toPandas()
sr_streaming_addresses = sr_streaming_addresses.toPandas()
```

> Vous convertissez les deux dataframes en dataframes Pandas pour faciliter la transformation.

```python
sr_addresses_frame = [sr_sales_addresses, sr_streaming_addresses]
sr_addresses = pd.concat(sr_addresses_frame)
```

> Le code ci-dessus crée une collection de dataframes (`sr_addresses_frame`) afin de pouvoir ensuite les concaténer et les attribuer à `sr_addresses`.

```python
sr_addresses['CreatedDate'] = pd.to_datetime(sr_addresses['CreatedDate'], errors='coerce')
sr_addresses['UpdatedDate'] = pd.to_datetime(sr_addresses['UpdatedDate'], errors='coerce')

sr_addresses.AddressLine2 = sr_addresses.AddressLine2.astype(str)
sr_addresses.ZipCode = sr_addresses.ZipCode.astype(str)
```

> Dans le morceau de code ci-dessus, nous donnons au jeu de données un standard pour les champs susceptibles d’avoir des problèmes d’incohérence :
>
> - `CreatedDate` est converti en colonne `datetime`, ainsi que `UpdatedDate`
> - `AddressLine2` contient probablement des valeurs `null`, ce qui peut entraîner des problèmes pendant la concaténation des données et l’inférence du type. Pour l’éviter, vous convertissez cette colonne en colonne `string`
> - `ZipCode` est converti en colonne `string`

```python
display(sr_addresses)
```

> La dernière étape de ce morceau de code est l’opération `display` qui affiche le résultat de la transformation de données à l’écran, sous forme de table.