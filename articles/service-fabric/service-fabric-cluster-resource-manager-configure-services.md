---
title: Ange mått och placeringsinställningar
description: Lär dig hur du beskriver en Service Fabric-tjänst genom att ange mått, placeringsbegränsningar och andra placeringsprinciper.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610105"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurera inställningar för klusterresurshanteraren för Service Fabric-tjänster
Service Fabric Cluster Resource Manager ger finkornig kontroll över de regler som styr varje enskild tjänst som heter. Varje namngiven tjänst kan ange regler för hur den ska fördelas i klustret. Varje namngiven tjänst kan också definiera den uppsättning mått som den vill rapportera, inklusive hur viktiga de är för den tjänsten. Konfigurera tjänster delas upp i tre olika uppgifter:

1. Konfigurera placeringsbegränsningar
2. Konfigurera mått
3. Konfigurera avancerade placeringsprinciper och andra regler (mindre vanliga)

## <a name="placement-constraints"></a>Placeringsbegränsningar
Placeringsbegränsningar används för att styra vilka noder i klustret som en tjänst faktiskt kan köras på. Vanligtvis en viss namngiven tjänstinstans eller alla tjänster av en viss typ som är begränsade att köras på en viss typ av nod. Placeringsbegränsningar kan utbyggas. Du kan definiera alla egenskaper per nodtyp och sedan välja för dem med begränsningar när du skapar tjänster. Du kan också ändra en tjänsts placeringsbegränsningar medan den körs. På så sätt kan du svara på ändringar i klustret eller kraven för tjänsten. Egenskaperna för en viss nod kan också uppdateras dynamiskt i klustret. Mer information om placeringsbegränsningar och hur du konfigurerar dem finns i [den här artikeln](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Mått
Mått är den uppsättning resurser som en viss namngiven tjänst behöver. En tjänsts måttkonfiguration innehåller hur mycket av resursen varje tillståndskänslig replik eller tillståndslös instans av tjänsten förbrukar som standard. Mått innehåller också en vikt som anger hur viktigt balanseringen det måttet är för den tjänsten, om kompromisser är nödvändiga.

## <a name="advanced-placement-rules"></a>Avancerade placeringsregler
Det finns andra typer av placeringsregler som är användbara i mindre vanliga scenarier. Några exempel är:
- Begränsningar som hjälper till med geografiskt distribuerade kluster
- Vissa programarkitekturer

Andra placeringsregler konfigureras via antingen Korrelationer eller principer.

## <a name="next-steps"></a>Nästa steg
- Mått är hur Service Fabric Cluster Resource Manger hanterar förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem kan du läsa [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
- Tillhörighet är ett läge som du kan konfigurera för dina tjänster. Det är inte vanligt, men om du behöver det kan du lära dig om det [här](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Det finns många olika placeringsregler som kan konfigureras på din tjänst för att hantera ytterligare scenarier. Du kan läsa mer om dessa olika placeringspolicyer [här](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastning i klustret kan du läsa artikeln om [utjämningsbelastning](service-fabric-cluster-resource-manager-balancing.md)
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om dem kan du läsa den här artikeln om [hur du beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
