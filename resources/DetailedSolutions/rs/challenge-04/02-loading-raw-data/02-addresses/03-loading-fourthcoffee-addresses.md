---
ms.openlocfilehash: ce6a43729bf1e77733545bf55454117f05c3cf0f
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140766006"
---
# <a name="23---loading-fourthcoffee-addresses"></a>2.3 - Chargement des adresses FourthCoffee

Le code suivant charge les clients FourthCoffee :

```python
fc_customers_filePath = "/dbfs" + mountPoint + "/raw/fourthcoffee/Customers.csv"

fc_customers_pd = pd.read_csv(fc_customers_filePath)

fc_addresses = fc_customers_pd[['CustomerID', 'AddressLine1', 'AddressLine2', 'City', 'State', 'ZipCode', 'CreatedDate', 'UpdatedDate']]

fc_addresses['CreatedDate'] = pd.to_datetime(fc_addresses['CreatedDate'], errors='coerce')
fc_addresses['UpdatedDate'] = pd.to_datetime(fc_addresses['UpdatedDate'], errors='coerce')
fc_addresses.AddressLine2 = fc_addresses.AddressLine2.astype(str)
fc_addresses.ZipCode = fc_addresses.ZipCode.astype(str)

fc_addresses.loc[fc_addresses.AddressLine2 == 'nan', 'AddressLine2'] = 'None'

fc_addresses.insert(0, 'AddressID', 'None')

display(fc_addresses)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Voici des informations détaillées sur ce code :

```python
fc_customers_filePath = "/dbfs" + mountPoint + "/raw/fourthcoffee/Customers.csv"

fc_customers_pd = pd.read_csv(fc_customers_filePath)
```

> Comme les données FourthCoffee sont enregistrées dans le lac de données sous forme de fichier CSV, vous le chargez en utilisant `pandas.read_csv`. Les données chargées sont déjà dans un dataframe Pandas, ce qui vous évite de les convertir à partir d’autres formats.

```python
fc_addresses = fc_customers_pd[['CustomerID', 'AddressLine1', 'AddressLine2', 'City', 'State', 'ZipCode', 'CreatedDate', 'UpdatedDate']]
```

> Le dataframe contient également les données des clients, mais dans ce contexte vous avez seulement besoin de leur adresse. Pour cette raison, nous utilisons le code ci-dessus afin de sélectionner uniquement les colonnes relatives aux informations des clients, comme pour le dataframe Southridge.

```python
fc_addresses['CreatedDate'] = pd.to_datetime(fc_addresses['CreatedDate'], errors='coerce')
fc_addresses['UpdatedDate'] = pd.to_datetime(fc_addresses['UpdatedDate'], errors='coerce')
fc_addresses.AddressLine2 = fc_addresses.AddressLine2.astype(str)
fc_addresses.ZipCode = fc_addresses.ZipCode.astype(str)
```

> Dans le morceau de code ci-dessus, nous donnons au jeu de données un standard pour les champs susceptibles d’avoir des problèmes d’incohérence :
>
> - `CreatedDate` est converti en colonne `datetime`, ainsi que `UpdatedDate`
> - `AddressLine2` contient probablement des valeurs `null`, ce qui peut entraîner des problèmes pendant la concaténation des données et l’inférence du type. Pour l’éviter, vous convertissez cette colonne en colonne `string`
> - `ZipCode` est converti en colonne `string`

```python
fc_addresses.insert(0, 'AddressID', 'None')
```

> Pour que toutes les données soient mises en forme dans le même ensemble de colonnes, vous ajoutez `AddressID` à ce dataframe afin qu’il suive le schéma de Southridge. Dans les situations où vous devez choisir de *supprimer une colonne ou d’ajouter une colonne vide*, vous choisissez probablement d’ajouter une colonne vide pour suivre les jeux de données au lieu de supprimer des données. Pour plus d’informations, consultez [ce lien](../../../_TODOS/delete-or-not.md).

```python
display(fc_addresses)
```

> La dernière étape de ce morceau de code est l’opération `display` qui affiche le résultat de la transformation de données à l’écran, sous forme de table.