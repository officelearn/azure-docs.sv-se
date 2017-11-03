---
title: "Ange inställningar för mått och placering i Azure mikrotjänster | Microsoft Docs"
description: "Som beskriver ett Service Fabric-tjänsten genom att ange mått, placeringen och andra placeringsprinciper."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 084fdf2f9a65eed3e444851afb88390aacf33738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurera klustret resource manager-inställningar för Service Fabric-tjänster
Service Fabric klustret Resource Manager kan detaljerad kontroll över de regler som styr varje person med namnet på tjänsten. Varje tjänsten kan ange regler för hur den ska allokeras i klustret. Varje tjänsten kan också definiera en uppsättning mått som rapporten, inklusive hur viktigt är de till den tjänsten. Konfigurera services uppdelad i tre olika aktiviteter:

1. Konfigurera placeringen
2. Konfigurera mått
3. Konfigurera avancerade placeringsprinciper och andra regler (mindre vanliga)

## <a name="placement-constraints"></a>Placeringen
Placeringen används för att styra vilka noder i klustret en tjänst kan köras på. Vanligtvis med en viss namnet tjänstinstans eller alla tjänster av en viss typ begränsad ska köras på en viss typ av noden. Placeringen är extensible. Du kan definiera en uppsättning egenskaper per nodtypen och sedan välja för dem med begränsningar när du skapar tjänster. Du kan också ändra placeringen för en tjänst när den körs. På så sätt kan du svara på förändringar i klustret eller krav på tjänsten. Egenskaperna för en viss nod kan också uppdateras dynamiskt i klustret. Mer information om placeringen och hur de konfigureras finns i [i den här artikeln](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Mått
Mått är uppsättning resurser som krävs för en viss namngiven tjänst. En tjänst mått konfigurationen innefattar hur mycket av den här resursen varje tillståndskänslig replik eller tillståndslösa instans av den tjänsten använder som standard. Mått kan även innehålla en vikt som anger hur viktigt att måttet är till den tjänsten i fall kompromisser krävs.

## <a name="advanced-placement-rules"></a>Av avancerade placeringsregler
Det finns andra typer av av placeringsregler som är användbara i mindre vanliga scenarier. Några exempel är:
- Begränsningarna som hjälper till med geografiskt distribuerade kluster
- Vissa programarkitekturer

Andra placeringsregler konfigureras via korrelationer eller principer.

## <a name="next-steps"></a>Nästa steg
- Mått är hur Service Fabric-kluster Resource Manager hanterar förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem kolla [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
- Tillhörighet är ett läge som du kan konfigurera för dina tjänster. Det är inte vanligt, men om det behövs kan du läsa om den [här](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Det finns många olika placeringsregler som kan konfigureras på din tjänst för att hantera fler scenarier. Du kan läsa mer om de olika placeringsprinciper [här](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Börja från början och [få en introduktion till Service Fabric klustret Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Om du vill veta mer om hur klustret Resource Manager hanterar och balanserar belastningen i klustret kan ta en titt i artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
- Klustret Resource Manager har många alternativ för att beskriva klustret. Kolla in den här artikeln om du vill veta mer om dem på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
