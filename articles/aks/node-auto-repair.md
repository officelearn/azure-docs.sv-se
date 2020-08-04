---
title: Reparera AKS-noder (Azure Kubernetes service) automatiskt
description: Lär dig mer om funktionen för automatisk reparation av noder och hur AKS åtgärdar brutna arbetsnoder.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 7fcb7b380f3694aaf34328019c3e09f5157c9e64
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542050"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatisk reparation av Azure Kubernetes service-noden (AKS)

AKS kontrollerar kontinuerligt hälso tillståndet för arbetsnoder och utför automatisk reparation av noder om de blir felaktiga. Det här dokumentet informerar operatörer om hur funktionen för automatisk reparation av noder beter sig. Förutom AKS-reparationer utför Azure VM-plattformen [underhåll av Virtual Machines][vm-updates] som upplever problem. AKS och virtuella Azure-datorer fungerar tillsammans för att minimera tjänst avbrott i kluster.

## <a name="limitations"></a>Begränsningar

* Windows Node-pooler stöds inte idag.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hur AKS söker efter felaktiga noder

AKS använder regler för att avgöra om en nod är ohälsosam och behöver repare ras. AKS använder följande regler för att avgöra om automatisk reparation krävs.

* Noden rapporterar status för **notrappsteg** på efterföljande kontroller inom en 10-minuters tidsram
* Noden rapporterar ingen status inom 10 minuter

Du kan kontrol lera hälso tillståndet för dina noder manuellt med kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Så här fungerar automatisk reparation

> [!Note]
> AKS initierar reparations åtgärder med användar kontot **AKS-remediator**.

Om en nod bedöms vara ohälsosam enligt reglerna ovan och inte är felfri under 10 minuter i följd, startar AKS om noden. Om noderna inte är felfria efter den första reparations åtgärden, undersökas ytterligare åtgärder av AKS-tekniker.
  
Om flera noder är i fel tillstånd under en hälso kontroll repare ras varje nod individuellt innan en ny reparation påbörjas.

## <a name="next-steps"></a>Nästa steg

Använd [Tillgänglighetszoner][availability-zones] för att öka hög tillgänglighet med AKS-klustrets arbets belastningar.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
