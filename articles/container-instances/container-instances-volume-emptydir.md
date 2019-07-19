---
title: Montera en emptyDir volym i Azure Container Instances
description: Lär dig hur du monterar en emptyDir-volym för att dela data mellan behållarna i en behållar grupp i Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 0dbe26ff1e00e1912cfd63e8383695ca794dd037
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325450"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montera en emptyDir volym i Azure Container Instances

Lär dig hur du monterar en *emptyDir* -volym för att dela data mellan behållarna i en behållar grupp i Azure Container instances.

> [!NOTE]
> Att montera en *emptyDir* volym är för närvarande begränsad till Linux-behållare. Medan vi arbetar för att hämta alla funktioner till Windows-behållare kan du hitta aktuella plattforms skillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>emptyDir volym

*EmptyDir* -volymen ger en skrivbar katalog som är tillgänglig för varje behållare i en behållar grupp. Behållare i gruppen kan läsa och skriva samma filer i volymen, och den kan monteras med samma eller olika sökvägar i varje behållare.

Några exempel använder för en *emptyDir* -volym:

* Scratch Space
* Kontroll punkter under långvariga aktiviteter
* Lagra data som hämtats av en sidvagn-behållare och hanteras av en program behållare

Data i en *emptyDir* -volym behålls genom behållar krascher. Behållare som startas om är dock inte garanterade att bevara data i en *emptyDir* volym.

## <a name="mount-an-emptydir-volume"></a>Montera en emptyDir volym

Om du vill montera en emptyDir-volym i en behållar instans måste du distribuera med hjälp av en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Fyll `volumes` först i matrisen i avsnittet behållar grupp `properties` i mallen. Sedan, för varje behållare i den behållar grupp där du vill montera *emptyDir* -volymen, fyller `volumeMounts` du i matrisen i `properties` avsnittet i behållar definitionen.

Följande Resource Manager-mall skapar till exempel en behållar grupp bestående av två behållare, som var och en monterar *emptyDir* -volymen:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Om du vill se ett exempel på distribution av container instanser med en Azure Resource Manager-mall, se [distribuera grupper med flera behållare i Azure Container instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volym typer i Azure Container Instances:

* [Montera en Azure-filresursen i Azure Container instanser](container-instances-volume-azure-files.md)
* [Montera en gitRepo volym i Azure Container instanser](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)