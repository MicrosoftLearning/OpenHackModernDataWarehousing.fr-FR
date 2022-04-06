---
ms.openlocfilehash: 62c5c7635ac90f4ffa7cec309b18a0175795a443
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765770"
---
# <a name="21-loading-southridge-movies-and-actors"></a>2.1 Chargement des films et des acteurs Southridge

Le code suivant charge les films et les acteurs de Southridge :

```python
sr_movies_filepath = mountPoint + "/raw/moviescatalog/movies.json"

sr_movies_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(sr_movies_filepath)
sr_movies_pd = sr_movies_raw.toPandas()

sr_movies_pd = sr_movies_pd[['actors', 'availabilityDate', 'genre', 'id', 'rating', 'releaseYear', 'runtime', 'streamingAvailabilityDate', 'tier', 'title']]

movieactors = sr_movies_pd[['id', 'actors']]
movies = sr_movies_pd[['id', 'title', 'genre', 'availabilityDate', 'rating', 'releaseYear', 'runtime', 'streamingAvailabilityDate', 'tier']]

import numpy as np

actorslist = movieactors.actors.values.tolist()
actorcountbymovie = [len(r) for r in actorslist]
explodedmovieids = np.repeat(movieactors.id, actorcountbymovie)

movieactors = pd.DataFrame(np.column_stack((explodedmovieids, np.concatenate(actorslist))), columns=movieactors.columns)

sr_all_moviesactorsactors = pd.merge(movies, movieactors, on='id')

sr_all_moviesactorsactors = sr_all_moviesactorsactors.rename(index=str, columns={'id': 'MovieID', 'title': 'MovieTitle', 'genre': 'Genre', 'availabilityDate': 'AvailabilityDate', 'rating': 'Rating', 'releaseYear': 'ReleaseYear', 'runtime': 'RuntimeMin', 'streamingAvailabilityDate': 'StreamingAvailabilityDate', 'tier': 'Tier', 'actors': 'ActorName'})

sr_all_moviesactorsactors['ActorID'] = 'None'
sr_all_moviesactorsactors['MovieActorID'] = 'None'
sr_all_moviesactorsactors['ActorGender'] = 'None'
sr_all_moviesactorsactors['ReleaseDate'] = 'None'

sr_all_moviesactorsactors.ReleaseYear = sr_all_moviesactorsactors.ReleaseYear.astype(str)
sr_all_moviesactorsactors.Tier = sr_all_moviesactorsactors.Tier.astype(str)
sr_all_moviesactorsactors.RuntimeMin = sr_all_moviesactorsactors.RuntimeMin.astype(str)

sr_all_moviesactorsactors = sr_all_moviesactorsactors[['MovieID', 'MovieTitle', 'Genre', 'ReleaseDate', 'AvailabilityDate', 'StreamingAvailabilityDate', 'ReleaseYear', 'Tier', 'Rating', 'RuntimeMin', 'MovieActorID', 'ActorID', 'ActorName', 'ActorGender']]
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Voici des informations détaillées sur ce code :

### <a name="loading-the-data"></a>Chargement des données

```python
sr_movies_filepath = mountPoint + "/raw/movies/movies.json"

sr_movies_raw = spark.read.option("multiLine", "true").options(header='true', inferschema='true').json(sr_movies_filepath)
```

> Le catalogue de films de Southridge est stocké dans le lac de données sous forme de fichier JSON, tel qu’il a été précédemment chargé à partir d’une collection CosmosDB. Par conséquent, vous utilisez `spark.read.json` pour charger les données dans un dataframe Pyspark.

```python
sr_movies_pd = sr_movies_raw.toPandas()
```

> Vous convertissez le dataframe Pyspark en dataframe Pandas pour faciliter la transformation.

```python
sr_movies_pd = sr_movies_pd[['actors', 'availabilityDate', 'genre', 'id', 'rating', 'releaseYear', 'runtime', 'streamingAvailabilityDate', 'tier', 'title']]
```

> Comme un dataframe qui a chargé un fichier JSON contient également des colonnes spécifiques de ce fichier JSON, vous devez sélectionner uniquement les colonnes qui concernent cette étendue.

```python
movieactors = sr_movies_pd[['id', 'actors']]
movies = sr_movies_pd[['id', 'title', 'genre', 'availabilityDate', 'rating', 'releaseYear', 'runtime', 'streamingAvailabilityDate', 'tier']]
```

> Comme les acteurs sont stockés sous forme de tableau pour chaque film, vous devez d’abord séparer ces deux informations afin de pouvoir *éclater* les acteurs, et les regrouper de nouveau par la suite. `movieactors` contient uniquement les informations des acteurs et `movies` contient uniquement les données des films.

### <a name="denormalizing-the-movies-and-actors-relationship"></a>Dénormalisation de la relation films et acteurs

```python
import numpy as np

actorslist = movieactors.actors.values.tolist()
actorcountbymovie = [len(r) for r in actorslist]
explodedactorids = np.repeat(movieactors.id, actorcountbymovie)
```

> Ce morceau de code est important pour cette étendue. En bref, vous éclatez le tableau des acteurs de chaque film dans une table `movieactors` distincte temporaire afin de pouvoir la fusionner avec les données de films.
>
> Mais avant cela, comme vous utilisez `numpy` pour extraire le dataframe des acteurs, vous devez l’importer en utilisant `import numpuy as np`.

```python
actorslist = movieactors.actors.values.tolist()
```

> Vous commencez par obtenir la liste brute des noms d’acteur du dataframe `movieactors`.

```python
actorcountbymovie = [len(r) for r in actorslist]
```

> Vous identifiez ensuite le nombre d’acteurs de chaque film.

```python
explodedmovieids = np.repeat(movieactors.id, actorcountbymovie)
```

> Enfin, vous créez une liste d’ID des films qui répète chaque élément pour chaque acteur du film. Par exemple : si le film *Movie 1* a deux acteurs *Actor A*, *Actor B* et que le *Movie 2* en a trois, *Actor A*, *Actor C* et *Actor D*, le dataframe final ressemble à ceci :

|MovieID|ActorID|MovieName|ActorName|
|-------|-------|---------|---------|
|1|Un|Movie 1|Actor A|
|1|B|Movie 1|Actor B|
|2|A|Movie 2|Actor A|
|2|C|Movie 2|Actor C|
|2|D|Movie 2|Actor D|

```python
movieactors = pd.DataFrame(np.column_stack((explodedmovieids, np.concatenate(actorslist))), columns=movieactors.columns)
```

> Ce morceau de code crée un dataframe Pandas avec tous les ID de films (`explodedmovieids`) et leurs noms d’acteur (`np.concatenate(actorslist)`)

### <a name="bringing-movies-and-actors-together"></a>Regrouper les films et les acteurs

```python
sr_all_moviesactors = pd.merge(movies, movieactors, on='id')
```

Enfin, les films et les acteurs sont regroupés en fonction de l’`id` du film

```python
sr_all_moviesactors = sr_all_moviesactors.rename(index=str, columns={'id': 'MovieID', 'title': 'MovieTitle', 'genre': 'Genre', 'availabilityDate': 'AvailabilityDate', 'rating': 'Rating', 'releaseYear': 'ReleaseYear', 'runtime': 'RuntimeMin', 'streamingAvailabilityDate': 'StreamingAvailabilityDate', 'tier': 'Tier', 'actors': 'ActorName'})
```

> Avec les films et les acteurs dénormalisés à portée de main, le code ci-dessus renomme les colonnes pour qu’elles correspondent à un modèle spécifique utilisé par tous les dataframes de films et d’acteurs.

### <a name="conforming-the-data-types"></a>Mise en conformité des types de données

```python
sr_all_moviesactors['ActorID'] = 'None'
sr_all_moviesactors['MovieActorID'] = 'None'
sr_all_moviesactors['ActorGender'] = 'None'
```

> Comme d’autres dataframes peuvent avoir des valeurs dans ces champs, vous les ajoutez à ce dataframe avec la valeur `None`, au lieu de les supprimer dans les autres dataframes.

```python
sr_all_moviesactors.ReleaseYear = sr_all_moviesactors.ReleaseYear.astype(str)
sr_all_moviesactors.Tier = sr_all_moviesactors.Tier.astype(str)
sr_all_moviesactors.RuntimeMin = sr_all_moviesactors.RuntimeMin.astype(str)
```

> Ces colonnes existantes peuvent avoir des types de données différents dans les autres dataframes. Pour le gérer, vous utilisez une chaîne pour l’ensemble des types, sur tous les dataframes.

```python
sr_all_moviesactors = sr_all_moviesactors[['MovieID', 'MovieTitle', 'Genre', 'AvailabilityDate', 'StreamingAvailabilityDate', 'ReleaseYear', 'Tier', 'Rating', 'RuntimeMin', 'MovieActorID', 'ActorID', 'ActorName', 'ActorGender']]
```

> Une fois que toutes les données ont été transformées, vous sélectionnez les colonnes pertinentes (et cohérentes sur tous les dataframes) et les définissez sur `sr_all_moviesactors`, dans un ordre plus facile à consulter.