---
ms.openlocfilehash: 7246f53f9c1618cf0fa50e03a326dfb76a5473a1
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765784"
---
# <a name="23---loading-fourthcoffee-customers"></a>2.3 - Chargement des clients FourthCoffee

Le code suivant charge les clients FourthCoffee :

```python
fc_customers_filePath = "/dbfs" + mountPoint + "/raw/fourthcoffee/Customers.csv"

fc_customers_pd = pd.read_csv(fc_customers_filePath)

fc_customers = fc_customers_pd[['CustomerID', 'LastName', 'FirstName', 'PhoneNumber', 'CreatedDate', 'UpdatedDate']]

fc_customers['CreatedDate'] = pd.to_datetime(fc_customers_pd['CreatedDate'], errors='coerce')
fc_customers['UpdatedDate'] = pd.to_datetime(fc_customers_pd['UpdatedDate'], errors='coerce')
fc_customers.PhoneNumber = fc_customers_pd.PhoneNumber.astype(str)

display(fc_customers)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Voici des informations détaillées sur ce code :

### <a name="loading-the-data"></a>Chargement des données

```python
fc_customers_filePath = "/dbfs" + mountPoint + "/raw/fourthcoffee/Customers.csv"

fc_customers_pd = pd.read_csv(fc_customers_filePath)
```

> Comme les données FourthCoffee sont enregistrées dans le lac de données sous forme de fichier CSV, vous le chargez en utilisant `pandas.read_csv`. Les données chargées sont déjà dans un dataframe Pandas, ce qui vous évite de les convertir à partir d’autres formats.

### <a name="selecting-the-relevant-data"></a>Sélection des données appropriées

```python
fc_customers = fc_customers_pd[['CustomerID', 'LastName', 'FirstName', 'PhoneNumber', 'CreatedDate', 'UpdatedDate']]
```

> Le dataframe contient également les adresses, mais dans notre contexte vous avez seulement besoin des informations des clients. Pour cette raison, nous utilisons le code ci-dessus afin de sélectionner uniquement les colonnes relatives aux informations des clients, comme pour le dataframe Southridge.

### <a name="conforming-the-data-types"></a>Mise en conformité des types de données

```python
fc_customers['CreatedDate'] = pd.to_datetime(fc_customers['CreatedDate'], errors='coerce')
fc_customers['UpdatedDate'] = pd.to_datetime(fc_customers['UpdatedDate'], errors='coerce')
fc_customers.PhoneNumber = fc_customers.PhoneNumber.astype(str)
```

> Dans le morceau de code ci-dessus, nous donnons au jeu de données un standard pour les champs susceptibles d’avoir des problèmes d’incohérence :
>
> - `CreatedDate` est converti en colonne `datetime`, ainsi que `UpdatedDate`
> - `PhoneNumber` est converti en colonne `string`

```python
display(fc_customers)
```

> La dernière étape de ce morceau de code est l’opération `display` qui affiche le résultat de la transformation de données à l’écran, sous forme de table.