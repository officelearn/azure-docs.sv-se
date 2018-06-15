---
title: Montera en emptyDir volymen i Azure Container instanser
description: Lär dig hur du monterar en emptyDir volym om du vill dela data mellan behållare i en behållare grupp i Azure Container instanser
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 89289a7a0bb5c486c662d528c5014bdbd8eebaca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164898"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montera en emptyDir volymen i Azure Container instanser

Lär dig hur du monterar en *emptyDir* volymen för att dela data mellan behållare i en behållare grupp i Azure Container instanser.

> [!NOTE]
> Montera en *emptyDir* volym är för närvarande begränsad till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="emptydir-volume"></a>emptyDir volym

Den *emptyDir* volym ger en skrivbar katalog som är tillgängliga för varje behållare i en grupp i behållaren. Behållare i gruppen kan läsa och skriva samma filer i volymen, och kan monteras med hjälp av samma eller olika sökvägar i varje behållare.

Vissa exempel som används för en *emptyDir* volymen:

* Arbetsyta
* Kontrollpunkter under tidskrävande uppgifter
* Lagra data hämtas av en sidovagn behållare och hanteras av en behållare för program

Data i en *emptyDir* volym sparas via behållaren kraschar. Behållare som har startats om, men är inte garanterat att spara data i en *emptyDir* volym.

## <a name="mount-an-emptydir-volume"></a>Montera en emptyDir volym

Om du vill montera en emptyDir volym i en behållare instans, måste du distribuera med hjälp av en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Först fylla i `volumes` matris i behållargruppen `properties` avsnitt i mallen. I varje behållare i behållargruppen där du vill montera den *emptyDir* volym, fylla i `volumeMounts` matris i den `properties` avsnitt i definition av behållare.

Till exempel följande Resource Manager-mall skapas en behållare grupp bestående av två behållare var och en av vilka monteringar den *emptyDir* volymen:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Ett exempel på distribution av behållare-instans med en Azure Resource Manager-mall finns i [distribuera flera behållare grupper i Azure Behållarinstanser](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper av i Azure Container instanser:

* [Montera en Azure-filresursen i Azure Container instanser](container-instances-volume-azure-files.md)
* [Montera en gitRepo volym i Azure Container instanser](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container instanser](container-instances-volume-secret.md)