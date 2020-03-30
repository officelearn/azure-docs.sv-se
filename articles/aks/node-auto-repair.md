---
title: Reparera Azure Kubernetes Service-noder (Azure Kubernetes Service) (Automatiskt)
description: Lär dig mer om automatisk reparation av nod och hur AKS åtgärdar brutna arbetsnoder.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284848"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes Service (AKS) nod automatisk reparation

AKS kontrollerar kontinuerligt arbetarnodernas hälsotillstånd och utför automatisk reparation av noderna om de blir felaktiga. Den här dokumentationen beskriver hur Azure Kubernetes Service (AKS) övervakar arbetsnoder och reparerar felaktiga arbetsnoder.  Dokumentationen är att informera AKS-operatörer om hur nodreparationsfunktioner har. Det är också viktigt att notera att Azure-plattformen [utför underhåll på virtuella datorer][vm-updates] som uppstår problem. AKS och Azure arbetar tillsammans för att minimera avbrott i tjänsten för dina kluster.

> [!Important]
> Nod automatisk reparationsfunktioner stöds för närvarande inte för Windows Server-nodpooler.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Så här söker AKS efter felaktiga noder

> [!Note]
> AKS vidtar reparationsåtgärder för noder med användarkontot **aks-remediator**.

AKS använder regler för att avgöra om en nod är ett feltillstånd och behöver repareras. AKS använder följande regler för att avgöra om automatisk reparation behövs.

* Noden rapporterar status **för NotReady** vid på varandra följande kontroller inom en 10-minuters tidsram
* Noden rapporterar inte status inom 10 minuter

Du kan manuellt kontrollera hälsotillståndet för dina noder med kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Så här fungerar automatisk reparation

> [!Note]
> AKS vidtar reparationsåtgärder för noder med användarkontot **aks-remediator**.

Det här problemet gäller **för skalningsuppsättningar för virtuella datorer**.  Automatisk reparation tar flera steg för att reparera en trasig nod.  Om en nod bedöms vara felfritt försöker AKS flera reparationssteg.  Stegen utförs i den här ordningen:

1. När behållarkörningen inte svarar i 10 minuter startas de misslyckade körningstjänsterna om på noden.
2. Om noden inte är klar inom 10 minuter startas noden om.
3. Om noden inte är klar inom 30 minuter avbildas noden på om.

> [!Note]
> Om flera noder är felaktiga repareras de en efter en

## <a name="next-steps"></a>Nästa steg

Använd [tillgänglighetszoner][availability-zones] för att öka hög tillgänglighet med aks-klusterarbetsbelastningarna.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
