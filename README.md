---
ms.openlocfilehash: afdb81fdc5caae0ca5f84fe17090c3e37026e1af
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140765819"
---
# <a name="mdw-openhack-content-repo"></a>Dépôt de contenu MDW OpenHack

Bonjour. Bienvenue dans le dépôt de collaboration Modern Data Warehouse OpenHack.
Ici se trouvent toutes les ressources qui génèrent le contenu et les ressources sous-jacentes pour l’exécution de cet événement. Vous pouvez trouver plus d’informations ainsi que des liens dans les éléments ci-dessous.

# <a name="repo-structure"></a>Structure du référentiel
* **portal** (version en anglais)
    * **en**
        * Fichiers Markdown pour les défis dans le portail OpenHack
        * Fichier JSON définissant la structure lab du portail Opsgility
* **resources**
    * Guide des coachs
    * Ensemble de rubriques pour les coachs et les participants
    * 1-pager
    * Ressources supplémentaires pour les coachs et les parties prenantes OpenHack
    * **solutions**
        * code de solution validé pour les défis

## <a name="deployment-pipeline"></a>Pipeline de déploiement

Le pipeline de déploiement pour ce contenu fonctionne de la façon suivante :

![Pipeline de mise en production du contenu](./resources/CoachesGuide/content/images/release-pipeline.jpg)

### <a name="artifact"></a>Artefact

L’artefact est le résultat de [ce pipeline de build](https://dev.azure.com/cseeest/OpenHack/_build?definitionId=9), qui produit une structure à deux dossiers, un pour le Guide des coachs et un autre pour le portail Opsgility.

### <a name="stage-1---validate-portal-content"></a>Étape 1 - Valider le contenu du portail

Avant d’être publié sur Opsgility, tout le contenu est d’abord publié ici :

[https://openhack-validation-env.azurewebsites.net/](https://openhack-validation-env.azurewebsites.net/)

En plus d’un fichier pour le Guide des coachs, dans un format de document Word.

### <a name="stage-2---publish-content-to-azure-blob"></a>Étape 2 - Publier le contenu sur un blob Azure

C’est là que le contenu est publié en *Production*. Quand nous déployons le contenu sur ce blob, il est disponible pour la création de classes avec cette version.

## <a name="source-code-structure"></a>Structure du code source

[![État de la build](https://dev.azure.com/cseeest/OpenHack/_apis/build/status/Data-ModernDataWarehousing-CI?branchName=master)](https://dev.azure.com/cseeest/OpenHack/_build/latest?definitionId=9&branchName=master)

### <a name="portal"></a>Portail

```bash
portal
    /en
```

Ce `portal` stocke le contenu à publier sur le portail Opsgility. Vous pouvez ajouter plusieurs langues de contenu. Dans l’exemple ci-dessus, le dossier `en` est le contenu en anglais. Pour publier du contenu en espagnol, par exemple, vous devez mettre une version espagnole du contenu dans le dossier `portal/es`.

```bash
resources
    /CoachesGuide
        /content
        Coaches-Guide-Template.docx
```

### <a name="resources"></a>Ressources

#### <a name="coaches-guide"></a>Guide des coachs

Tous les fichiers de contenu et références qui ne sont pas sur le portail sont stockés sous `resources`.
Le contenu du Guide des coachs est également stocké au format Markdown parce qu’il est lisible dans le portail Azure DevOps et qu’il peut être exporté vers divers formats de fichier.

Actuellement, nous avons déjà un processus sur `CI-pipeline.yaml` qui l’exporte vers un document Word. `Coaches-Guide-Template.docx` est le modèle utilisé pour générer le document avec les en-têtes et les styles par défaut.

#### <a name="etl-ddl"></a>ETL-DDL

```bash
resources
    /ETL-DDL
        /Ingest
        /Normalize
```

Collection de notebooks qui peuvent être importés dans un espace de travail Databricks.
Ces notebooks appliquent des schémas sur les données extraites, puis mettent en conformité les données de tous les systèmes sources dans le but de créer un jeu de données intermédiaire.

### <a name="one-pager"></a>Document d’une page

```bash
resources
    /one-pager
        MDWOp1pg.docx
```

Le dossier `one-pager` contient un seul fichier (`MDWOp1pg.docx`) qui est le document d’une seule page décrivant OpenHack, son contexte, la structure du défi et les technologies associées.

### <a name="data-catalog"></a>Data Catalog

```bash
resources
    /DataCatalog
        *.csv
        DataCatalog.xlsx
```

Un **exemple** de catalogue de données pour tous les systèmes sources et un schéma conforme cible. Les fichiers `.csv` existent uniquement dans le but de prendre en charge les utilisateurs qui n’ont pas d’application pouvant ouvrir le fichier `.xlsx`. Chaque CSV représente une feuille du classeur XLSX.

### <a name="tooling"></a>Outillage

```bash
tooling
    /address-generation
    /Databases
    /Deployment
    /Utility
```

#### <a name="address-generation"></a>Génération d’adresses

Il s’agit d’une application Node stockée sous `address-generation` pour générer des adresses aléatoires. Vous n’avez peut-être pas besoin de la modifier, sauf si vous devez modifier la sauvegarde de la base de données d’adresses actuelle qui est utilisée pour provisionner l’environnement de la classe.

#### <a name="databases"></a>Bases de données

Il s’agit des projets de base de données pour la création et le déploiement des schémas sources, ainsi que des schémas en étoile cibles.

#### <a name="deployment"></a>Déploiement

Le dossier `Deployment` contient tous les fichiers utilisés pour déployer un ou plusieurs environnements pour une classe. Pour plus d’informations sur cet ensemble de scripts, consultez [ce fichier README](./tooling/Deployment/README.md).

### <a name="markdown-lint-file"></a>Fichier lint Markdown

`.markdownlin.json` est le fichier de configuration lint utilisé sur le pipeline de build CI Azure DevOps (`CI-pipeline.yaml`) et sur Visual Studio Code, si vous utilisez l’[extension VS Code markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint).

### <a name="ci-build-pipeline-definition-file"></a>Fichier de définition du pipeline de build CI

Le fichier `CI-pipeline.yaml` stocke toute la configuration pour l’exécution de la build CI sur le contenu OpenHack. Cette build est actuellement utilisée pour les builds PR et pour CI sur des branches autres que la branche maîtresse.
