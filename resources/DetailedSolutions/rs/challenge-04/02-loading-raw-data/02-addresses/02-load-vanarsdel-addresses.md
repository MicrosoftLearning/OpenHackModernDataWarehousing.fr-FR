---
ms.openlocfilehash: 2248b634086756c34289c2293c1273c48339a9b8
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765803"
---
# <a name="22---loading-vanarsdel-addresses"></a>2.2 - Chargement des adresses VanArsdel

Le code suivant charge les adresses VanArsdel :

```python
va_customers_filePath = mountPoint + "/raw/vanarsdel/Customers.json"

va_customers_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_customers_filePath)
va_customers_pd = va_customers_raw.toPandas()

va_addresses = va_customers_pd[['CustomerID', 'AddressLine1', 'AddressLine2', 'City', 'State', 'ZipCode', 'CreatedDate', 'UpdatedDate']]

va_addresses['CreatedDate'] = pd.to_datetime(va_addresses['CreatedDate'], errors='coerce')
va_addresses['UpdatedDate'] = pd.to_datetime(va_addresses['UpdatedDate'], errors='coerce')

va_addresses.AddressLine2 = va_addresses.AddressLine2.astype(str)
va_addresses.ZipCode = va_addresses.ZipCode.astype(str)

va_addresses.insert(0, 'AddressID', 'None')

display(va_addresses)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Voici des informations détaillées sur ce code :

```python
va_addresses_filePath = mountPoint + "/raw/vanarsdel/Addresses.json"

va_addresses_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_addresses_filePath)
```

> Comme les données VanArsdel sont enregistrées dans le lac de données sous forme de fichier JSON, vous les chargez en utilisant `spark.read.json` avec quelques options que vous pouvez voir ci-dessus. Les données chargées sont alors dans un dataframe Pyspark prêt à l’emploi.

```python
va_addresses_pd = va_addresses_raw.toPandas()
```

> Vous convertissez le dataframe Pyspark en dataframe Pandas pour faciliter la transformation.

```python
va_addresses = va_addresses_pd[['CustomerID', 'LastName', 'FirstName', 'PhoneNumber', 'CreatedDate', 'UpdatedDate']]
```

> Le dataframe contient également des informations des clients, mais dans notre contexte vous avez seulement besoin de leur adresse. Pour cette raison, nous utilisons le code ci-dessus afin de sélectionner uniquement les colonnes relatives aux informations des clients, comme pour le dataframe Southridge.

```python
va_addresses['CreatedDate'] = pd.to_datetime(va_addresses['CreatedDate'], errors='coerce')
va_addresses['UpdatedDate'] = pd.to_datetime(va_addresses['UpdatedDate'], errors='coerce')

va_addresses.AddressLine2 = va_addresses.AddressLine2.astype(str)
va_addresses.ZipCode = va_addresses.ZipCode.astype(str)
```

> Dans le morceau de code ci-dessus, nous donnons au jeu de données un standard pour les champs susceptibles d’avoir des problèmes d’incohérence :
>
> - `CreatedDate` est converti en colonne `datetime`, ainsi que `UpdatedDate`
> - `AddressLine2` contient probablement des valeurs `null`, ce qui peut entraîner des problèmes pendant la concaténation des données et l’inférence du type. Pour l’éviter, vous convertissez cette colonne en colonne `string`
> - `ZipCode` est converti en colonne `string`

```python
va_addresses.insert(0, 'AddressID', 'None')
```

> Pour que toutes les données soient mises en forme dans le même ensemble de colonnes, vous ajoutez `AddressID` à ce dataframe afin qu’il suive le schéma de Southridge. Dans les situations où vous devez choisir de *supprimer une colonne ou d’ajouter une colonne vide*, vous choisissez probablement d’ajouter une colonne vide pour suivre les jeux de données au lieu de supprimer des données. Pour plus d’informations, consultez [ce lien](../../../_TODOS/delete-or-not.md).

```python
display(va_addresses)
```

> La dernière étape de ce morceau de code est l’opération `display` qui affiche le résultat de la transformation de données à l’écran, sous forme de table.