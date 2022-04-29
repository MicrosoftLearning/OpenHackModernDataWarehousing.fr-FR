---
ms.openlocfilehash: 979d99a39ac1d17f41ac86baf14e386510ffacaf
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765841"
---
# <a name="creating-the-linked-services-for-azure-data-factory"></a>Création des services liés pour Azure Data Factory

## <a name="01---configure-linked-services-for-azure-sql-databases"></a>01 - Configurer des services liés pour les bases de données Azure SQL

Avec [cette référence](https://docs.microsoft.com/fr-fr/azure/data-factory/connector-azure-sql-database#linked-service-properties), vous allez créer un service lié Data Factory pour avoir Azure SQL comme source de données.

Créez un fichier JSON nommé `CloudSales-LinkedService.json` avec la structure suivante :

```json
{
    "name": "<Name for the Linked Service>",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
}
```

Attention à la valeur que vous devez remplacer :

- `<Name for the Linked Service>`
- SQL Server `<servername>`
- SQL `<databasename>`
- SQL Server `<username>`
- SQL SQL Server `<password>`

Exécutez les commandes ci-dessous en utilisant PowerShell pour créer ce service lié :

> Si vous exécutez ces commandes sur Azure Cloud Shell, assurez-vous de charger le fichier JSON avant de continuer et de prendre note de l’emplacement du fichier.

```powershell
# Creating the values to avoid typing errors
$resourceGroupName = "<resource group name>"
$dataFactoryName = "<data factory name>"
$linkedServiceName = "<cloud sales linked service name>"

Set-AzDataFactoryV2LinkedService `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $linkedServiceName `
    -File "./CloudSales-LinkedService.json"
```

Pour la base de données Cloud Streaming, vous pouvez dupliquer le fichier JSON que vous avez créé, juste en changeant les propriétés pour pointer vers la bonne base de données et en créant un autre nom de service lié. Donc, veillez à mettre à jour les propriétés suivantes :

- Nom du fichier (par exemple, `CloudStreaming-LinkedService.json`)
- `<Name for the Linked Service>`
- `<databasename>`

> Les autres propriétés n’ont pas besoin d’être mises à jour à tout prix, car les deux bases de données se trouvent sur un même serveur. Dans la vraie vie, vous aurez plus probablement différents serveurs et différentes informations d’identification.

La commande de création du service lié Cloud Streaming doit être alors :

```powershell
# Reassigning the linkedServiceName variable
$linkedServiceName = "<cloud streaming linked service name>"

Set-AzDataFactoryV2LinkedService `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $linkedServiceName `
    -File "./CloudStreaming-LinkedService.json"
```

## <a name="02---configure-a-linked-service-for-the-cosmos-db-as-a-source"></a>02 - Configurer un service lié pour Cosmos DB en tant que source

Avec [cette référence](https://docs.microsoft.com/fr-fr/azure/data-factory/connector-azure-cosmos-db#linked-service-properties), vous allez créer un service lié pour avoir Cosmos DB comme source de données.

Tout d’abord, vous devez obtenir la `connectionString` complète CosmosDB auprès d’Azure.
Pour cela, utilisez la commande Azure CLI suivante :

```powershell
$resourceGroupName = "<resource group name>"
$cosmosDbAccountName = "<the CosmosDB account name>"

az cosmosdb list-connection-strings \
    -n $cosmosDbAccountName
    -g $resourceGroupName
```

> Cette action n’est pas prise en charge pour l’instant sur le module Az.

Vous devez avoir une sortie similaire à ce qui suit :

```json
{
  "connectionStrings": [
    {
      "connectionString": "AccountEndpoint=https://<cosmosdb name>.documents.azure.com:443/;AccountKey=<account key1>",
      "description": "Primary SQL Connection String"
    },
    {
      "connectionString": "AccountEndpoint=https://<cosmosdb name>.documents.azure.com:443/;AccountKey=<account key2>",
      "description": "Secondary SQL Connection String"
    },
    {
      "connectionString": "AccountEndpoint=https://<cosmosdb name>.documents.azure.com:443/;AccountKey=<read-only account key1>",
      "description": "Primary Read-Only SQL Connection String"
    },
    {
      "connectionString": "AccountEndpoint=https://<cosmosdb name>.documents.azure.com:443/;AccountKey=<read-only account key2>",
      "description": "Secondary Read-Only SQL Connection String"
    }
  ]
}
```

> Étant donné que CosmosDB va être utilisé comme source de données, il est recommandé d’utiliser l’une des chaînes de connexion `Read-Only SQL Connection String`.

Comme vous le constaterez, aucune des chaînes de connexion ne spécifie la `Database` à la fin de la chaîne, donc veillez à la conserver et spécifiez également la base de données à partir de laquelle vous souhaitez extraire les données.

> Vous pouvez obtenir la liste des *noms de base de données* auprès de l’Explorateur de données Cosmos DB dans le portail Azure. Pour l’obtenir avec Azure CLI, utilisez la commande suivante :

```cmd
az cosmosdb database list \
-n <Azure Cosmos DB account name> \
-g `<resource group name>`
```

> Dans le cadre de cet exemple, vous n’avez qu’une seule base de données.
> Si vous en avez plusieurs, sélectionnez juste l’une d’entre elles et utilisez la valeur du champ `id` comme nom de votre base de données.

Maintenant que vous avez toutes les données de la source de données CosmosDB, créez un fichier JSON nommé `Movies-LinkedService.json` avec la structure suivante :

```json
{
    "name": "<Name for the Movies Linked Service>",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        }
    }
}
```

À présent, utilisez la commande ci-dessous pour créer le service lié CosmosDB :

```powershell
# Reassigning the linkedServiceName variable
$linkedServiceName = "<Name for the Movies Linked Service>"

Set-AzDataFactoryV2LinkedService `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $linkedServiceName `
    -File "./Movies-LinkedService.json"
```

## <a name="03---configure-a-linked-service-for-the-data-lake-as-a-sink"></a>03 - Configurer un service lié pour le lac de données comme récepteur

Avec [cette référence](https://docs.microsoft.com/fr-fr/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties), vous allez créer un service lié pour avoir le lac de données que vous avez créé comme récepteur pour le pipeline.

Tout d’abord, vous avez besoin de la clé du compte Stockage pour permettre à Data Factory de lire et d’écrire dans ADLS Gen2. Pour ce faire, obtenez la `accountKey` pour ADLS Gen2 à l’aide de la commande suivante :

```powershell
Get-AzStorageAccountAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name "<storage account name>"
```

Vous constaterez un résultat comme ci-dessous :

```powershell
KeyName Value               Permissions
------- -----               -----------
key1    <key1 value>        Full
key2    <key2 value>        Full
```

Utilisez `<key1 value>` ou `<key2 value>` sur votre fichier JSON comme `<accountkey>`.

Maintenant que vous avez la `accountKey`, créez un fichier JSON nommé `ADLS-LinkedService.json` avec la structure suivante :

```json
{
    "name": "<Name for the Data Lake Linked Service>",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net",
            "accountkey": {
                "type": "SecureString",
                "value": "<accountkey>"
            }
        }
    }
}
```

À présent, utilisez la commande ci-dessous pour créer le service lié Data Lake Gen2 :

```powershell
# Reassigning the linkedServiceName variable
$linkedServiceName = "<Name for the Data Lake Linked Service>"

Set-AzDataFactoryV2LinkedService `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $linkedServiceName `
    -File "./ADLS-LinkedService.json"
```