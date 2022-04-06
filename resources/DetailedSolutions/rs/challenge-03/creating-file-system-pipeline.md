---
ms.openlocfilehash: 51b01ebe45757aef564461eb2e23632eb2afcfb6
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765849"
---
# <a name="creating-the-pipeline-to-copy-data-from-on-prem-sql-to-adls"></a>Création du pipeline pour copier des données de l’instance SQL locale vers ADLS

En utilisant les références ci-dessous :

- [Informations de référence sur le format JSON du pipeline](https://docs.microsoft.com/en-us/azure/data-factory/concepts-pipelines-activities#pipeline-json)
- [Activité ForEach dans Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/control-flow-for-each-activity)
- [Propriétés de l’activité Copy](https://docs.microsoft.com/en-us/azure/data-factory/connector-file-system#copy-activity-properties)

Vous créez un pipeline pour copier les données du système de fichiers local vers le lac de données Azure.

Créez un fichier JSON nommé `File-System-Pipeline.json` avec la structure suivante :


```json
{
    "name": "<pipeline name>",
    "properties": {
        "activities":[
            {
                "name": "ForEach_File",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.files",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "CopyFromFileSystem",
                            "type": "Copy",
                            "inputs": [
                                {
                                    "referenceName": "<source dataset name>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "fileName": "@item()"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "<sink dataset name>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "fileName": "@item()",
                                        "filePath": "@pipeline().parameters.destinationFolder"
                                    }
                                }
                            ],
                            "typeProperties": {
                                "source": {
                                    "type": "FileSystemSource",
                                    "recursive": true
                                },
                                "sink": {
                                    "type": "AzureBlobFSSink"
                                }
                            }
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "files": {
                "type": "Array",
                "defaultValue": [
                    "file1.ext",
                    "file2.ext",
                    "file3.ext",
                    "fileN.ext"
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
- `<sink dataset name>` : nom du jeu de données de destination (binaire)
- `parameters:files` : liste des fichiers à copier dans le récepteur.
Vous remarquez qu’il y a des noms de fichiers de type `fileN.ext`. Remplacez-les par les noms de fichier et leurs extensions que vous copiez et ajoutez de nouveaux éléments en fonction des besoins.
- `destinationFolder:defaultValue` : remplacez `<folder name>` par le nom du dossier où stocker les fichiers FourthCoffee.

Une fois le fichier correctement modifié, exécutez les commandes PowerShell ci-dessous pour créer le pipeline sur Data Factory :

```powershell
$dataFactoryName = "<data factory name>"
$resourceGroupName = "<resource group name>"
$pipelineName = "<pipeline name>"

Set-AzDataFactoryV2Pipeline `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $pipelineName `
    -DefinitionFile ".\File-System-Pipeline.json"
```