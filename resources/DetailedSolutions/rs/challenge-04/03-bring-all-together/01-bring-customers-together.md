---
ms.openlocfilehash: 7362a7338ee78639b13c481f725e2e86b645fe05
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765782"
---
# <a name="31---bringing-all-customers-together"></a>3.1 - Regrouper tous les clients

Après avoir chargé les clients des trois systèmes sources, les dernières étapes à effectuer sont les suivantes :

- Ajouter une colonne à chaque dataframe pour suivre les systèmes sources
- Concaténer les trois dataframes

C’est ce que fait le code ci-dessous :

```python
sr_customers['SourceSystem'] = 'southridge'
va_customers['SourceSystem'] = 'vanarsdel'
fc_customers['SourceSystem'] = 'fourthcoffee'
customers_frame = [sr_customers, va_customers, fc_customers]

all_customers = pd.concat(customers_frame)

display(all_customers)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

```python
sr_customers['SourceSystem'] = 'southridge'
va_customers['SourceSystem'] = 'vanarsdel'
fc_customers['SourceSystem'] = 'fourthcoffee'
```

> Pour chacun de vos dataframes de clients, ce code ajoute une nouvelle colonne appelée `SourceSystem` et définit sa valeur avec le nom du système source.

```python
customers_frame = [sr_customers, va_customers, fc_customers]

all_customers = pd.concat(customers_frame)
```

> La collection de dataframes `customers_frame` est créée pour être ensuite concaténée et le résultat est attribué à la variable `all_customers`.

```python
display(all_customers)
```

> Le dataframe `all_customers` s’affiche pour la validation.