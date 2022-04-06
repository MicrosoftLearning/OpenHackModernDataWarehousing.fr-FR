---
ms.openlocfilehash: a92b11c8be6b20d5344f1c001b4176aeba647a0c
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765848"
---
# <a name="22---loading-vanarsdel-customers"></a>2.2 - Chargement des clients VanArsdel

Le code suivant charge les clients VanArsdel :

```python
va_customers_filePath = mountPoint + "/raw/vanarsdel/Customers.json"

va_customers_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_customers_filePath)
va_customers_pd = va_customers_raw.toPandas()

va_customers = va_customers_pd[['CustomerID', 'LastName', 'FirstName', 'PhoneNumber', 'CreatedDate', 'UpdatedDate']]

va_customers['CreatedDate'] = pd.to_datetime(va_customers_pd['CreatedDate'], errors='coerce')
va_customers['UpdatedDate'] = pd.to_datetime(va_customers_pd['UpdatedDate'], errors='coerce')

va_customers.PhoneNumber = va_customers_pd.PhoneNumber.astype(str)

display(va_customers)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Voici des informations détaillées sur ce code :

### <a name="loading-the-data"></a>Chargement des données

```python
va_customers_filePath = mountPoint + "/raw/vanarsdel/Customers.json"

va_customers_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_customers_filePath)
```

> Comme les données VanArsdel sont enregistrées dans le lac de données sous forme de fichier JSON, vous les chargez en utilisant `spark.read.json` avec quelques options que vous pouvez voir ci-dessus. Les données chargées sont alors dans un dataframe Pyspark prêt à l’emploi.

```python
va_customers_pd = va_customers_raw.toPandas()
```

> Vous convertissez les dataframes Pyspark en dataframes Pandas pour faciliter la transformation.

### <a name="selecting-the-relevant-data"></a>Sélection des données appropriées

```python
va_customers = va_customers_pd[['CustomerID', 'LastName', 'FirstName', 'PhoneNumber', 'CreatedDate', 'UpdatedDate']]
```

> Le dataframe contient également les adresses, mais dans notre contexte vous avez seulement besoin des informations des clients. Pour cette raison, nous utilisons le code ci-dessus afin de sélectionner uniquement les colonnes relatives aux informations des clients, comme pour le dataframe Southridge.

### <a name="conforming-the-data-types"></a>Mise en conformité des types de données

```python
va_customers['CreatedDate'] = pd.to_datetime(va_customers['CreatedDate'], errors='coerce')
va_customers['UpdatedDate'] = pd.to_datetime(va_customers['UpdatedDate'], errors='coerce')

va_customers.PhoneNumber = va_customers.PhoneNumber.astype(str)
```

> Dans le morceau de code ci-dessus, nous donnons au jeu de données un standard pour les champs susceptibles d’avoir des problèmes d’incohérence :
>
> - `CreatedDate` est converti en colonne `datetime`, ainsi que `UpdatedDate`
> - `PhoneNumber` est converti en colonne `string`

```python
display(va_customers)
```

> La dernière étape de ce morceau de code est l’opération `display` qui affiche le résultat de la transformation de données à l’écran, sous forme de table.