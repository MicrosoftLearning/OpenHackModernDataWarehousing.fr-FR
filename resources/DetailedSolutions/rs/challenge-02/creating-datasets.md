---
ms.openlocfilehash: c92ae1db0dd0204be596af62e44851cb9769c886
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765813"
---
# <a name="creating-the-datasets-on-data-factory"></a>Création des jeux de données sur Data Factory

Lorsque vous créez un jeu de données, vous en définissez un qui représente les données depuis ou vers lesquelles copier. Chaque jeu de données est associé à un service lié, que nous avons créé à l’étape précédente.

## <a name="01---create-the-dataset-for-the-cloudsales"></a>01 - Créer le jeu de données pour CloudSales

> Pour nommer des jeux de données, vous pouvez utiliser uniquement des lettres, des chiffres et des « _ ».

En utilisant [cette référence](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-powershell#create-a-dataset), créez un fichier JSON appelé `CloudSales-Dataset.json` avec cette structure :

```json
{
    "name": "<dataset name>",
    "properties": {
        "linkedServiceName": {
            "referenceName": "<CloudSales Linked Service Name>",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "tableName": {
                "type": "String"
            }
        },
        "type": "AzureSqlTable",
        "typeProperties": {
            "tableName": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

Veillez à utiliser le nom correct pour `<CloudSales Linked Service Name>`.
Veillez aussi à nommer correctement votre jeu de données, par exemple `CloudSales_SQL`.

Utilisez maintenant les commandes ci-dessous pour créer le jeu de données dans Data Factory :

```powershell
$dataFactoryName = "<data factory name>"
$resourceGroupName = "<resource group name>"
$datasetName = "<dataset name>"

Set-AzDataFactoryV2Dataset `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $datasetName `
    -DefinitionFile ".\CloudSales-Dataset.json"
```

## <a name="02---create-the-dataset-for-the-cloudstreaming"></a>02 - Créer le jeu de données pour CloudStreaming

Répétez les étapes ci-dessus pour créer un jeu de données pour la base de données CloudStreaming.
Vous devez vous assurer que les choses suivantes sont différentes :

- `linkedService:ReferenceName` dans le fichier de définition JSON pour le jeu de données
- `name` pour le jeu de données dans le fichier de définition JSON
- `<dataset name>` dans le script PowerShell pour créer le jeu de données (qui doit être le même nom que celui que vous avez utilisé dans le fichier de définition JSON)

## <a name="03---create-the-dataset-for-the-movies-catalog-on-cosmosdb"></a>03 - Créer le jeu de données pour le catalogue Movies sur CosmosDB

Avec [cette référence](https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-cosmos-db#dataset-properties), vous allez créer un jeu de données pour définir le format de données de la base de données Movies sur Cosmos DB.

Créez un fichier JSON nommé `Movies-Dataset.json` avec la structure suivante :

```json
{
    "name": "<dataset name>",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Movies Linked Service Name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

Veillez à utiliser le nom correct pour `<Movies Linked Service Name>`.
N’oubliez pas non plus de remplacer `<collection name>` par le nom de la collection de bases de données CosmosDB que vous allez ingérer avec ce processus. Dans cet exemple, nous allons utiliser `movies`.

Utilisez maintenant les commandes ci-dessous pour créer le jeu de données dans Data Factory :

```powershell
$dataFactoryName = "<data factory name>"
$resourceGroupName = "<resource group name>"
$datasetName = "<dataset name>"

Set-AzDataFactoryV2Dataset `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $datasetName `
    -DefinitionFile ".\Movies-Dataset.json"
```

## <a name="04---create-the-sink-dataset-for-azure-sql-database-tables"></a>04 - Créer le jeu de données récepteur pour des tables Azure SQL Database

Avec [cette référence](https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-storage#dataset-properties), vous allez créer un jeu de données pour décrire le format de données pour Azure Data Lake Storage Gen2 comme récepteur pour les fichiers parquet. Ce jeu de données va être utilisé pour copier les tables Azure SQL Database.

> Pour le cas Southridge, qui copie les données d’Azure SQL, nous pouvons déjà copier les données au format Parquet, ce qui facilitera leur chargement pour leur transformation ultérieure.

Créez un fichier JSON appelé `ADLS-Dataset-Parquet.json` avec la structure suivante :

```json
{
    "name": "<dataset name>",
    "properties": {
        "linkedServiceName": {
            "referenceName": "<ADLS Linked Service name>",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "tableName": {
                "type": "String"
            },
            "filePath": {
                "type": "string"
            }
        },
        "type": "AzureBlobFSFile",
        "typeProperties": {
            "format": {
                "type": "ParquetFormat"
            },
            "fileName": {
                "value": "@{concat(dataset().tableName, '.parquet')}",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@dataset().filePath",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

Veillez à utiliser le nom correct du service lié pour remplacer `<ADLS Linked Service name>`.

- `@dataset().filePath` servira à définir la structure de dossiers où le pipeline enregistrera le fichier.
- `@dataset().tableName` recevra le nom de la table afin qu’il puisse enregistrer un fichier parquet pour celui-ci.

Utilisez maintenant les commandes ci-dessous pour créer le jeu de données dans Data Factory :

```powershell
$dataFactoryName = "<data factory name>"
$resourceGroupName = "<resource group name>"
$datasetName = "<dataset name>"

Set-AzDataFactoryV2Dataset `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $datasetName `
    -DefinitionFile ".\ADLS-Dataset-Parquet.json"
```

## <a name="05---create-the-sink-dataset-for-the-movies-catalog"></a>05 - Créer le jeu de données récepteur pour le catalogue Movies

Avec [cette référence](https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-storage#dataset-properties), vous allez créer un jeu de données pour décrire le format de données pour Azure Data Lake Storage Gen2 en tant que récepteur pour les fichiers JSON.

Créez un fichier JSON appelé `ADLS-Dataset-JSON.json` avec la structure suivante :

```json
{
    "name": "<dataset name>",
    "properties": {
        "linkedServiceName": {
            "referenceName": "<ADLS Linked Service name>",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "fileName": {
                "type": "String"
            },
            "filePath": {
                "type": "string"
            }
        },
        "type": "AzureBlobFSFile",
        "typeProperties": {
            "format": {
                "type": "JsonFormat",
                "filePattern": "arrayOfObjects"
            },
            "fileName": {
                "value": "@{concat(dataset().fileName, '.json')}",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@dataset().filePath",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

Veillez à utiliser le nom correct pour `<CloudSales Linked Service Name>`.
Veillez aussi à nommer correctement votre jeu de données, par exemple `ADLS_CloudSales_JSON`.

Utilisez maintenant les commandes ci-dessous pour créer le jeu de données dans Data Factory :

```powershell
$dataFactoryName = "<data factory name>"
$resourceGroupName = "<resource group name>"
$datasetName = "<dataset name>"

Set-AzDataFactoryV2Dataset `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $datasetName `
    -DefinitionFile ".\ADLS-Dataset-JSON.json"
```