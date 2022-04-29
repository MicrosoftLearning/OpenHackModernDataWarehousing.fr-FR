---
ms.openlocfilehash: 26b81fc0c3e9f686f8c6dd9d99021ea25fc02cf8
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765786"
---
# <a name="creating-the-on-premises-sql-server-linked-service-to-use-the-self-hosted-integration-runtime"></a>Création du service lié SQL Server local pour utiliser le runtime d’intégration auto-hébergé

Tout comme pour les services liés précédents que vous avez créés, en utilisant [cette documentation](https://docs.microsoft.com/fr-fr/azure/data-factory/connector-sql-server#linked-service-properties), créez un fichier appelé `SQL-Server-LinkedService.json` avec la structure suivante :

```json
{
    "name": "<filesystem linked service name>",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> Notez la double barre oblique inverse sur la valeur `connectionString`, car il s’agit d’un fichier JSON.
>
> Notez également que `<servername>` peut être défini comme `localhost`, car l’agent du runtime d’intégration auto-hébergé s’exécute sur la machine elle-même.

Une fois le fichier créé, exécutez la commande PowerShell ci-dessous pour créer le service lié sur Data Factory :

```powershell
$resourceGroupName = "<resource group name>"
$dataFactoryName = "<data factory name>"
$linkedServiceName = "<sql server linked service name>"

Set-AzDataFactoryV2LinkedService `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $linkedServiceName `
    -File "./SQL-Server-LinkedService.json"
```