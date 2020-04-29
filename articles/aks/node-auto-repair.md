---
title: Reparera AKS-noder (Azure Kubernetes service) automatiskt
description: Lär dig mer om funktionen för automatisk reparation av noder och hur AKS åtgärdar brutna arbetsnoder.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284848"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatisk reparation av Azure Kubernetes service-noden (AKS)

AKS kontrollerar kontinuerligt hälso tillståndet för arbetsnoder och utför automatisk reparation av noder om de blir felaktiga. I den här dokumentationen beskrivs hur Azure Kubernetes service (AKS) övervakar arbetsnoder och reparerar Felaktiga arbetsnoder.  Dokumentationen är att informera AKS-operatörer om funktions sättet för reparation av noder. Det är också viktigt att Observera att Azure-plattformen [utför underhåll på Virtual Machines][vm-updates] det uppstår problem. AKS och Azure fungerar tillsammans för att minimera tjänst avbrott i klustren.

> [!Important]
> Funktionen för automatisk reparation av noder stöds inte för närvarande för Windows Server-adresspooler.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hur AKS söker efter felaktiga noder

> [!Note]
> AKS tar reparations åtgärder på noder med användar kontot **AKS-remediator**.

AKS använder regler för att avgöra om en nod är i ett ohälsosamt tillstånd och behöver repare ras. AKS använder följande regler för att avgöra om automatisk reparation krävs.

* Noden rapporterar status för **notrappsteg** på efterföljande kontroller inom en 10-minuters tidsram
* Noden rapporterar ingen status inom 10 minuter

Du kan kontrol lera hälso tillståndet för dina noder manuellt med kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Så här fungerar automatisk reparation

> [!Note]
> AKS tar reparations åtgärder på noder med användar kontot **AKS-remediator**.

Det här fungerar för **Virtual Machine Scale Sets**.  Automatisk reparation tar flera steg för att reparera en bruten nod.  Om en nod bedöms vara skadad, försöker AKS utföra flera åtgärder.  Stegen utförs i följande ordning:

1. När behållar körningen slutar svara i 10 minuter startas de misslyckade körnings tjänsterna om på noden.
2. Om noden inte är klar inom 10 minuter startas noden om.
3. Om noden inte är klar inom 30 minuter, avbildas noden på nytt.

> [!Note]
> Om flera noder inte är felfria reparerar de en i taget

## <a name="next-steps"></a>Nästa steg

Använd [Tillgänglighetszoner][availability-zones] för att öka hög tillgänglighet med AKS-klustrets arbets belastningar.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
