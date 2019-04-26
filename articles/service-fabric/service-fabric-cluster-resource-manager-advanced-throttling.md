---
title: Begränsning i cluster resource manager i Service Fabric | Microsoft Docs
description: Lär dig att konfigurera de begränsningar som tillhandahålls av Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 4abc3e4a28b8b98070affe19b7b7ca38f904c45b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384977"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Begränsning av Service Fabric Cluster Resource Manager
Även om du har konfigurerat Klusterresurshanteraren korrekt kan klustret kan få till följd. Exempelvis kan det uppstå samtidiga nod och fel domän fel – vad som skulle hända om som inträffat under en uppgradering? Klusterresurshanteraren försöker alltid åtgärda allt, förbrukar klusterresurser försöker organisera om och rätta till klustret. Begränsningar att ge en backstop så att klustret kan använda resurser stabilisera - noderna är tillbaka, network-partitioner reparation av nodtjänst, korrigerad bits distribueras.

För att hjälpa till med dessa typer av situationer, innehåller Service Fabric Cluster Resource Manager flera begränsningar. Dessa begränsningar är alla ganska stora hammare. Vanligtvis att de får inte ändras utan noggrann planering och testning.

Om du ändrar Cluster Resource Manager-begränsningar kan finjustera du dem till din förväntade faktiska belastningen. Du kan bestämma du behöver ha vissa begränsningar på plats, även om det innebär att klustret tar längre tid att stabilisera i vissa situationer. Testa krävs för att fastställa rätt värden för begränsningar. Begränsningar måste vara tillräckligt högt för att att klustret svarar på ändringar i en rimlig tidsperiod och med låg till faktiskt hindra för mycket resursförbrukning. 

I de flesta fall har vi sett kunder använda begränsningar som det har varit eftersom de redan i en begränsad resurs-miljö. Några exempel är begränsad nätverksbandbredd för enskilda noder eller diskar som inte kan bygga många tillståndskänsliga repliker parallellt på grund av begränsningar för dataflöde. Utan begränsningar, kan operations utnyttjandet dessa resurser som gör att åtgärder att misslyckas eller vara långsam. I sådana fall kunder används begränsningar och vi visste de utökar hur lång tid tar det klustret för att nå ett stabilt tillstånd. Kunder tolkas även de kan hamna körs vid lägre övergripande tillförlitlighet, även om de har begränsats.


## <a name="configuring-the-throttles"></a>Konfigurera begränsningar

Service Fabric har två mekanismer för begränsning av antalet repliken förflyttningar. Standardmekanismen som fanns före Service Fabric 5.7 representerar begränsning som ett absolut antal flyttar tillåts. Detta fungerar inte för kluster i alla storlekar. I synnerhet för stora kluster vara standardvärdet för litet avsevärt långsammare belastningsutjämning även när det är nödvändigt, samtidigt som du har ingen effekt i mindre kluster. Den här mekanismen som tidigare har ersatts av procent-baserade begränsningar, som kan skalas bättre med dynamiska kluster antalet tjänster och noder ändras regelbundet.

Begränsningar baseras på en procentandel av antalet repliker i klustren. Procent baserad begränsningar aktivera uttrycka regeln: ”inte flytta mer än 10% av repliker i en 10 minuters intervall”, till exempel.

Konfigurationsinställningarna för procent-baserade begränsning är:

  - GlobalMovementThrottleThresholdPercentage - maximalt antal förflyttningar som tillåts i klustret när som helst, uttryckt i procent av totalt antal repliker i klustret. 0 anger att ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThreshold anges, används mer konservativ gränsen.
  - GlobalMovementThrottleThresholdPercentageForPlacement - maximalt antal förflyttningar tillåts under fasen för placering, uttryckt i procent av totalt antal repliker i klustret. 0 anger att ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThresholdForPlacement anges, används mer konservativ gränsen.
  - GlobalMovementThrottleThresholdPercentageForBalancing - maximalt antal förflyttningar tillåts under fasen för belastningsutjämning, uttryckt i procent av totalt antal repliker i klustret. 0 anger att ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThresholdForBalancing anges, används mer konservativ gränsen.

När du anger den begränsning i procent, anger du 5% som 0,05. Intervallet som dessa begränsningar regleras är GlobalMovementThrottleCountingInterval som anges i sekunder.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig.json för distribution av fristående eller Template.json för Azure som värd kluster:

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

### <a name="default-count-based-throttles"></a>Standard baserat på antal begränsningar
Den här informationen tillhandahålls om du har äldre kluster eller fortfarande behålla de här konfigurationerna i kluster som sedan har uppgraderats. I allmänhet rekommenderar vi att de har ersatts med ovanstående procent-baserade begränsningar. Eftersom procent-baserade begränsning är inaktiverad som standard, förblir dessa begränsningar standard-begränsningar för ett kluster förrän de är inaktiverade och ersätts med procent-baserade-begränsningar. 

  - GlobalMovementThrottleThreshold – den här inställningen styr det totala antalet förflyttningar i klustret via en stund. Hur lång tid har angetts i sekunder som GlobalMovementThrottleCountingInterval. Standardvärdet för GlobalMovementThrottleThreshold är 1 000 och standardvärdet för GlobalMovementThrottleCountingInterval är 600.
  - MovementPerPartitionThrottleThreshold – den här inställningen styr det totala antalet förflyttningar alla service partitioner över tid. Hur lång tid har angetts i sekunder som MovementPerPartitionThrottleCountingInterval. Standardvärdet för MovementPerPartitionThrottleThreshold är 50 och standardvärdet för MovementPerPartitionThrottleCountingInterval är 600.

Konfigurationen för dessa begränsningar följer samma mönster som procentandel-baserade begränsning.

## <a name="next-steps"></a>Nästa steg
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastningen i klustret kan du läsa artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om dem, Kolla in den här artikeln på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
