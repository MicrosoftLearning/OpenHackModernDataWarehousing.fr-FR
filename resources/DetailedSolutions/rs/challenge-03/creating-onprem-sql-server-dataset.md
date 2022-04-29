---
ms.openlocfilehash: fa20fdaff219893e3d07f591c0335be55a6150c2
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765788"
---
# <a name="creating-a-dataset-for-an-on-premises-sql-server-database"></a>Création d’un jeu de données pour une base de données SQL Server locale

En utilisant [cette référence](https://docs.microsoft.com/fr-fr/azure/data-factory/connector-sql-server#dataset-properties), créez un fichier JSON nommé `SQL-Server-Dataset.json` avec cette structure :

```json
{
    "name": "<dataset name>",
    "properties": {
        "linkedServiceName": {
            "referenceName": "<On prem SQL Server Linked Service Name>",
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

Veillez à utiliser le nom correct pour `<On prem SQL Server Linked Service Name>`.

Utilisez maintenant les commandes ci-dessous pour créer le jeu de données dans la fabrique de données :

```powershell
$dataFactoryName = "<data factory name>"
$resourceGroupName = "<resource group name>"
$datasetName = "<dataset name>"

Set-AzDataFactoryV2Dataset `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $datasetName `
    -DefinitionFile ".\SQL-Server-Dataset.json"
```