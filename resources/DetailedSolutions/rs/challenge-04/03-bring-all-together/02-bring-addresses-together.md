---
ms.openlocfilehash: 9877ceaf24da6e80dca3f0b8723af2865a75554e
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765840"
---
# <a name="31---bringing-all-addresses-together"></a>3.1 - Regrouper toutes les adresses

Après avoir chargé les adresses des trois systèmes sources, les dernières étapes à effectuer sont les suivantes :

- Ajouter une colonne à chaque dataframe pour suivre les systèmes sources
- Concaténer les trois dataframes

C’est ce que fait le code ci-dessous :

```python
sr_addresses['SourceSystem'] = 'southridge'
va_addresses['SourceSystem'] = 'vanarsdel'
fc_addresses['SourceSystem'] = 'fourthcoffee'
addresses_frame = [sr_addresses, va_addresses, fc_addresses]

all_addresses = pd.concat(addresses_frame)

display(all_addresses)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

```python
sr_addresses['SourceSystem'] = 'southridge'
va_addresses['SourceSystem'] = 'vanarsdel'
fc_addresses['SourceSystem'] = 'fourthcoffee'
```

> Pour chacun de vos dataframes d’adresses, ce code ajoute une nouvelle colonne appelée `SourceSystem` et définit sa valeur avec le nom du système source.

```python
addresses_frame = [sr_addresses, va_addresses, fc_addresses]

all_addresses = pd.concat(addresses_frame)
```

> La collection de dataframes `addresses_frame` est créée pour être ensuite concaténée et le résultat est attribué à la variable `all_addresses`.

```python
display(all_addresses)
```

> Le dataframe `all_addresses` s’affiche pour la validation.