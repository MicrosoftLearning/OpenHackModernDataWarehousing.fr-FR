---
ms.openlocfilehash: 1ecf0eebe8a77e4b52126e498a973a6a4ce7c2fb
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765847"
---
# <a name="22-loading-vanarsdel-movies-and-actors"></a>2.2 Chargement des films et des acteurs VanArsdel

Le code suivant charge les films et les acteurs de VanArsdel :

```python
va_movies_filepath = mountPoint + "/raw/vanarsdel/Movies.json"
va_actors_filepath = mountPoint + "/raw/vanarsdel/Actors.json"
va_movieactors_filepath = mountPoint + "/raw/vanarsdel/MovieActors.json"

va_movies_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_movies_filepath)
va_actors_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_actors_filepath)
va_movieactors_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_movieactors_filepath)

va_movies_pd = va_movies_raw.toPandas()
va_actors_pd = va_actors_raw.toPandas()
va_movieactors_pd = va_movieactors_raw.toPandas()

va_all_movies = pd.merge(va_movieactors_pd, va_movies_pd, on='MovieID')
va_all_movies = pd.merge(va_all_movies, va_actors_pd, on='ActorID')

va_all_movies = va_all_movies.rename(index=str, columns={'Category': 'Genre', 'RunTimeMin': 'RuntimeMin', 'Gender': 'ActorGender'})

va_all_movies['AvailabilityDate'] = 'None'
va_all_movies['StreamingAvailabilityDate'] = 'None'
va_all_movies['ReleaseYear'] = 'None'
va_all_movies['Tier'] = 'None'

va_all_movies.RuntimeMin = va_all_movies.RuntimeMin.astype(str)

va_all_movies = va_all_movies[['MovieID', 'MovieTitle', 'Genre', 'ReleaseDate', 'AvailabilityDate', 'StreamingAvailabilityDate', 'ReleaseYear', 'Tier', 'Rating', 'RuntimeMin', 'MovieActorID', 'ActorID', 'ActorName', 'ActorGender']]

va_all_movies.dtypes
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Voici des informations détaillées sur ce code :

### <a name="loading-the-data"></a>Chargement des données

```python
va_movies_filepath = mountPoint + "/raw/vanarsdel/Movies.json"
va_actors_filepath = mountPoint + "/raw/vanarsdel/Actors.json"
va_movieactors_filepath = mountPoint + "/raw/vanarsdel/MovieActors.json"

va_movies_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_movies_filepath)
va_actors_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_actors_filepath)
va_movieactors_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(va_movieactors_filepath)
```

> Comme les données VanArsdel sont stockées dans le lac de données sous forme de fichiers JSON, vous utilisez `spark.read.json` pour charger les données dans un dataframe Pyspark.

```python
va_movies_pd = va_movies_raw.toPandas()
va_actors_pd = va_actors_raw.toPandas()
va_movieactors_pd = va_movieactors_raw.toPandas()
```

> Vous convertissez les dataframes Pyspark en dataframes Pandas pour faciliter la transformation.

### <a name="denormalizing-the-movies-and-actors-relationship"></a>Dénormalisation de la relation films et acteurs

```python
va_all_movies = pd.merge(va_movieactors_pd, va_movies_pd, on='MovieID')
va_all_movies = pd.merge(va_all_movies, va_actors_pd, on='ActorID')
```

> Une fois toutes les données chargées, vous devez fusionner les films et les acteurs en fonction de la table de mappage `MoviesActors`.

```python
va_all_moviesactors = va_all_moviesactors.rename(index=str, columns={'Category': 'Genre', 'RunTimeMin': 'RuntimeMin', 'Gender': 'ActorGender'})
```

> Pour normaliser les noms de colonne, vous renommez ces colonnes afin qu’elles correspondent au jeu de données Southridge précédemment chargé.

### <a name="conforming-data-types-and-columns"></a>Mise en conformité des types de données et des colonnes

```python
va_all_moviesactors['AvailabilityDate'] = 'None'
va_all_moviesactors['StreamingAvailabilityDate'] = 'None'
va_all_moviesactors['ReleaseYear'] = 'None'
va_all_moviesactors['Tier'] = 'None'
```

> Comme d’autres dataframes peuvent avoir des valeurs dans ces champs, vous les ajoutez à ce dataframe avec la valeur `None`, au lieu de les supprimer dans les autres dataframes.

```python
va_all_moviesactors.RuntimeMin = va_all_moviesactors.RuntimeMin.astype(str)
```

> Cette colonne existante peut avoir un type de données différent dans les autres dataframes. Pour gérer ce cas, vous utilisez une chaîne pour cette colonne, sur tous les dataframes.

```python
va_all_moviesactors = va_all_moviesactors[['MovieID', 'MovieTitle', 'Genre', 'ReleaseDate', 'AvailabilityDate', 'StreamingAvailabilityDate', 'ReleaseYear', 'Tier', 'Rating', 'RuntimeMin', 'MovieActorID', 'ActorID', 'ActorName', 'ActorGender']]'ActorGender']]
```

> Une fois que toutes les données ont été transformées, vous sélectionnez les colonnes pertinentes (et cohérentes sur tous les dataframes) et les définissez sur `va_all_moviesactors`, dans un ordre plus facile à consulter.