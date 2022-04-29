---
ms.openlocfilehash: b9514d06c7093e6cd7b81cd13e3dfea7526028aa
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765812"
---
# <a name="creating-the-file-system-linked-service-to-use-the-self-hosted-integration-runtime"></a>Création du service lié de système de fichiers pour utiliser le runtime d’intégration auto-hébergé

Tout comme pour les services liés précédents que vous avez créés, en utilisant [cette documentation](https://docs.microsoft.com/fr-fr/azure/data-factory/connector-file-system#linked-service-properties), créez un fichier appelé `FileSystem-LinkedService.json` avec la structure suivante :

```json
{
    "name": "<filesystem linked service name>",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Notez que `<host>` ici **n’est pas** le nom d’hôte de la machine virtuelle. Vous devez spécifier le dossier racine que vous voulez servir et partager à travers ce service lié, par ex., `C:\\Rentals`.

Veillez à remplacer également `<domain>\\<user>`, `<password>` et `<name of Integration Runtime>` par les valeurs appropriées.

> Notez également la double barre oblique inverse, car il s’agit d’un fichier JSON.
>
> Évitez d’exposer l’intégralité du lecteur C: comme service lié, pour empêcher les violations de sécurité. Créez plutôt des services liés différents pour chacun de vos sous-dossiers de lecteur sur la machine locale.

Une fois le fichier créé, exécutez la commande PowerShell ci-dessous pour créer le service lié sur Data Factory :

```powershell
# Creating the values to avoid typing errors
$resourceGroupName = "<resource group name>"
$dataFactoryName = "<data factory name>"
$linkedServiceName = "<filesystem linked service name>"

Set-AzDataFactoryV2LinkedService `
    -DataFactoryName $dataFactoryName `
    -ResourceGroupName $resourceGroupName `
    -Name $linkedServiceName `
    -File "./FileSystem-LinkedService.json"
```