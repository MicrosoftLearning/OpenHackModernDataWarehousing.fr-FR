---
ms.openlocfilehash: 6446674f2883e3b72263dcc5149f7d5bb6b21583
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765817"
---
# <a name="challenge-01---define-a-data-lake"></a>Défi 01 - Définir un lac de données

## <a name="requirements"></a>Spécifications

Pour résoudre les défis 1 à 3, vous devez installer les outils suivants :

- [Azure CLI](https://docs.microsoft.com/fr-fr/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Module Az PowerShell](https://docs.microsoft.com/fr-fr/powershell/azure/install-az-ps?view=azps-1.7.0)

## <a name="creating-adls-gen2"></a>Création d’une instance ADLS Gen2

Utilisez [ce guide](https://docs.microsoft.com/fr-fr/azure/storage/blobs/data-lake-storage-quickstart-create-account#create-an-account-using-azure-cli) pour créer un compte de stockage avec Azure Data Lake Storage Gen2.

```bash
# Create variables to have consistency throughout the tutorial
RESOURCE_GROUP="<resource group name>"
LOCATION="eastus"

# Install the storage-preview extension
az extension add --name storage-preview

# Create the Storage Account with ADLS Gen2 enabled
az storage account create \
  --name "<storage account name>" \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION \
  --sku Standard_LRS \
  --kind StorageV2 \
  --hierarchical-namespace true
```

## <a name="learning-experiences-and-road-blocks"></a>Expériences d’apprentissage et blocages

- Créer et utiliser Azure Cloud Shell ou
- Installer l’interface de ligne de commande Microsoft Azure
- Si les utilisateurs ne lisent pas, ils ne se rendent pas compte qu’il faut installer l’extension `storage-preview` sur Azure CLI

## <a name="potential-feedbacks"></a>Commentaires potentiels

- [Ce tutoriel](https://docs.microsoft.com/fr-fr/azure/storage/blobs/data-lake-storage-quickstart-create-account#create-an-account-using-azure-cli) est intitulé *« Démarrage rapide : Créer un compte de stockage Azure Data Lake Storage Gen2 »* . Pour être plus précis sur le fait qu’ADLS Gen2 est une **fonctionnalité** de compte de stockage, nous suggérons de l’appeler :

    > Démarrage rapide : Créer un compte stockage Azure avec Data Lake Storage Gen2 activé