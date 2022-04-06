---
ms.openlocfilehash: 1df8ec6e8f41086aeee50f22d37444fb2cd66bd0
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765831"
---
# <a name="creating-a-dataset-for-file-system-sources"></a>Création d’un jeu de données pour les sources de système de fichiers

En utilisant [cette référence](https://docs.microsoft.com/en-us/azure/data-factory/connector-file-system#dataset-properties), vous créez un jeu de données pour la source de système de fichiers.

Créez un fichier JSON nommé `File-System-Dataset.json` avec la structure suivante :

```json
{
    "name": "<file system dataset name>",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "fileName": {
                "type": "String"
            }
        },
        "typeProperties": {
            "folderPath": "<folder/subfolder/ you will copy data from>",
            "fileName": "@dataset().fileName",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2020-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n",
                "firstRowAsHeader": true
            }
        }
    }
}
```

> Notez que `properties:typeProperties:folderPath` est relatif au chemin racine que vous avez configuré sur le [service lié de système de fichiers](creating-file-system-linked-service.md).
> Si vous copiez les fichiers du dossier spécifié sur le service lié, utilisez « . » comme valeur pour cette propriété.
>
> Par ailleurs, vérifiez que les fichiers sur la machine virtuelle locale ont la propriété `modifiedDate` entre `properties:typeproperties:modifiedDatetimeStart` et `properties:typeproperties:modifiedDatetimeEnd`.

Une fois que vous avez créé le fichier, utilisez la commande PowerShell suivante pour créer le jeu de données sur Azure Data Factory :

```powershell
$dataFactoryName = "<data factory name>"
$resourceGroupName = "<resource group name>"
$datasetName = "<dataset name>"

Set-AzDataFactoryV2Dataset `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $datasetName `
    -DefinitionFile ".\File-System-Dataset.json"
```