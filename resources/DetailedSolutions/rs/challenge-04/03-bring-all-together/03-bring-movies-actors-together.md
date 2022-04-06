---
ms.openlocfilehash: 8144ebfae887df737b1ef38920465c064f1cc111
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140766002"
---
# <a name="31---bringing-all-movies-and-actors-together"></a>3.1 - Rassembler tous les films et les acteurs

Après avoir chargé les films et les acteurs des trois systèmes sources, les dernières étapes à effectuer sont les suivantes :

- Ajouter une colonne à chaque dataframe pour suivre les systèmes sources
- Concaténer les trois dataframes

C’est ce que fait le code ci-dessous :

```python
sr_all_moviesactors['SourceSystem'] = 'southridge'
va_all_moviesactors['SourceSystem'] = 'vanarsdel'
fc_all_moviesactors['SourceSystem'] = 'fourthcoffee'

moviesactors_frame = [sr_all_moviesactors, va_all_moviesactors, fc_all_moviesactors]

all_moviesactors = pd.concat(moviesactors_frame)

display(all_moviesactors)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

```python
sr_all_moviesactors['SourceSystem'] = 'southridge'
va_all_moviesactors['SourceSystem'] = 'vanarsdel'
fc_all_moviesactors['SourceSystem'] = 'fourthcoffee'
```

> Pour chacun de vos dataframes de films/acteurs, ce code ajoute une nouvelle colonne appelée `SourceSystem` et définit sa valeur avec le nom du système source.

```python
moviesactors_frame = [sr_all_moviesactors, va_all_moviesactors, fc_all_moviesactors]

all_moviesactors = pd.concat(moviesactors_frame)
```

> La collection de dataframes `moviesactors_frame` est créée pour être ensuite concaténée et le résultat est attribué à la variable `all_moviesactors`.

```python
display(all_moviesactors)
```

> Le dataframe `all_moviesactors` s’affiche pour la validation.