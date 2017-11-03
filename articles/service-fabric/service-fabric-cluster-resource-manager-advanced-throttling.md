---
title: "Begränsning i resurshanterare Service Fabric-kluster | Microsoft Docs"
description: "Lär dig hur du konfigurerar begränsningar som tillhandahålls av Service Fabric klustret Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22223923f6783f11178b44c3c3087f17eb8dc41b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Begränsning av Service Fabric klustret Resource Manager
Även om du har konfigurerat klustret Resource Manager korrekt kan kan klustret hämta upplöst. Det kan till exempel vara samtidiga nod och feltolerans domän fel - vad som skulle hända om som uppstod under en uppgradering? Klustret Resource Manager försöker alltid åtgärda allt, förbrukar klusterresurser försök att ordna om och rätta till klustret. Begränsningar ger en backstop så att klustret kan använda resurser för att hålla - noderna komma tillbaka, network-partitioner läka, korrigerade bits distribueras.

Om du vill hjälpa till med sådana här situationer, innehåller Service Fabric klustret Resource Manager flera begränsningar. Dessa begränsningar är alla ganska stort hammare. I allmänhet de får inte ändras utan noggrann planering och testning.

Om du ändrar klustret Resource Manager begränsas, bör du finjustera dem till din förväntade faktiska belastningen. Du kan bestämma du behöver ha vissa begränsningar på plats, även om det innebär att klustret tar längre tid att hålla i vissa situationer. Testa krävs för att fastställa korrekta värden för begränsas. Begränsas måste vara tillräckligt högt för att klustret ska svara på ändringar i rimlig tid och låg nivå faktiskt att för mycket resursförbrukning. 

I de flesta fall har vi sett kunder använder begränsningar har eftersom de redan i en miljö med begränsad resurs. Några exempel är begränsad nätverksbandbredd för enskilda noder eller diskar som inte kan bygga många tillståndskänslig repliker parallellt på grund av begränsningar i genomflöde. Utan begränsningar, kunde operations överväldigande resurserna orsakar åtgärder att misslyckas eller vara långsam. I sådana situationer kunder används begränsningar och känner de utöka hur lång tid tar det klustret för att nå ett stabilt tillstånd. Kunder kan också förstått de kan hamna körs med lägre pålitlighet när de har begränsats.


## <a name="configuring-the-throttles"></a>Konfigurera begränsningar

Service Fabric har två mekanismer för begränsning av antalet replik förflyttningar. Standardmekanism som fanns före Service Fabric 5.7 representerar begränsning som ett absolut antal flyttar tillåts. Detta fungerar inte för kluster i alla storlekar. I synnerhet för stora kluster kan standardvärdet vara för liten avsevärt långsammare belastningsutjämning även när det är nödvändigt, samtidigt som du har ingen effekt i mindre kluster. Denna mekanism för tidigare har ersatts av procent-baserade begränsning som skalas bättre med dynamiska kluster antalet tjänster och noder ändras regelbundet.

Begränsas baseras på en procentandel av antalet repliker i kluster. Percetage baserat begränsas aktivera uttrycka regeln: ”inte flytta mer än 10% av repliker 10: e minut”, till exempel.

Konfigurationsinställningarna för procent-baserade begränsning är:

  - GlobalMovementThrottleThresholdPercentage - maximalt antal förflyttningar tillåts i klustret när som helst, uttryckt i procent av totalt antal repliker i klustret. 0 anger att ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThreshold anges, används mer konservativ gränsen.
  - GlobalMovementThrottleThresholdPercentageForPlacement - maximalt antal förflyttningar av typ får användas under fasen placering uttryckt i procent av totalt antal repliker i klustret. 0 anger att ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThresholdForPlacement anges, används mer konservativ gränsen.
  - GlobalMovementThrottleThresholdPercentageForBalancing - maximalt antal förflyttningar av typ får användas under fasen belastningsutjämning uttryckt i procent av totalt antal repliker i klustret. 0 anger att ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThresholdForBalancing anges, används mer konservativ gränsen.

När du anger den begränsning i procent, anger du 5% som 0,05. Intervallet som dessa begränsningar regleras är GlobalMovementThrottleCountingInterval som anges i sekunder.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

värdbaserade kluster via ClusterConfig.json för fristående distributioner eller Template.json för Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Standard antal baserat begränsningar
Den här informationen anges om du har äldre kluster eller fortfarande behålla dessa konfigurationer i kluster som sedan har uppgraderats. I allmänhet rekommenderar vi att dessa ersätts med ovan procent-baserade begränsas. Eftersom procent-baserade begränsning är inaktiverad som standard förblir dessa begränsningar standard begränsningar för ett kluster tills de är inaktiverade och ersätts med procent-baserade begränsas. 

  - GlobalMovementThrottleThreshold – den här inställningen styr det totala antalet förflyttningar i klustret över tid. Hur lång tid har angetts i sekunder som GlobalMovementThrottleCountingInterval. Standardvärdet för GlobalMovementThrottleThreshold är 1000 och standardvärdet för GlobalMovementThrottleCountingInterval är 600.
  - MovementPerPartitionThrottleThreshold – den här inställningen styr det totala antalet förflyttningar för alla partitioner för tjänsten över tid. Hur lång tid har angetts i sekunder som MovementPerPartitionThrottleCountingInterval. Standardvärdet för MovementPerPartitionThrottleThreshold är 50 och standardvärdet för MovementPerPartitionThrottleCountingInterval är 600.

Konfigurationen för dessa begränsningar följer samma mönster som procentandel-baserade begränsning.

## <a name="next-steps"></a>Nästa steg
- Om du vill veta mer om hur klustret Resource Manager hanterar och balanserar belastningen i klustret kan ta en titt i artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
- Klustret Resource Manager har många alternativ för att beskriva klustret. Kolla in den här artikeln om du vill veta mer om dem på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
