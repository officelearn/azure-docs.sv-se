---
title: Montera en emptyDir-volym i Azure Container Instances
description: Lär dig hur du monterar en emptyDir volym för att dela data mellan behållare i en behållargrupp i Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 98a72123a05fa7d8dc16be7ddb787f2a2cf7e4d1
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857660"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montera en emptyDir-volym i Azure Container Instances

Lär dig hur du monterar en *emptyDir* volymen för att dela data mellan behållare i en behållargrupp i Azure Container Instances.

> [!NOTE]
> Montera en *emptyDir* volymen är för närvarande begränsade till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="emptydir-volume"></a>emptyDir volym

Den *emptyDir* volymen ger en skrivbar katalog som är tillgängliga för varje behållare i en behållargrupp. Behållare i gruppen kan läsa och skriva samma filer i volymen och kan monteras med samma eller olika sökvägar i varje behållare.

Vissa exempel som används för en *emptyDir* volym:

* Tillfälligt utrymme
* Kontrollpunkter under långvariga aktiviteter
* Store-data hämtas av en sidovagnsbehållare och hanteras av en programbehållare

Data i en *emptyDir* volym bevaras via behållare krascher. Behållare som startas om, men inte garanterade att bevara data i en *emptyDir* volym.

## <a name="mount-an-emptydir-volume"></a>Montera en emptyDir-volym

Om du vill montera en emptyDir-volym i en behållarinstans, måste du distribuera med hjälp av en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Först fylla i `volumes` matris i behållargruppen `properties` avsnitt i mallen. För varje behållare i behållargruppen där du vill montera den *emptyDir* volym, Fyll i den `volumeMounts` matrisen i den `properties` avsnitt i behållardefinitionen.

Exempelvis följande Resource Manager-mallen skapar en behållargrupp som består av två behållare, var och en av vilka monterar den *emptyDir* volym:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json --> [!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Ett exempel på distribution av behållarinstanser med en Azure Resource Manager-mall finns i [distribuera grupper med flera behållare i Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en Azure-filresursen i Azure Container instanser](container-instances-volume-azure-files.md)
* [Montera en gitRepo volym i Azure Container instanser](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)