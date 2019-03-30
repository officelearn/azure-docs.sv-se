---
title: Ange inställningar för mått och placering i Azure Service Fabric | Microsoft Docs
description: Lär dig mer om att beskriva en Service Fabric-tjänst genom att ange mått, placeringsbegränsningar och andra placeringsprinciper.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 21fcac62c9335652d0c682a6ac889be82e649464
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661555"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurera klusterinställningar resource manager för Service Fabric-tjänster
Service Fabric Cluster Resource Manager kan finmaskig kontroll över de regler som styr varje person med namnet på tjänsten. Varje namngiven tjänst kan ange regler för hur den ska allokeras i klustret. Varje namngiven tjänst kan också definiera en uppsättning mått som läggs till rapporten, inklusive hur viktiga de är till den tjänsten. Konfigurera services delar upp till tre olika aktiviteter:

1. Konfigurera placeringsbegränsningar
2. Konfigurera mått
3. Konfigurera avancerade placeringsprinciper och andra regler (mindre vanliga)

## <a name="placement-constraints"></a>Placeringsbegränsningar
Placeringsbegränsningar används för att styra vilka noder i klustret en tjänst kan köras på. Vanligtvis med en viss namnet tjänsteinstans eller alla tjänster av en viss typ som anger att den körs på en viss typ av nod. Placeringsbegränsningar är utökningsbar. Du kan ange valfri uppsättning egenskaper per nodtyp och sedan välja de begränsningar när du skapar tjänster. Du kan också ändra placeringsbegränsningar för en tjänst när den körs. På så sätt kan du svara på ändringar i klustret eller krav för tjänsten. Egenskaperna för en viss nod kan också uppdateras dynamiskt i klustret. Mer information om placeringsbegränsningar och hur du konfigurerar dem finns i [i den här artikeln](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Mått
Mått är uppsättningen resurser som krävs för en viss namngiven tjänst. En metrisk tjänstkonfigurationen innehåller hur mycket av den resurs som varje tillståndskänsliga replik eller tillståndslösa instans av den tjänsten förbrukar som standard. Mått kan även innehålla en vikt som anger hur viktigt belastningsutjämning som mått är att tjänsten, i fall kompromisser krävs.

## <a name="advanced-placement-rules"></a>Avancerade placeringsregler
Det finns andra typer av placeringsregler som är användbara i mindre vanliga scenarier. Några exempel är:
- Restriktioner som skyddar med geografiskt distribuerade kluster
- Vissa arkitekturer för program

Andra placeringsregler konfigureras via korrelationer eller principer.

## <a name="next-steps"></a>Nästa steg
- Mått är hur Service Fabric-kluster Resource Manager hanterar förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem kan du kolla in [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
- Tillhörighet är ett läge som du kan konfigurera för dina tjänster. Det är inte vanligt, men om du behöver den kan du läsa om den [här](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Det finns många olika placeringsregler som kan konfigureras på din tjänst för att hantera fler scenarier. Du kan läsa mer om de olika placeringsprinciper [här](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastningen i klustret kan du läsa artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om dem, Kolla in den här artikeln på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
