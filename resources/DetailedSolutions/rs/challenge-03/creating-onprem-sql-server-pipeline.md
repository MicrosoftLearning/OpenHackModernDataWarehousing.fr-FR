---
ms.openlocfilehash: 187ab7f7362e0fb16af55867f1cf59766790dc04
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765765"
---
# <a name="creating-the-pipeline-to-copy-data-from-on-prem-sql-to-adls"></a>Création du pipeline pour copier des données de l’instance SQL locale vers ADLS

En utilisant les références ci-dessous :

- [Informations de référence sur le format JSON du pipeline](https://docs.microsoft.com/fr-fr/azure/data-factory/concepts-pipelines-activities#pipeline-json)
- [Activité ForEach dans Azure Data Factory](https://docs.microsoft.com/fr-fr/azure/data-factory/control-flow-for-each-activity)
- [Propriétés de l’activité Copy pour SQL Server](https://docs.microsoft.com/fr-fr/azure/data-factory/connector-sql-server#copy-activity-properties)

Vous créez un pipeline pour copier les données de la base de données SQL Server locale vers le lac de données Azure.

Créez un fichier JSON nommé `SQL-Server-Pipeline.json` avec la structure suivante :

```json
{
    "name": "<pipeline name>",
    "properties": {
        "activities": [
            {
                "name": "ForEach_Table",
                "type": "ForEach",
                "typeProperties": {
                    "items": {
                        "value": "@pipeline().parameters.items",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "Copy_Rows",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "userProperties": [
                                {
                                    "name": "Source",
                                    "value": "@{item().source.tableName}"
                                },
                                {
                                    "name": "Destination",
                                    "value": "@{pipeline().parameters.destinationFolder}/@{item().destination.fileName}"
                                }
                            ],
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource"
                                },
                                "sink": {
                                    "type": "AzureBlobFSSink"
                                },
                                "enableStaging": false
                            },
                            "inputs": [
                                {
                                    "referenceName": "<source dataset name>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "tableName": "@item().source.tableName"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "<sink dataset name>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "fileName": "@item().destination.fileName",
                                        "filePath": "@pipeline().parameters.destinationFolder"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "items": {
                "type": "Array",
                "defaultValue": [
                    {
                        "source": {
                            "tableName": "[dbo].[Table1]"
                        },
                        "destination": {
                            "fileName": "Table1"
                        }
                    },
                    {
                        "source": {
                            "tableName": "[dbo].[Table2]"
                        },
                        "destination": {
                            "fileName": "Table2"
                        }
                    },
                    {
                        "source": {
                            "tableName": "[dbo].[TableN]"
                        },
                        "destination": {
                            "fileName": "TableN"
                        }
                    }
                ]
            },
            "destinationFolder": {
                "type": "String",
                "defaultValue": "<folder name>"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

Attention, remplacez les valeurs suivantes :

- `<pipeline name>` : nom de votre pipeline
- `<source dataset name>` : nom du jeu de données source
- `<sink dataset name>` : nom du jeu de données de destination (JSON)
- `parameters:items` : liste des tables à copier dans le récepteur.
Vous remarquez qu’il y a des noms de table de type `[dbo].[Table1]`. Remplacez-les par les noms des tables que vous copiez et ajoutez de nouveaux éléments en fonction des besoins.
- `destinationFolder:defaultValue` : remplacez `<folder name>` par le nom du dossier où stocker les fichiers VanArsdel.

> Par ailleurs, concernant `parameters:items`, ce pipeline s’appuie sur une instruction très importante : `ForEach`. **ForEach** est une instruction de boucle qui répète une ou plusieurs activités (dans ce cas, `Copy_Rows`) sur chaque élément donné.
>
> Pour plus d’informations sur l’activité `ForEach`, consultez [ce lien](https://docs.microsoft.com/fr-fr/azure/data-factory/control-flow-for-each-activity).

Une fois le fichier correctement modifié, exécutez les commandes PowerShell ci-dessous pour créer le pipeline sur Data Factory :

```powershell
$dataFactoryName = "<data factory name>"
$resourceGroupName = "<resource group name>"
$pipelineName = "<pipeline name>"

Set-AzDataFactoryV2Pipeline `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $pipelineName `
    -DefinitionFile ".\SQL-Server-Pipeline.json"
```