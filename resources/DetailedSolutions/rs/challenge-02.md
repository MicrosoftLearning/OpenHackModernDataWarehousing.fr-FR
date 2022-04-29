---
ms.openlocfilehash: 16fbe6839a0025975655b2c4fd319c298b494dee
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765799"
---
# <a name="challenge-02---extract-data-from-azure-sql-and-cosmos-db"></a>Défi 02 - Extraire des données Azure SQL et Cosmos DB

Ce défi vous demande de copier des données de

- Bases de données Azure SQL
    - CloudSales
    - CloudStreaming
- Collection de documents CosmosDB
    - films

Pour ce faire, nous devons provisionner un mécanisme d’extraction sur Azure. Cette solution détaillée décrit ce scénario en utilisant Azure Data Factory comme mécanisme d’extraction.

Une fois la fabrique de données provisionnée, vous devez configurer les 3 ressources de fabrique pour pouvoir copier les données d’une source vers un récepteur :

- Service lié : déclare la cible de la connexion
- Jeu de données : décrit votre structure de données
- Pipeline : configure votre flux de données

## <a name="create-an-azure-data-factory"></a>Créer une fabrique de données Azure

Azure Data Factory est un service d’intégration de données cloud qui permet de composer des services de stockage, de transit et de traitement des données dans des pipelines de données automatisés.

Vous pouvez créer une fabrique de données Azure de deux manières, par exemple :

- Portail Azure
- Azure PowerShell

Dans ce tutoriel, vous créez une fabrique de données en utilisant **PowerShell**.

Des instructions détaillées sont disponibles [ici](https://docs.microsoft.com/fr-fr/azure/data-factory/quickstart-create-data-factory-powershell)

> Si vous utilisez un autre système d’exploitation que Windows, vous pouvez tirer parti d’Azure Cloud Shell pour exécuter les commandes ci-dessous.

```powershell
# Create variables to have consistency throughout the tutorial
$resourceGroupName = "dryrunres"
$dataFactoryName = "<data factory name>"

# If you already have the Resource Group you will create the Data Factory in
$resourceGroup = Get-AzRmResourceGroup -Name $resourceGroupName

# If you will create the Resource Group as part of this tutorial
$resourceGroup = New-AzResourceGroup $resourceGroupName -location '<location>'

# Now to create the Data Factory
$DataFactory = Set-AzDataFactoryV2 `
    -ResourceGroupName $resourceGroup.ResourceGroupName `
    -Location $resourceGroup.Location -Name $dataFactoryName
```

## <a name="copying-sql-data-into-azure-data-lake-storage-gen2-using-powershell"></a>Copie de données SQL dans Azure Data Lake Storage Gen2 avec PowerShell

Une fois que Data Lake Storage Gen2 est activé sur le compte de stockage, nous pouvons commencer à le configurer pour copier les données des bases de données dans le lac de données.

Suivez les étapes ci-dessous pour le faire avec PowerShell.

Vous avez également besoin de références de configuration de service lié pour les différents types de services liés que vous créez dans ce tutoriel.
Gardez la documentation sur le [type de jeu de données](https://docs.microsoft.com/fr-fr/azure/data-factory/concepts-datasets-linked-services#dataset-type) à portée de main pour vérifier si la source ou le récepteur que vous voulez configurer est pris en charge, et pour voir des exemples.

- [01 - Création des services liés](challenge-02/creating-linked-services.md)
    - Avant de créer des jeux de données, nous avons besoin de services liés pour nous connecter aux sources de données et aux récepteurs.
- [02 - Création des jeux de données](challenge-02/creating-datasets.md)
    - Avec les jeux de données, nous définissons les représentations de données pour la copie.
- [03 - Création des pipelines](challenge-02/creating-pipelines.md)
    - Ces pipelines orchestrent les jeux de données pour déplacer les données de la source vers le récepteur.

## <a name="learning-experiences-and-road-blocks"></a>Expériences d’apprentissage et blocages

- Si le participant utilise un ordinateur MacOS et Azure CLI localement, il ne peut pas utiliser bash pour créer l’instance ADF. Il doit donc
    - Utiliser Azure Cloud Shell
    - Créer la ressource en utilisant l’interface utilisateur
- Charger les fichiers avec Azure Cloud Shell

## <a name="potential-feedbacks"></a>Commentaires potentiels

### <a name="data-factory-creation"></a>Création de la fabrique de données

- D’après ce que j’ai pu voir, il n’y a pas de prise en charge de la gestion d’ADF avec Azure CLI.
- Le menu à gauche dans [ce lien](https://docs.microsoft.com/fr-fr/azure/data-factory/#5-minute-quickstarts) présente des titres incorrects
    - Pour _« Créer une fabrique de données »_ , il ne doit y avoir que deux titres :
        - Interface utilisateur ou
        - Azure PowerShell
    - Les autres doivent être, par exemple, _« Créer un pipeline - (objet) »_
- [Ce tutoriel](https://docs.microsoft.com/fr-fr/azure/data-factory/quickstart-create-data-factory-powershell) ne vous montre pas seulement comment créer une fabrique de données Azure, mais aussi comment créer un exemple complet de **déplacement de données**. Ce serait bien d’avoir un exemple clair pour la **Création d’une fabrique de données**.
    - C’est parce que dans ce tutoriel, on crée d’abord le compte de stockage qui contient les données copiées, puis on apprend comment créer la fabrique de données. Ce peut être un peu déroutant, car les utilisateurs vont créer le compte de stockage simplement parce qu’ils suivent ce tutoriel, alors qu’ils n’en ont peut-être pas besoin.

### <a name="linked-services"></a>Services liés

- [Ce tutoriel](https://docs.microsoft.com/fr-fr/azure/data-factory/tutorial-bulk-copy) ne partage avec les utilisateurs aucune référence qui leur permette d’obtenir d’autres exemples de différents types de services liés, par ex., ADLS au lieu de SQL DW.
- La session Azure Cloud Shell expire trop rapidement quand elle est exécutée sous un autre onglet. Cela a un impact si l’utilisateur a défini des variables pour assurer la cohérence des noms dans l’ensemble du tutoriel. Ces variables perdent leur valeur si la session est supprimée.

### <a name="datasets"></a>Groupes de données

- Je n’ai pas trouvé le moyen d’obtenir des chaînes de connexion CosmosDB à partir d’une commande PowerShell avec le module AzureRm.
- Dans le module Az PowerShell, pour créer des services liés, nous référençons le fichier avec le paramètre `-File`. Pour créer des jeux de données, le paramètre qui a le même objet s’appelle `-DefinitionFile`. On pourrait ici avoir un paramètre standard.
- Découverte du fonctionnement des fichiers Parquet
- La documentation du jeu de données est trop faible en termes de techniques à utiliser pour rendre la solution plus robuste, c’est-à-dire l’utilisation de paramètres.

### <a name="pipelines"></a>Pipelines

- Pendant la création de pipelines comme source pour CosmosDB, si vous copiez des données *en l’état*, le paramètre `properties:activities:<Copy Activity>:typeProperties:source:nestingSeparator`
**doit** être vide. Dans le cas contraire, il attribue automatiquement « . » comme valeur et le pipeline ne fonctionne pas.