---
ms.openlocfilehash: e99184e6d7f456d523ed385dc31e3182487fa498
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765783"
---
# <a name="23-loading-fourthcoffee-movies-and-actors"></a>2.3 Chargement des films et des acteurs FourthCoffee

Le code suivant charge les films et les acteurs de FourthCoffee :

```python
fc_movies_filepath = "/dbfs/" + mountPoint + "/raw/fourthcoffee/Movies.csv"
fc_actors_filepath = "/dbfs/" + mountPoint + "/raw/fourthcoffee/Actors.csv"
fc_movieactors_filepath = "/dbfs/" + mountPoint + "/raw/fourthcoffee/MovieActors.csv"

fc_movies_pd = pd.read_csv(fc_movies_filepath)
fc_actors_pd = pd.read_csv(fc_actors_filepath)
fc_movieactors_pd = pd.read_csv(fc_movieactors_filepath)

fc_all_moviesactors = pd.merge(fc_movieactors_pd, fc_movies_pd, on='MovieID')
fc_all_moviesactors = pd.merge(fc_all_moviesactors, fc_actors_pd, on='ActorID')

fc_all_moviesactors = fc_all_moviesactors.rename(index=str, columns={'Category': 'Genre', 'RunTimeMin': 'RuntimeMin', 'Gender': 'ActorGender'})

fc_all_moviesactors['AvailabilityDate'] = 'None'
fc_all_moviesactors['StreamingAvailabilityDate'] = 'None'
fc_all_moviesactors['ReleaseYear'] = 'None'
fc_all_moviesactors['Tier'] = 'None'

fc_all_moviesactors.RuntimeMin = fc_all_moviesactors.RuntimeMin.astype(str)

fc_all_moviesactors = fc_all_moviesactors[['MovieID', 'MovieTitle', 'Genre', 'ReleaseDate', 'AvailabilityDate', 'StreamingAvailabilityDate', 'ReleaseYear', 'Tier', 'Rating', 'RuntimeMin', 'MovieActorID', 'ActorID', 'ActorName', 'ActorGender']]

display(fc_all_moviesactors)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Voici des informations détaillées sur ce code :

### <a name="loading-the-data"></a>Chargement des données

```python
fc_movies_filepath = mountPoint + "/raw/fourthcoffee/Movies.json"
fc_actors_filepath = mountPoint + "/raw/fourthcoffee/Actors.json"
fc_movieactors_filepath = mountPoint + "/raw/fourthcoffee/MovieActors.json"

fc_movies_pd = pd.read_csv(fc_movies_filepath)
fc_actors_pd = pd.read_csv(fc_actors_filepath)
fc_movieactors_pd = pd.read_csv(fc_movieactors_filepath)
```

> Comme les données FourthCoffee sont stockées dans le lac de données sous forme de fichiers CSV, vous utilisez `pandas.read_csv` pour charger les données dans un dataframe Pandas.
>
> Cela facilite le processus, car vous n’avez pas besoin de transformer un dataframe Pyspark en dataframe Pandas.

### <a name="denormalizing-the-movies-and-actors-relationship"></a>Dénormalisation de la relation films et acteurs

```python
fc_all_movies = pd.merge(fc_movieactors_pd, fc_movies_pd, on='MovieID')
fc_all_movies = pd.merge(fc_all_movies, fc_actors_pd, on='ActorID')
```

> Une fois toutes les données chargées, vous devez fusionner les films et les acteurs en fonction de la table de mappage `MoviesActors`.

```python
fc_all_moviesactors = fc_all_moviesactors.rename(index=str, columns={'Category': 'Genre', 'RunTimeMin': 'RuntimeMin', 'Gender': 'ActorGender'})
```

> Pour normaliser les noms de colonne, vous renommez ces colonnes afin qu’elles correspondent au jeu de données Southridge précédemment chargé.

### <a name="conforming-data-types-and-columns"></a>Mise en conformité des types de données et des colonnes

```python
fc_all_moviesactors['AvailabilityDate'] = 'None'
fc_all_moviesactors['StreamingAvailabilityDate'] = 'None'
fc_all_moviesactors['ReleaseYear'] = 'None'
fc_all_moviesactors['Tier'] = 'None'
```

> Comme d’autres dataframes peuvent avoir des valeurs dans ces champs, vous les ajoutez à ce dataframe avec la valeur `None`, au lieu de les supprimer dans les autres dataframes.

```python
fc_all_moviesactors.RuntimeMin = fc_all_moviesactors.RuntimeMin.astype(str)
```

> Cette colonne existante peut avoir un type de données différent dans les autres dataframes. Pour gérer ce cas, vous utilisez une chaîne pour cette colonne, sur tous les dataframes.

```python
fc_all_moviesactors = fc_all_moviesactors[['MovieID', 'MovieTitle', 'Genre', 'ReleaseDate', 'AvailabilityDate', 'StreamingAvailabilityDate', 'ReleaseYear', 'Tier', 'Rating', 'RuntimeMin', 'MovieActorID', 'ActorID', 'ActorName', 'ActorGender']]'ActorGender']]
```

> Une fois que toutes les données ont été transformées, vous sélectionnez les colonnes pertinentes (et cohérentes sur tous les dataframes) et les définissez sur `fc_all_moviesactors`, dans un ordre plus facile à consulter.