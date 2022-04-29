---
ms.openlocfilehash: 50106c82620f6ba4b6fcada7e608047981f83620
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765821"
---
# <a name="modern-data-warehousing-openhack-lab-deployment"></a>Déploiement du lab OpenHack Modern Data Warehousing

## <a name="overview"></a>Vue d’ensemble

Le déploiement de l’environnement du lab OpenHack Modern Data Warehousing comprend les éléments suivants

### <a name="southridge-video-resources"></a>Ressources vidéo Southridge

- Deux bases de données Azure SQL dans un même serveur logique
- Un compte Cosmos DB avec une seule collection pour le catalogue de films

### <a name="fourth-coffee-resources"></a>Ressources Fourth Coffee

- Une machine virtuelle avec un répertoire de données CSV

### <a name="vanarsdel-ltd-resources"></a>Ressources VanArsdel, Ltd.

- Une machine virtuelle avec une base de données SQL

## <a name="quickstart"></a>Démarrage rapide

1. Attribuez les variables `$sqlpwd` et `$vmpwd` dans votre session PowerShell sous forme de **chaînes sécurisées**. Veillez à utiliser un mot de passe fort pour les deux. Suivez[ce lien](https://docs.microsoft.com/fr-fr/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm) pour voir les exigences de mot de passe de la machine virtuelle et [ce lien](https://docs.microsoft.com/fr-fr/sql/relational-databases/security/password-policy?view=sql-server-2017#password-complexity) pour SQL Server.

    ```powershell
    $sqlpwd = "0p3nH4ckD4t4!" | ConvertTo-SecureString -AsPlainText -Force
    $vmpwd = "0p3nH4ckD4t4!" | ConvertTo-SecureString -AsPlainText -Force
    ```

1. Attribuez également la variable `$containerSAS` dans votre session PowerShell sous forme de **chaîne sécurisée**. Il s’agit d’une SAS rwl limitée à un conteneur contenant des sauvegardes de base de données. La SAS vous est fournie en dehors de ces instructions (dans de nombreux cas, dans un fichier appelé lab_backup_sas.txt) :

    ```powershell
    $containerSAS = "TheContainerSASYouHave" | ConvertTo-SecureString -AsPlainText -Force
    ```

1. Vérifiez que vous avez installé le [module Az](https://docs.microsoft.com/fr-fr/powershell/azure/new-azureps-module-az?view=azps-1.8.0).

1. Exportez la liste des abonnements de l’équipe OpenHack et les informations d’identification associées de la [classe Opsgility](https://aka.ms/ohadmin). Pour ce faire, effectuez la procédure suivante :  
    - Visiter la Vue lab correspondant à votre classe  
    - Vérifiez que toutes les équipes sont `In Use`. Sinon, exécutez `Start` pour les lancer
    - Une fois qu’elles sont toutes `In Use`, cliquez sur `List credentials`
    - Faites défiler vers le bas la fenêtre contextuelle des informations d’identification et cliquez sur `Export to CSV`

1. Attribuez la variable `$credentialFile` dans votre session PowerShell. Il s’agit du chemin du fichier d’informations d’identification obtenu à l’étape précédente :

    ```powershell
    $credentialFile = "C:\Users\alias\oh-credentials.csv"
    ```

1. Exécutez la commande suivante pour déployer les environnements (ce processus peut prendre 10-15 minutes) :

    ```powershell
    .\ExecuteARMDeploymentsForTeamSubscriptions.ps1 `
        -CredentialFile $credentialFile `
        -DeploymentTemplateFile ".\DeployMDWOpenHackLab.json" `
        -DeploymentParameterFile ".\DeployMDWOpenHackLab.parameters.json" `
        -ResourceGroupName mdw-oh-lab `
        -Location eastus `
        -SqlAdminLoginPassword $sqlpwd `
        -VMAdminPassword $vmpwd `
        -BackupStorageContainerSAS $containerSAS

1. A background job will now be running to deploy the MDW OpenHack lab resources into each team subscription.
To check the status of these jobs, use PowerShell job cmdlets:

    ```powershell
    Get-Job

    Receive-Job -Id 1
    ```