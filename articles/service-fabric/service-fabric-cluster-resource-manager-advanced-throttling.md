---
title: Begränsning i resurshanteraren för klusterresurshanteraren för service fabric-kluster
description: Lär dig att konfigurera de begränsningar som tillhandahålls av Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452163"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Begränsning av resurshanteraren för klusterresurser för servicevävnad
Även om du har konfigurerat Klusterresurshanteraren korrekt kan klustret störas. Det kan till exempel finnas samtidiga nod- och feldomänfel - vad skulle hända om det inträffade under en uppgradering? Klusterresurshanteraren försöker alltid åtgärda allt och förbrukar klustrets resurser för att försöka ordna om och åtgärda klustret. Begränsningar hjälper till att tillhandahålla en backstop så att klustret kan använda resurser för att stabilisera - noderna kommer tillbaka, nätverkspartitionerna läker, korrigerade bitar distribueras.

För att hjälpa till med den här typen av situationer innehåller Service Fabric Cluster Resource Manager flera begränsningar. Dessa gasreglage är alla ganska stora hammare. Generellt bör de inte ändras utan noggrann planering och testning.

Om du ändrar cluster resource manager s gasreglage, bör du ställa in dem till din förväntade faktiska belastning. Du kan bestämma att du måste ha några begränsningar på plats, även om det innebär att klustret tar längre tid att stabilisera i vissa situationer. Testning krävs för att fastställa rätt värden för gasspjäll. Begränsningar måste vara tillräckligt höga för att klustret ska kunna svara på ändringar på en rimlig tid och tillräckligt låg för att faktiskt förhindra för mycket resursförbrukning. 

För det mesta har vi sett kunder använda gasreglage har det varit för att de redan var i en resurs begränsad miljö. Några exempel är begränsad nätverksbandbredd för enskilda noder eller diskar som inte kan skapa många tillståndskänsliga repliker parallellt på grund av begränsningar för dataflöde. Utan begränsningar kan åtgärder överbelasta dessa resurser, vilket gör att åtgärder misslyckas eller vara långsamma. I dessa situationer kunder använde gasreglage och visste att de förlängde den tid det skulle ta klustret att nå ett stabilt tillstånd. Kunderna förstod också att de kunde hamna igång med lägre total tillförlitlighet medan de var strypta.


## <a name="configuring-the-throttles"></a>Konfigurera gasreglaget

Service Fabric har två mekanismer för begränsning av antalet replikrörelser. Standardmekanismen som fanns före Service Fabric 5.7 representerar begränsning som ett absolut antal tillåtna drag. Detta fungerar inte för kluster av alla storlekar. Särskilt för stora kluster kan standardvärdet vara för litet, vilket avsevärt kan sakta ner balanseringen även när det är nödvändigt, samtidigt som det inte har någon effekt i mindre kluster. Den här tidigare mekanismen har ersatts av procentbaserad begränsning, som skalas bättre med dynamiska kluster där antalet tjänster och noder ändras regelbundet.

Begränsningarna baseras på en procentandel av antalet repliker i klustren. Procentbaserade begränsningar gör det möjligt att uttrycka regeln: "flytta inte mer än 10% av replikerna i ett 10 minuters intervall", till exempel.

Konfigurationsinställningarna för procentbaserad begränsning är:

  - GlobalMovementThrottleThresholdPercentage - Maximalt antal tillåtna i klustret när som helst, uttryckt i procent av det totala antalet repliker i klustret. 0 anger ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThreshold anges används den mer konservativa gränsen.
  - GlobalMovementThrottleThresholdPercentageForPlacement - Maximalt antal tillåtna rörelser under placeringsfasen, uttryckt i procent av det totala antalet repliker i klustret. 0 anger ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThresholdForPlacement anges används den mer konservativa gränsen.
  - GlobalMovementThrottleThresholdPercentageForBalancing - Maximalt antal tillåtna rörelser under balanseringsfasen, uttryckt i procent av det totala antalet repliker i klustret. 0 anger ingen gräns. Standardvärdet är 0. Om både den här inställningen och GlobalMovementThrottleThresholdForBalancing anges används den mer konservativa gränsen.

När du anger begränsningsprocenten anger du 5 % som 0,05. Det intervall som dessa begränsningar styrs på är GlobalMovementThrottleCountingInterval, som anges i sekunder.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig.json för fristående distributioner eller Template.json för Azure-värdkluster:

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

### <a name="default-count-based-throttles"></a>Standardantalsbaserade begränsningar
Den här informationen tillhandahålls om du har äldre kluster eller fortfarande behåller dessa konfigurationer i kluster som sedan har uppgraderats. I allmänhet rekommenderas att dessa ersätts med de procentbaserade gasreglagena ovan. Eftersom procentbaserad begränsning är inaktiverad som standard förblir dessa begränsningar standardbegränsningar för ett kluster tills de inaktiveras och ersätts med de procentbaserade begränsningarna. 

  - GlobalMovementThrottleThreshold – den här inställningen styr det totala antalet förflyttningar i klustret under en viss tid. Tiden anges i sekunder som GlobalMovementThrottleCountingInterval. Standardvärdet för GlobalMovementThrottleThreshold är 1000 och standardvärdet för GlobalMovementThrottleCountingInterval är 600.
  - MovementPerPartitionThrottleThreshold – den här inställningen styr det totala antalet förflyttningar för en tjänstpartition under en viss tid. Tiden anges i sekunder som MovementPerPartitionThrottleCountingInterval. Standardvärdet för MovementPerPartitionThrottleThreshold är 50 och standardvärdet för MovementPerPartitionThrottleCountingInterval är 600.

Konfigurationen för dessa begränsningar följer samma mönster som den procentbaserade begränsningen.

## <a name="next-steps"></a>Nästa steg
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastning i klustret kan du läsa artikeln om [utjämningsbelastning](service-fabric-cluster-resource-manager-balancing.md)
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om dem kan du läsa den här artikeln om [hur du beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
