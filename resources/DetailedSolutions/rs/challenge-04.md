---
ms.openlocfilehash: ccf2ccbddf8a648a61ba072620667af464764aba
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765759"
---
# <a name="challenge-04---transforming-all-the-data"></a>Défi 04 - Transformation de toutes les données

L’objectif de ce défi est de fusionner toutes les données communes et de modifier certains types de données, tout en suivant une bonne pratique des branches de contrôle de code source.

Pour réussir ce défi, cette solution détaillée décrit l’option Databricks.

## <a name="protecting-the-master-branch-with-pull-request-policies"></a>Protection de la branche maîtresse avec des stratégies de demande de tirage

L’objectif est d’empêcher les validations et les envois push d’être effectués directement sur le serveur maître.
Cela empêche par la suite le déploiement automatique du code non approuvé dans les environnements. Pour le moment, l’idée est de transformer la branche `master` en codebase pour du code stable.

Pour le faire, un très bon guide est disponible dans Microsoft Docs [ici](https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops).
Plus précisément, [cette étape](https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#require-a-minimum-number-of-reviewers) vous permet d’avoir au moins un réviseur si quelqu’un veut envoyer du code au serveur maître.

## <a name="creating-the-databricks-workspace"></a>Création d’un espace de travail Databricks

En utilisant [cette référence](https://azure.microsoft.com/en-us/resources/templates/101-databricks-workspace/), vous créez un espace de travail Databricks avec les propriétés suivantes :

- `workspaceName` : nom de votre espace de travail
- `pricingTier` : Standard, pour l’étendue de cette solution

### <a name="azure-cli"></a>Azure CLI

```cmd
RESOURCE_GROUP_NAME="<Name of the resource group you already have>"
WORKSPACE_NAME="<The name for your Databricks workspace>"
PRICING_TIER="standard"
TEMPLATE_URI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-databricks-workspace/azuredeploy.json"

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri $TEMPLATE_URI
    --parameters \
    workspaceName=$WORKSPACE_NAME \
    pricingTier=$PRICING_TIER
```

### <a name="powershell"></a>PowerShell

> **Avertissement** : dans le cadre de ce guide, nous vous recommandons vivement d’utiliser Azure CLI. Le module Az PowerShell ne vous permet pas de récupérer le mot de passe du principal de service en texte brut, dont vous avez besoin plus tard dans ce scénario

```powershell
$resourceGroupName = "<Name of the resource group you already have>"
$workspaceName = "<The name for your Databricks workspace>"
$pricingTier = "standard"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-databricks-workspace/azuredeploy.json"

New-AzResourceGroupDeployment `
    -TemplateUri $templateUri
    -ResourceGroupName $resourceGroupName `
    -workspaceName $workspaceName `
    -pricingTier $pricingTier
```

## <a name="create-a-service-principal"></a>Créer un principal du service

Vous utilisez un principal de service pour permettre à Databricks d’accéder au Data Lake Storage sur le compte de stockage Azure. Pour ce faire, procédez comme suit :

Références :

- [Tutoriel : Accéder aux données Data Lake Storage Gen2 avec Azure Databricks à l’aide de Spark](https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-use-databricks-spark)
- [Gérer l’accès aux ressources Azure à l’aide du contrôle RBAC et d’Azure PowerShell](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-powershell)
- [Octroyer l’accès aux données d’objet blob et de file d’attente Azure avec RBAC dans le Portail Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal)
- [Accorder l’accès aux données blob et de file d’attente Azure avec RBAC en utilisant **PowerShell**](https://docs.microsoft.com/en-us/azure/storage/common/storage-auth-aad-rbac-powershell#list-available-rbac-roles)

Vous pouvez le faire avec une seule commande :

- Création d’un principal de service
- Attribution de `Storage Blob Data Contributor` à ce principal de service sur le compte de stockage

### <a name="powershell"></a>PowerShell

```powershell
$spDisplayName = "<The display name for the Service Principal>"
$scope = "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"

New-AzADServicePrincipal `
    -DisplayName riserradsp `
    -Role "Storage Blob Data Contributor" `
    -Scope $scope
```

Veillez à renseigner les espaces réservés suivants :

- `<subscription id>`
- `<resource group name>`
- `<storage account name>`

Vous obtenez une sortie de ce type :

```powershell
Secret                : System.Security.SecureString
ServicePrincipalNames : {<ApplicationId GUID>, http://<Service Principal Display Name>}
ApplicationId         : <ApplicationId GUID>
ObjectType            : ServicePrincipal
DisplayName           : <Service Principal Display Name>
Id                    : <Service Principal Object Id>
Type                  :
```

### <a name="azure-cli"></a>Azure CLI

```bash
$SP_DISPLAYNAME="<The display name for the Service Principal>"
$SCOPE="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"

az ad sp create-for-rbac \
    -n $SP_DISPLAYNAME \
    --role "Storage Blob Data Contributor" \
    --scope $SCOPE
```

Et la sortie devrait être :

```bash
{
  "appId": "<ApplicationID GUID>",
  "displayName": "<Service Principal Display Name>",
  "name": "http://<Service Principal Display Name>",
  "password": "<Service Principal Password/Secret GUID>",
  "tenant": "<Tenant ID>"
}
```

## <a name="install-the-databricks-cli"></a>Installer l’interface CLI Databricks

En utilisant [cette référence](https://github.com/databricks/databricks-cli#installation), vous installez l’interface CLI Databricks avec `pip`. L’exigence ici est :

- Python 2.7.9+ ou 3.6

```bash
pip install --upgrade databricks-cli
```

Si vous recevez un message de ce type :

```bash
Cannot uninstall 'six'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
```

Vous pouvez essayer d’utiliser `--ignore-installed`. Tenez compte des références suivantes :

**TODO** : affiner les liens ci-dessous pour obtenir une meilleure explication.

- [pip 10 et apt : comment éviter les erreurs « Impossible de désinstaller X » pour les packages distutils](https://stackoverflow.com/questions/49932759/pip-10-and-apt-how-to-avoid-cannot-uninstall-x-errors-for-distutils-packages)
- [pip ne peut pas désinstaller <package>: « Le projet installé est un projet distutils »](https://stackoverflow.com/questions/53807511/pip-cannot-uninstall-package-it-is-a-distutils-installed-project/53807588#53807588)
- [Mise à niveau vers pip 10 : le projet installé est un projet distutils, par conséquent, nous ne pouvons pas déterminer exactement les fichiers qui lui appartiennent, ce qui risque d’entraîner une désinstallation partielle.](https://github.com/pypa/pip/issues/5247#issuecomment-443398741)

Une fois que vous avez installé databricks-cli, vous devez générer un jeton d’accès à partir de votre espace de travail Databricks pour configurer l’interface CLI. Pour le faire, suivez [ce lien](https://docs.databricks.com/api/latest/authentication.html).

Avec le jeton à portée de main, utilisez la commande ci-dessous pour configurer votre interface CLI afin de vous connecter à votre espace de travail Databricks :

```bash
databricks configure --token
```

Vous êtes invité à entrer deux informations :

- `Databricks Host` : par exemple, si vous avez créé votre espace de travail Databricks sur `East US`, l’URL peut être `https://eastus.azuredatabricks.net`. Pour vérifier, ouvrez votre espace de travail Databricks et copiez l’URL racine
- `Token` : jeton que vous avez généré avec l’interface utilisateur Databricks à l’étape précédente

Pour vérifier que votre interface CLI est correctement configurée, utilisez la commande suivante :

```bash
databricks workspace ls
```

La sortie suivante doit s’afficher :

```bash
Shared
Users
```

## <a name="create-the-databricks-cluster"></a>Créer le cluster Databricks

Vous créez un cluster Databricks avec les étapes suivantes.

### <a name="create-the-json-metadata"></a>Créer les métadonnées JSON

Pour utiliser l’interface CLI Databricks afin de créer un cluster, vous avez besoin de ses métadonnées dans un fichier JSON. Créez un fichier JSON nommé `cluster.json` avec la structure suivante :

```json
{
    "num_workers": null,
    "autoscale": {
        "min_workers": 2,
        "max_workers": 8
    },
    "cluster_name": "<cluster name>",
    "spark_version": "5.3.x-scala2.11",
    "spark_conf": {},
    "node_type_id": "Standard_DS3_v2",
    "ssh_public_keys": [],
    "custom_tags": {},
    "spark_env_vars": {
        "PYSPARK_PYTHON": "/databricks/python3/bin/python3"
    },
    "autotermination_minutes": 120,
    "init_scripts": []
}
```

Notez que vous avez des valeurs fixes pour `cluster_name`, `spark_version` et `node_type_id`. Vérifiez que ces valeurs sont toujours valides quand vous exécutez cette solution.

Une fois le fichier JSON créé, utilisez la commande ci-dessous pour créer le cluster :

```bash
databricks clusters create --json-file cluster.json
```

Vous recevez le `cluster_id` immédiatement :

```bash
{
    "cluster_id": "<cluster id>"
}
```

Pour vérifier l’état de ce cluster, utilisez :

```bash
databricks clusters list
```

Vous pouvez utiliser votre cluster quand il apparaît dans l’état `RUNNING` :

```bash
<cluster id>  <cluster name>  RUNNING
```

## <a name="create-databricks-notebooks-to-start-transforming"></a>Créer des notebooks Databricks pour démarrer la transformation

Une fois que votre cluster est créé sur Databricks, vous pouvez créer vos notebooks pour commencer à transformer les données qui ont été ingérées.

Il existe de nombreuses approches à ce stade. Dans le cadre de cette solution détaillée, la structure suivante est proposée :

- Un notebook par objet ci-dessous
    - Clients
    - Adresses
    - Les films et les acteurs restent ensemble
- Chaque notebook fait les opérations suivantes :
    - Charger les données d’objet à partir des fichiers sur le lac de données
    - Les regrouper à partir des différents systèmes sources
        - Unifier les noms de colonne, les types de données
    - Enregistrer les données unifiées au format Parquet dans le lac de données

Certaines cellules sont communes à tous les notebooks que vous créez pour ce défi. Par conséquent, la solution détaillée pour chaque notebook est décomposée par étape dans chaque notebook.

### <a name="notebooks-structure"></a>Structure des notebooks

Le notebook de transformation a la structure suivante :

- Définir des fonctions utiles
- Charger les données des 3 systèmes sources
- Enregistrer les données concaténées dans un fichier Parquet

Utilisez les étapes détaillées ci-dessous pour créer chaque notebook.

### <a name="notebooks-steps-details"></a>Détails des étapes de notebook

#### <a name="customers-notebook"></a>Notebook des clients

Créez un notebook sur Databricks et appelez-le `Customers`. Une fois que vous l’avez créé, [ajoutez-le au contrôle de code source](challenge-04/00-add-notebook-source-control.md), puis suivez les étapes ci-dessous, une par cellule :

- [1 - Définir des fonctions utiles](challenge-04/01-define-useful-functions.md)
- 2 - Chargement des données brutes
    - [2.1 - Chargement des clients Southridge](challenge-04/02-loading-raw-data/01-customers/01-loading-southridge-customers.md)
    - [2.2 - Chargement des clients VanArsdel](challenge-04/02-loading-raw-data/01-customers/02-load-vanarsdel-customers.md)
    - [2.3 - Chargement des clients FourthCoffee](challenge-04/02-loading-raw-data/01-customers/03-loading-fourthcoffee-customers.md)
- 3 - Les regrouper
    - [3.1 - Regrouper tous les clients](challenge-04/03-bring-all-together/01-bring-customers-together.md)
- [4 - Enregistrement des données au format Parquet dans le lac de données](challenge-04/04-saving-data-as-parquet.md)

#### <a name="addresses-notebook"></a>Notebook des adresses

Créez un notebook sur Databricks et appelez-le `Addresses`. Une fois que vous l’avez créé, [ajoutez-le au contrôle de code source](challenge-04/00-add-notebook-source-control.md), puis suivez les étapes ci-dessous, une par cellule :

- [1 - Définir des fonctions utiles](challenge-04/01-define-useful-functions.md)
- 2 - Chargement des données brutes
    - [2.1 - Chargement des adresses Southridge](challenge-04/02-loading-raw-data/02-addresses/01-loading-southridge-addresses.md)
    - [2.2 - Chargement des adresses VanArsdel](challenge-04/02-loading-raw-data/02-addresses/02-load-vanarsdel-addresses.md)
    - [2.3 - Chargement des adresses FourthCoffee](challenge-04/02-loading-raw-data/02-addresses/03-loading-fourthcoffee-addresses.md)
- 3 - Les regrouper
    - [3.1 - Regrouper toutes les adresses](challenge-04/03-bring-all-together/02-bring-addresses-together.md)
- [4 - Enregistrement des données au format Parquet dans le lac de données](challenge-04/04-saving-data-as-parquet.md)

#### <a name="movies-and-actors-notebook"></a>Notebook des films et des acteurs

Créez un notebook sur Databricks et appelez-le `MoviesAndActors`. Une fois que vous l’avez créé, [ajoutez-le au contrôle de code source](challenge-04/00-add-notebook-source-control.md), puis suivez les étapes ci-dessous, une par cellule :

- [1 - Définir des fonctions utiles](challenge-04/01-define-useful-functions.md)
- 2 - Chargement des données brutes
    - [2.1 - Chargement des films et des acteurs Southridge](challenge-04/02-loading-raw-data/03-movies-actors/01-loading-southridge-moviesandactors.md)
    - [2.2 - Chargement des films et des acteurs VanArsdel](challenge-04/02-loading-raw-data/03-movies-actors/02-loading-vanarsdel-moviesandactors.md)
    - [2.3 - Chargement des films et des acteurs FourthCoffee](challenge-04/02-loading-raw-data/03-movies-actors/03-loading-fourthcoffee-moviesandactors.md)
- 3 - Les regrouper
    - [3.1 - Regrouper tous les films et les acteurs](challenge-04/03-bring-all-together/03-bring-movies-actors-together.md)
- [4 - Enregistrement des données au format Parquet dans le lac de données](challenge-04/04-saving-data-as-parquet.md)

## <a name="learning-experiences-and-road-blocks"></a>Expériences d’apprentissage et blocages

Cette référence est incroyable et nous pouvons probablement ajouter :

- [Présentation des DataFrames - Python](https://docs.databricks.com/spark/latest/dataframes-datasets/introduction-to-dataframes-python.html)

- Quels sont les critères pour supprimer les doublons entre les clients CloudSales et CloudStreaming ?
- Il y a des clients communs entre Southridge, FourthCoffee et VanArsdel.
Quels sont les critères pour les supprimer ?
- Il faut prendre une décision sur la marche à suivre pour la `ReleaseDate` des films de Southridge

## <a name="potential-feedbacks"></a>Commentaires potentiels

- Apparemment, nous ne pouvons pas voir le mot de passe du principal de service qui est généré avec PowerShell, ce qui n’est pas le cas avec l’interface CLI.
Pour notre cas, nous devons voir et copier le mot de passe à utiliser sur nos notebooks, etc. Ce serait bien que PowerShell propose une option pour récupérer le mot de passe
- Pour mettre à niveau un niveau tarifaire de l’espace de travail Databricks, nous devons le *recréer* avec les mêmes valeurs de paramètre que celles du niveau existant, à l’exception du niveau.
[Voici le document qui décrit cela](https://docs.azuredatabricks.net/administration-guide/account-settings/upgrade-downgrade.html)
- Nous pouvons également ajouter [ce tutoriel/cette référence](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/adls-passthrough.html#enable-passthrough-for-a-cluster) au défi 4.
    - En revanche, j’ai essayé de suivre cette procédure pas à pas, mais l’option permettant d’activer l’authentification directe avec les informations d’identification n’était pas disponible pour moi.
    [Capture d’écran ici](challenge-04/images/credential-passthrough-unavailable.jpg).
