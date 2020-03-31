---
title: Montera emptyDir-volym till behållargrupp
description: Lär dig hur du monterar en emptyDir-volym för att dela data mellan behållarna i en behållargrupp i Azure Container Instances
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117750"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montera en emptyDir-volym i Azure Container-instanser

Lär dig hur du monterar en *emptyDir-volym* för att dela data mellan behållarna i en behållargrupp i Azure Container Instances. Använd *emptyDir-volymer* som efemära cacheminnen för dina arbetsbelastningar som innehåller behållare.

> [!NOTE]
> Montering av en *emptyDir-volym* är för närvarande begränsad till Linux-behållare. Medan vi arbetar för att få alla funktioner till Windows-behållare, kan du hitta aktuella plattformsskillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>emptyDir-volym

*EmptyDir-volymen* ger en skrivbar katalog som är tillgänglig för varje behållare i en behållargrupp. Behållare i gruppen kan läsa och skriva samma filer i volymen, och den kan monteras med samma eller olika sökvägar i varje behållare.

Vissa exempel använder för en *emptyDir-volym:*

* Reputrymme
* Kontrollpunkter under tidskrävande aktiviteter
* Lagra data som hämtats av en sidovagnsbehållare och som betjänas av en programbehållare

Data i en *emptyDir-volym* sparas genom behållarkrascher. Behållare som startas om är dock inte garanterade att spara data i en *emptyDir-volym.* Om du stoppar en behållargrupp sparas inte *emptyDir-volymen.*

Den maximala storleken på en Linux *emptyDir-volym* är 50 GB.

## <a name="mount-an-emptydir-volume"></a>Montera en emptyDir-volym

Om du vill montera en emptyDir-volym i en behållarinstans kan du distribuera med en [Azure Resource Manager-mall,](/azure/templates/microsoft.containerinstance/containergroups)en [YAML-fil](container-instances-reference-yaml.md)eller andra programmatiska metoder för att distribuera en behållargrupp.

Fyll först i `volumes` matrisen i `properties` behållargruppen i filen. För varje behållare i behållargruppen där du vill montera *emptyDir-volymen* `volumeMounts` fyller du `properties` i matrisen i avsnittet i behållardefinitionen.

I följande Resource Manager-mall skapas till exempel en behållargrupp som består av två behållare, som var och en monterar *emptyDir-volymen:*

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Information om hur du ser exempel på distribution av behållargrupper finns i [Distribuera en grupp med flera behållare med hjälp av en Resource Manager-mall](container-instances-multi-container-group.md) och [Distribuera en grupp med flera behållare med hjälp av en YAML-fil](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en Azure-filresurs i Azure Container Instances](container-instances-volume-azure-files.md)
* [Montera en gitRepo-volym i Azure Container-instanser](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)