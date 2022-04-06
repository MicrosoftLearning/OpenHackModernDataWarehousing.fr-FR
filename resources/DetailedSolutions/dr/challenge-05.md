---
ms.openlocfilehash: ac2fca1d5a874c57bb13ce9be13ce684a1c654c8
ms.sourcegitcommit: 3ff1164f4921cd2b423d97e7ff29cf3184026d57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2022
ms.locfileid: "140766004"
---
## <a name="challenge-5-a-star-is-born"></a>Défi 5 : Une étoile est née

Ce défi porte sur l’optimisation du « tout-en-un » du défi précédent pour atteindre les objectifs décisionnels immédiats. Les fichiers PBIX sont fournis à l’équipe, mais celle-ci doit créer et remplir le schéma qui sert ces rapports.

Nous documentons la solution SQL DW avec Polybase. Il existe d’autres moyens.
Compte tenu de l’échelle des données, les équipes peuvent également créer un schéma en étoile dans Azure SQL au lieu de SQL DW.
Elles peuvent choisir de mettre Analysis Services par-dessus.
Elles peuvent choisir de pousser (push) les données dans le schéma en étoile avec un connecteur Spark plutôt que Polybase.

### <a name="sql-dw-polybase-solution"></a>Solution SQL DW Polybase

Reportez-vous au répertoire [star-schema](./star-schema/).
Combiné au Guide des autocars, cela permet de parcourir les points clés de la solution.
Ce travail étant toujours en cours et la solution incomplète, le reste du défi devrait se résumer à suivre les mêmes modèles pour traiter les tables de faits et les dimensions restantes.

Les scripts ont été divisés en répertoires classés par ordre numérique.
Dans le répertoire de chaque phase, les scripts sont à nouveau classés par ordre numérique.