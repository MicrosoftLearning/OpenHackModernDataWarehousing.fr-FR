---
ms.openlocfilehash: ad21585456e30ecfdc75eb0aa09580ecf1f80407
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765785"
---
# <a name="defining-useful-functions"></a>Définition des fonctions utiles

La toute première cellule de tous les notebooks de ce défi doit déclarer une fonction importante pour le reste du défi.

Comme chaque notebook distinct ne peut pas savoir si un autre a été exécuté avant lui, il doit vérifier que le système de fichiers du lac de données est monté sur le cluster Databricks afin que les fichiers que vous y avez stockés pendant l’ingestion puissent être chargés et leurs données transformées.

Elle déclare également une fonction qui vous aide à enregistrer le résultat de la transformation dans le lac de données au format Parquet.

Le code ci-dessous définit l’étape pour le reste de l’exécution du notebook :

```python
import pandas as pd

def mountFileSystem(containerName, storageAccountName):
  configs = {"fs.azure.account.auth.type": "OAuth",
       "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
       "fs.azure.account.oauth2.client.id": "<Service Principal Application ID>",
       "fs.azure.account.oauth2.client.secret": "<Service Principal Application Secret (or password)>",
       "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<Service Principal Tenant ID>/oauth2/token",
       "fs.azure.createRemoteFileSystemDuringInitialization": "true"}
  
  mountPoint = "/mnt/adls/" + containerName
  
  try:
    dbutils.fs.mount(
      source = "abfss://" + containerName + "@" + storageAccountName + ".dfs.core.windows.net",
      mount_point = mountPoint,
      extra_configs = configs
    )
    print(mountPoint + " mounted successfully")
  except:
    print("The mount " + mountPoint + " already exists.")

  return mountPoint

def saveAsParquet(dataFrame, filePath):
  df = sqlContext.createDataFrame(dataFrame)

  df.write.parquet(filePath, mode='overwrite')

  print(filePath + " saved successfully")

mountPoint = mountFileSystem("southridge", "<storage account name>")
print(mountPoint)
```

## <a name="detailing-the-code-above"></a>Détail du code ci-dessus

Le code ci-dessus est divisé en morceaux logiques, pour mieux pouvoir les expliquer.

### <a name="defining-the-mountfilesystem-function"></a>Définition de la fonction mountFileSystem

```python
import pandas as pd
```

> Vous utilisez Pandas pour charger et transformer les données que vous avez ingérées.
> Cette ligne importe le module Panda pour pouvoir l’utiliser dans tout le notebook.

```python
def mountFileSystem(containerName, storageAccountName):
  configs = {"fs.azure.account.auth.type": "OAuth",
       "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
       "fs.azure.account.oauth2.client.id": "<Service Principal Application ID>",
       "fs.azure.account.oauth2.client.secret": "<Service Principal Application Secret (or password)>",
       "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<Service Principal Tenant ID>/oauth2/token",
       "fs.azure.createRemoteFileSystemDuringInitialization": "true"}
```

> Cet ensemble de commandes effectue deux choses :
>
> - déclare la fonction `mountFileSystem` avec deux paramètres :
>     - `containerName` : nom du système de fichiers sur le stockage ADLS Gen2
>     - `storageAccountName` : nom du compte de stockage ADLS Gen2
> - définit la variable `configs` qui définit les paramètres d’accès du stockage ADLS Gen2 à travers le notebook qui s’exécute sur le cluster. Pour plus d’informations sur cette configuration, consultez [ce lien](https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-use-databricks-spark#create-a-file-system-and-mount-it).
>     - Notez les espaces réservés suivants :
>         - `<Service Principal Application ID>`
>         - `<Service Principal Application Secret (or password)>`
>         - `<Service Principal Tenant ID>`
>
> Utilisez les informations du principal de service que vous avez créé avant de les remplacer.

```python
mountPoint = "/mnt/adls/" + containerName
  
  try:
    dbutils.fs.mount(
      source = "abfss://" + containerName + "@" + storageAccountName + ".dfs.core.windows.net",
      mount_point = mountPoint,
      extra_configs = configs
    )
    print(mountPoint + " mounted successfully")
  except:
    print("The mount " + mountPoint + " already exists.")
```

> - La variable `mountPoint` est définie pour être utilisée
>     - pendant le montage du système de fichiers (`dbutils.fs.mount`)
>     - pour être renvoyée quand cette fonction est appelée, afin de faciliter la référence du système de fichiers après son montage.
> - Le traitement des exceptions est utile quand ce code n’est pas exécuté pour la première fois. Au lieu de remonter le système de fichiers, il écrit un message indiquant que le montage existe déjà.

```python
return mountPoint
```

> Peu importe si le système de fichiers était déjà monté ou non, la fonction retourne le chemin du système de fichiers afin que le code de consommateur trouve facilement les fichiers à consommer.

### <a name="defining-the-saveasparquet-function"></a>Définition de la fonction saveAsParquet

```python
def saveAsParquet(dataFrame, filePath):
  df = sqlContext.createDataFrame(dataFrame)
  
  df.write.parquet(filePath, mode='overwrite')
  
  print(filePath + " saved successfully")
```

> Le code ci-dessus déclare la fonction `saveAsParquet`, qui reçoit les paramètres suivants :
>
> - `dataFrame` : dataframe Pandas à enregistrer au format Parquet
> - `filePath` : chemin du fichier à enregistrer

```python
df = sqlContext.createDataFrame(dataFrame)
```

> Un dataframe Pandas ne peut pas s’écrire lui-même sous forme de fichier Parquet, contrairement à un dataframe Pyspark. C’est pourquoi la commande ci-dessous est exécutée pour convertir le dataframe Pandas en dataframe Pyspark.

```python
df.write.parquet(filePath, mode='overwrite')
  
print(filePath + " saved successfully")
```

> Les deux lignes de code ci-dessus écrivent le dataframe Pyspark dans un fichier Parquet dans le chemin spécifié, puis écrivent le message de réussite.

### <a name="calling-the-mountfilesystem-function"></a>Appel de la fonction mountFileSystem

Avant de faire quoique ce soit sur le notebook, le système de fichiers doit être monté. C’est pourquoi la fonction `mountFileSystem` est appelée juste après elle-même et les fonctions `saveAsParquet` sont déclarées dans la première cellule du notebook.

```python
...
mountPoint = mountFileSystem("southridge", "<storage account name>")
print(mountPoint)
```

Dans cet exemple, `southridge` est le conteneur Data Lake Storage Gen2 du système de fichiers où toutes les données ont été ingérées à travers Data Factory.