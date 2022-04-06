---
ms.openlocfilehash: 7de1cb3c951ee7e2cbe1245aaccb6aa9af168e17
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765761"
---
# <a name="21---loading-southridge-customers"></a>2.1 - Chargement des clients Southridge

Le code suivant charge les clients Southridge :

```python
sr_sales_customers_parquet = mountPoint + "/raw/cloudsales/Customers.parquet"
sr_streaming_customers_parquet = mountPoint + "/raw/cloudstreaming/Customers.parquet"

sr_sales_customers = sqlContext.read.parquet(sr_sales_customers_parquet)
sr_streaming_customers = sqlContext.read.parquet(sr_streaming_customers_parquet)

sr_sales_customers = sr_sales_customers.toPandas()
sr_streaming_customers = sr_streaming_customers.toPandas()

sr_customers_frame = [sr_sales_customers, sr_streaming_customers]
sr_customers = pd.concat(sr_customers_frame)

sr_customers['CreatedDate'] = pd.to_datetime(sr_customers['CreatedDate'], errors='coerce')
sr_customers['UpdatedDate'] = pd.to_datetime(sr_customers['UpdatedDate'], errors='coerce')
sr_customers.PhoneNumber = sr_customers.PhoneNumber.astype(str)

display(sr_customers)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Voici des informations détaillées sur ce code :

### <a name="loading-data"></a>Chargement des données

```python
sr_sales_customers_parquet = mountPoint + "/raw/cloudsales/Customers.parquet"
sr_streaming_customers_parquet = mountPoint + "/raw/cloudstreaming/Customers.parquet"

sr_sales_customers = sqlContext.read.parquet(sr_sales_customers_parquet)
sr_streaming_customers = sqlContext.read.parquet(sr_streaming_customers_parquet)
```

> Comme Southridge a deux sources pour les clients (Cloud Sales et Cloud Streaming), vous devez charger les deux pour les fusionner ensuite. Le code ci-dessus déclare le chemin des deux fichiers et les charge également sous forme de dataframes à partir du format **Parquet**.

```python
sr_sales_customers = sr_sales_customers.toPandas()
sr_streaming_customers = sr_streaming_customers.toPandas()
```

> Vous convertissez les deux dataframes en dataframes Pandas pour faciliter la transformation.

### <a name="concatenating-the-data-frames"></a>Concaténation des dataframes

```python
sr_customers_frame = [sr_sales_customers, sr_streaming_customers]
sr_customers = pd.concat(sr_customers_frame)
```

> Le code ci-dessus crée une collection de dataframes (`sr_customers_frame`) afin de pouvoir ensuite les concaténer et les attribuer à `sr_customers`.

### <a name="conforming-the-data-types"></a>Mise en conformité des types de données

```python
sr_customers['CreatedDate'] = pd.to_datetime(sr_customers['CreatedDate'], errors='coerce')
sr_customers['UpdatedDate'] = pd.to_datetime(sr_customers['UpdatedDate'], errors='coerce')
sr_customers.PhoneNumber = sr_customers.PhoneNumber.astype(str)
```

> Dans le morceau de code ci-dessus, nous donnons au jeu de données un standard pour les champs susceptibles d’avoir des problèmes d’incohérence :
>
> - `CreatedDate` est converti en colonne `datetime`, ainsi que `UpdatedDate`
> - `PhoneNumber` est converti en colonne `string`

```python
display(sr_customers)
```

> La dernière étape de ce morceau de code est l’opération `display` qui affiche le résultat de la transformation de données à l’écran, sous forme de table.