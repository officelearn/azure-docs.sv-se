---
title: Azure Service Fabric Event Store
description: Lär dig mer om Azure Service Fabric's EventStore, ett sätt att förstå och övervaka tillståndet för ett kluster eller arbetsbelastningar när som helst.
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: d23c8114bf10ef3225775accef6910c0ba539e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645743"
---
# <a name="eventstore-overview"></a>Översikt över EventStore

>[!NOTE]
>Från och med Service Fabric version 6.4. EventStore-API:erna är endast tillgängliga för Windows-kluster som bara körs på Azure. Vi arbetar med att portera den här funktionen till Linux samt våra fristående kluster.

## <a name="overview"></a>Översikt

EventStore-tjänsten introducerades i version 6.2 och är ett övervakningsalternativ i Service Fabric. EventStore är ett sätt att förstå tillståndet för klustret eller arbetsbelastningarna vid en viss tidpunkt.
EventStore är en tillståndskänslig Service Fabric-tjänst som underhåller händelser från klustret. Händelsen exponeras via Service Fabric Explorer, REST och API:er. EventStore frågar klustret direkt för att hämta diagnostikdata på alla entiteter i klustret och bör användas för att hjälpa:

* Diagnostisera problem vid utveckling eller testning, eller där du kanske använder en övervakningspipeline
* Bekräfta att hanteringsåtgärder som du vidtar i klustret bearbetas korrekt
* Få en "ögonblicksbild" av hur Service Fabric interagerar med en viss entitet

![EventStore (På plats)](media/service-fabric-diagnostics-eventstore/eventstore.png)

Information om hur du ser en fullständig lista över händelser som är tillgängliga i EventStore finns i [Service Fabric-händelser](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Från och med Service Fabric version 6.4. EventStore API:er och UX är allmänt tillgängliga för Azure Windows-kluster. Vi arbetar med att portera den här funktionen till Linux samt våra fristående kluster.

EventStore-tjänsten kan efterfrågas för händelser som är tillgängliga för varje entitets- och entitetstyp i klustret. Det innebär att du kan fråga efter händelser på följande nivåer:
* Kluster: händelser som är specifika för själva klustret (t.ex. klusteruppgradering)
* Noder: alla nodnivåhändelser
* Nod: händelser som är specifika för en nod, identifierade av`nodeName`
* Ansökningar: alla händelser på programnivå
* Tillämpning: händelser som är specifika för en ansökan som identifierats av`applicationId`
* Tjänster: händelser från alla tjänster i klustren
* Service: händelser från en viss tjänst som identifierats av`serviceId`
* Partitioner: händelser från alla partitioner
* Partition: händelser från en specifik partition som identifierats av`partitionId`
* Partitionsrepliker: händelser från alla repliker/instanser inom en specifik partition som identifieras av`partitionId`
* Partition Replica: händelser från en specifik `replicaId` replik / instans som identifierats av och`partitionId`

Om du vill veta mer om API:et finns i [EventStore API-referensen](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

EventStore-tjänsten har också möjlighet att korrelera händelser i klustret. Genom att titta på händelser som har skrivits samtidigt från olika entiteter som kan ha påverkat varandra kan EventStore-tjänsten länka dessa händelser för att hjälpa till med att identifiera orsaker till aktiviteter i klustret. Om ett av dina program till exempel råkar bli ohälsosamt utan några inducerade ändringar kommer EventStore också `Error` att `Warning` titta på andra händelser som exponeras av plattformen och kan korrelera detta med en händelse. Detta hjälper till med snabbare felidentifiering och rotorsaksanalys.

## <a name="enable-eventstore-on-your-cluster"></a>Aktivera EventStore i klustret

### <a name="local-cluster"></a>Lokalt kluster

I [fabricSettings.json i klustret](service-fabric-cluster-fabric-settings.md)lägger du till EventStoreService som en addOn-funktion och utför en klusteruppgradering.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure-klusterversion 6.5+
Om ditt Azure-kluster uppgraderas till version 6.5 eller senare aktiveras EventStore automatiskt i klustret. Om du vill välja bort det måste du uppdatera klustermallen med följande:

* Använda en API-version av `2019-03-01` eller nyare 
* Lägga till följande kod i egenskapsavsnittet i klustret
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Azure-klusterversion 6.4

Om du använder version 6.4 kan du redigera mallen Azure Resource Manager för att aktivera EventStore-tjänsten. Detta görs genom att utföra en [klusterkonfigurationsuppgradering](service-fabric-cluster-config-upgrade-azure.md) och lägga till följande kod, du kan använda PlacementConstraints för att placera replikerna för EventStore-tjänsten på en specifik NodeType t.ex. Avsnittet `upgradeDescription` konfigurerar konfigurationsuppgraderingen för att utlösa en omstart på noderna. Du kan ta bort avsnittet i en annan uppdatering.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Nästa steg
* Komma igång med EventStore-API: et – [Använda EventStore-API:erna i Azure Service Fabric-kluster](service-fabric-diagnostics-eventstore-query.md)
* Läs mer om listan över händelser som erbjuds av EventStore - [Service Fabric-evenemang](service-fabric-diagnostics-event-generation-operational.md)
* Översikt över övervakning och diagnostik inom Service Fabric - [Övervakning och diagnostik för Service Fabric](service-fabric-diagnostics-overview.md)
* Visa hela listan över API-anrop - [EVENTStore REST API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Läs mer om övervakning av klustret – [Övervakning av klustret och plattformen](service-fabric-diagnostics-event-generation-infra.md).
