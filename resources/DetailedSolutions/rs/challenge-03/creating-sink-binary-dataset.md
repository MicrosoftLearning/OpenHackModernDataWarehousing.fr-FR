---
ms.openlocfilehash: 9cb89d0557368dec223f0c1a15c64c4e2cf3a3df
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765762"
---
# <a name="creating-a-binary-dataset-for-sink"></a>Création d’un jeu de données binaire pour le récepteur

Dans certains cas, vous voudrez peut-être copier des fichiers à partir de la source *telle quelle*. Par exemple, vous avez un ordinateur local avec des fichiers CSV et vous n’avez pas ce qu’il faut pour générer des fichiers [parquet](https://docs.microsoft.com/en-us/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format). Vous pouvez copier ces fichiers CSV *tels quels* pour les traiter plus tard.

Avec [cette référence](https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-storage#dataset-properties), vous allez créer un jeu de données pour décrire le format de données pour Azure Data Lake Storage Gen2 en tant que récepteur.

Créez un fichier JSON appelé `ADLS-Dataset-Binary.json` avec la structure suivante :

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
            "fileName": {
                "value": "@dataset().fileName",
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

Veillez à utiliser le nom correct pour `<ADLS Linked Service name>`.
Veillez également à nommer correctement votre jeu de données, par exemple `FourthCoffee_ADLS_Binary`.

Utilisez maintenant les commandes ci-dessous pour créer le jeu de données dans la fabrique de données :

```powershell
$dataFactoryName = "<data factory name>"
$resourceGroupName = "<resource group name>"
$datasetName = "<dataset name>"

Set-AzDataFactoryV2Dataset `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $datasetName `
    -DefinitionFile ".\ADLS-Dataset-Binary.json"