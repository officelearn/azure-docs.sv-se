---
title: "Montera en gitRepo volym Azure Behållarinstanser"
description: "Lär dig hur du monterar en gitRepo volym för att klona en Git-lagringsplats i behållarinstanser"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 9acde9259fcb392458e7b2fa7d3369776978285e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montera en gitRepo volym i Azure Container instanser

Lär dig hur du monterar en *gitRepo* volymen för att klona en Git-lagringsplats i behållarinstanser.

> [!NOTE]
> Montera en *gitRepo* volym är för närvarande begränsad till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>gitRepo volym

Den *gitRepo* volym monterar en katalog och klonar på angivna Git-lagringsplatsen till den vid start av behållare. Med hjälp av en *gitRepo* volym i behållarinstanser, du kan undvika att lägga till kod för att göra det i dina program.

När du monterar en *gitRepo* volym, kan du ange tre egenskaper att konfigurera volymen:

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| `repository` | Ja | En fullständig URL inklusive `http://` eller `https://`, av Git-lagringsplats kloning.|
| `directory` | Nej | Katalogen där databasen ska klonas. Sökvägen måste inte innehålla eller börja med ”`..`”.  Om du anger ”`.`”, databasen klonas till katalogen för den volymen. I annat fall klonas på Git-lagringsplatsen i en underkatalog till det angivna namnet i katalogen volym. |
| `revision` | Nej | Commit-hash med revision kloning. Om inget anges den `HEAD` revision klonas. |

## <a name="mount-a-gitrepo-volume"></a>Montera en gitRepo volym

Montera en *gitRepo* volym i en behållare instans, måste du distribuera med hjälp av en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Först fylla i `volumes` matris i behållargruppen `properties` avsnitt i mallen. I varje behållare i behållargruppen där du vill montera den *gitRepo* volym, fylla i `volumeMounts` matris i den `properties` avsnitt i definition av behållare.

Till exempel skapas följande Resource Manager-mall en behållare grupp bestående av en enskild behållare. Behållaren klonar två GitHub-lagringsplatser som anges av den *gitRepo* volymblock. Den andra volymen innehåller ytterligare egenskaper som anger en katalog att klona till och commit-hash för en specifik version att klona.

[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Katalogstruktur för två klonade repor som definierats i mallen för föregående är:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Ett exempel på distribution av behållare-instans med en Azure Resource Manager-mall finns i [distribuera flera behållare grupper i Azure Behållarinstanser](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper av i Azure Container instanser:

* [Montera en Azure-filresursen i Azure Container instanser](container-instances-volume-azure-files.md)
* [Montera en emptyDir volymen i Azure Container instanser](container-instances-volume-emptydir.md)
* [Montera en hemlig volym i Azure Container instanser](container-instances-volume-secret.md)
