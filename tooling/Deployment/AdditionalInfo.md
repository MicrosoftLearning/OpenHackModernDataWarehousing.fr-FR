---
ms.openlocfilehash: 3f5c5c595657f8fe6fa05505dcfb1cae917521c0
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140766007"
---
# <a name="important"></a>Important #

Ne placez pas ces informations dans le dépôt public pour BYOS

# <a name="openhack---modern-data-warehousing"></a>OpenHack - Entreposage de données moderne #

## <a name="openhack-guides"></a>Guides OpenHack ##  

Rappel : les guides sont dans le dépôt privé principal.  Ils sont créés via des actions GitHub et déployés sur le compte de stockage qui est utilisé pendant le déploiement par les systèmes Opsgility.  Quand le déploiement est déclenché, seule la définition et la stratégie lab viennent de ce dépôt.  

La définition de contenu et les guides (ci-dessus) sont utilisés pendant le déploiement, mais ils doivent uniquement être modifiés à partir du dépôt principal (celui-ci).  Pendant la création du dépôt, l’action GitHub publie la dernière version des guides et de la définition du contenu lab dans le compte de stockage.  

Les artefacts de déploiement sont tous stockés dans l’abonnement BYOS, pour que les utilisateurs puissent le faire également.  Bien qu’ils existent dans un compte de stockage, la copie principale est dans le BYOS, en cas de perte du compte de stockage.  

La seule exception actuellement concerne les deux fichiers BACPAC.

Utilisez les liens suivants pour rechercher les informations importantes :  

*  Les guides sont disponibles ici : [dépôt MDW OpenHack](https://github.com/Microsoft-OpenHack/modern-data-warehousing/tree/main/portal)  

*  La définition de contenu se trouve ici : [Définition de contenu MDW](https://github.com/Microsoft-OpenHack/modern-data-warehousing/blob/main/portal/en/lab-content-definition.json)  

*  Les artefacts sont ici : [Artefacts de déploiement](https://github.com/microsoft/OpenHack/tree/main/byos/modern-data-warehousing)  

*  Les artefacts et les scripts de déploiement sont publiés dans le compte de stockage : OpsgilityProd - openhackguides - mdw-templates-tmp  

## <a name="artifacts"></a>Artifacts ##  

Si l’un des éléments suivants doit être modifié, faites-le UNIQUEMENT dans le dépôt BYOS, vous devez ensuite copier manuellement les changements effectués sur les fichiers en remplaçant ce qui se trouve dans le compte de stockage [Emplacement du compte de stockage des artefacts de déploiement indiqué ci-dessus], ou déclencher une build à partir du dépôt PRINCIPAL Microsoft OpenHack pour les guides ou à partir du dépôt BYOS pour les artefacts de déploiement, qui publie ces éléments dans le compte de stockage approprié
            
## <a name="arm-templates"></a>Modèles ARM ##  

Les modèles suivants sont utilisés pendant le déploiement, hébergés dans le BYOS et également dans le stockage Opsgility. 

### <a name="microsoft-byos-repo"></a>Dépôt Microsoft BYOS ###  

* [DeployCosmosDB](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/ARM/DeployCosmosDB.json)  
* [DeployFileVM](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/ARM/DeployFileVM.json)  
* [DeployMDWOpenHackLab](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/ARM/DeployMDWOpenHackLab.json)  
* [Paramètres DeployMDWOpenHackLab](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/ARM/DeployMDWOpanHackLab.parameters.json)    
* [DeploySQLAzure](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/ARM/DeploySQLAzure.json)
* [DeploySQLVM](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/ARM/DeploySQLVM.json)  

## <a name="scripts"></a>scripts ; ## 

###  <a name="microsoft-byos-repo"></a>Dépôt Microsoft BYOS ###  

* [Créer et remplir Cosmos](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/Scripts/CreateAndPopulateCosmos.ps1)
* [DeploySQLVM](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/Scripts/DeploySQLVM.ps1)
* [Désactiver IEESC](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/Scripts/DisableIEESC.ps1)
* [Pilote d’extension VM de fichier](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/Scripts/FileVMExtensionDriver.ps1)
* [Récupérer le fichier CSV](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/Scripts/RetrieveCSV.ps1)
* [Pilote d’extension VM SQL](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/Scripts/SqlVMExtensionDriver.ps1)  

## <a name="csv-files"></a>Fichiers CSV ##  

### <a name="microsoft-byos-repo"></a>Dépôt Microsoft BYOS ###  

* [Locations](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/CSVFiles/Rentals.zip)  
* [Transactions](https://github.com/microsoft/OpenHack/blob/main/byos/modern-data-warehousing/deploy/CSVFiles/Transactions_2018.zip)  

## <a name="deployment-resource-files-sas-token-required"></a>Fichiers de ressources de déploiement [jeton SAS obligatoire] ##  

### <a name="opsgility-storage"></a>Stockage Opsgility ###  

* [BACPAC CloudSales](https://openhackartifacts.blob.core.windows.net/mdw/CloudSales.bacpac)  
* [BACPAC CloudStreaming](https://openhackartifacts.blob.core.windows.net/mdw/CloudStreaming.bacpac)
* [JSON movies_southridge](https://openhackartifacts.blob.core.windows.net/mdw/movies_southridge.json)  
* [Fichier de sauvegarde OnPremRentals](https://openhackartifacts.blob.core.windows.net/mdw/onpremrentals.bak)  
* Pour les fichiers CSV, regardez dans le dossier du conteneur `Rentals` et le dossier du conteneur `onpremrentalscsv` dans le compte de stockage.  Ils se trouvent également dans le dépôt BYOS dans les fichiers zip.  