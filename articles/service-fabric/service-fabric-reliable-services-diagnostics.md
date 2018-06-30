---
title: Azure Service Fabric tillståndskänsliga Reliable Services diagnostik | Microsoft Docs
description: Diagnostikfunktion för tillståndskänsliga Reliable Services i Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2018
ms.author: dekapur
ms.openlocfilehash: 51895731efd466a314877e963a5fd2c6d868ec02
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110880"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnostisk funktionalitet för tillståndskänsliga Reliable Services
Klassen Azure Service Fabric Stateful Reliable Services StatefulServiceBase avger [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) händelser som kan användas vid felsökning av tjänsten, som ger insikter om hur körningsmiljön drift och underlätta felsökningen.

## <a name="eventsource-events"></a>EventSource händelser
EventSource namnet för klassen Stateful Reliable Services StatefulServiceBase är ”Microsoft-ServiceFabric-tjänster”. Händelser från den här händelsekällan visas i den [diagnostikhändelser](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) fönstret när tjänsten används [felsökas i Visual Studio](service-fabric-debugging-your-application.md).

Exempel på Verktyg och tekniker som hjälper till att samla in och/eller visa EventSource händelser är [förhandsgranskning](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), och [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Händelser
| Händelsenamn | Händelse-ID | Nivå | Händelsebeskrivning |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Information |Orsakat när aktiviteten RunAsync tjänst har startats |
| StatefulRunAsyncCancellation |2 |Information |Orsakat när tjänsten RunAsync uppgiften har avbrutits |
| StatefulRunAsyncCompletion |3 |Information |Orsakat när tjänsten RunAsync uppgiften har slutförts |
| StatefulRunAsyncSlowCancellation |4 |Varning |Orsakat när aktiviteten tjänst RunAsync tar för lång tid att slutföra annullering |
| StatefulRunAsyncFailure |5 |Fel |Orsakat när aktiviteten tjänst RunAsync genererar ett undantag |

## <a name="interpret-events"></a>Tolka händelser
StatefulRunAsyncInvocation och StatefulRunAsyncCompletion StatefulRunAsyncCancellation händelser är användbara för service-skrivaren för att förstå livscykeln för en tjänst, samt tiden för när en tjänst startar, avbryter eller har slutförts. Den här informationen kan vara användbar vid felsökning av problem med tjänsten eller förstå livscykeln för tjänsten.

Tjänsten skrivare bör uppmärksam på StatefulRunAsyncSlowCancellation och StatefulRunAsyncFailure händelser eftersom de visar på problem med tjänsten.

StatefulRunAsyncFailure genereras när aktiviteten tjänst RunAsync() genererar ett undantag. Normalt anger ett undantag uppstod ett fel eller ett fel i tjänsten. Dessutom gör undantaget tjänsten misslyckas, så flyttas den till en annan nod. Den här åtgärden kan vara dyrt och kan fördröja inkommande begäranden när tjänsten flyttas. Tjänsten skrivare bör ta reda på orsaken för undantaget och minimera om möjligt den.

StatefulRunAsyncSlowCancellation genereras när en begäran om att avbryta för RunAsync aktiviteten tar längre tid än fyra sekunder. När en tjänst tar för lång tid att slutföra annullering, påverkar möjligheten för tjänsten att startas snabbt på en annan nod. Det här scenariot kan påverka den allmänna tillgängligheten för tjänsten.

## <a name="performance-counters"></a>Prestandaräknare
Reliable Services runtime definierar följande prestandaräknarkategorier:

| Kategori | Beskrivning |
| --- | --- |
| Transaktionsreplikering i Service Fabric |Räknare som är specifika för den Azure-tjänsten Transaktionsreplikering fabric |
| Service Fabric TStore |Räknare som är specifika för Azure Service Fabric TStore |

Den Transaktionsreplikering Service fabric används av den [tillförlitliga Tillståndshanterare](service-fabric-reliable-services-reliable-collections-internals.md) att replikera transaktioner inom en given uppsättning [repliker](service-fabric-concepts-replica-lifecycle.md).

Service Fabric-TStore är en komponent som används i [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections-internals.md) för att lagra och hämta nyckel-värdepar.

Den [Windows Prestandaövervakaren](https://technet.microsoft.com/library/cc749249.aspx) program som är tillgängligt som standard i Windows-operativsystemet kan användas för att samla in och visa information om prestandaräknare. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in prestandaräknardata och överföra den till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Namn på prestandaräknarinstanser
Ett kluster med ett stort antal reliable services eller tillförlitlig tjänst partitioner har ett stort antal transaktionella replikatorn prestandaräknaren instanser. Detta är också fallet för TStore prestandaräknare, men även multiplicerat med antalet tillförlitliga ordböcker och tillförlitlig köer som används. Namn på prestandaräknarinstanser hjälper dig identifiera specifikt [partition](service-fabric-concepts-partitioning.md), service replik och tillståndsprovidern vid TStore som prestandaräknarinstans är kopplad till.

#### <a name="service-fabric-transactional-replicator-category"></a>Transaktionsreplikering Service fabric-kategori
För kategori `Service Fabric Transactional Replicator`, instansnamn räknaren finns i följande format:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* är strängrepresentation av Service Fabric partitions-ID som prestandaräknarinstans är kopplad till. Partitions-ID är ett GUID och dess strängrepresentation genereras via [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) med Formatspecificeraren ”D”.

*ServiceFabricReplicaId* är det ID som är associerade med en viss replik av en tillförlitlig tjänst. Replik-ID ingår i instansen prestandaräknarnamnet att säkerställa dess unika och undvika konflikter med andra prestandaräknaren instanser som skapats av samma partition. Mer information om repliker och deras roll i reliable services finns [här](service-fabric-concepts-replica-lifecycle.md).

Följande räknare instansnamnet är typiska för en räknare under den `Service Fabric Transactional Replicator` kategori:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

I föregående exempel `00d0126d-3e36-4d68-98da-cc4f7195d85e` är strängrepresentation av Service Fabric partitions-ID och `131652217797162571` är replik-ID.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore kategori
För kategori `Service Fabric TStore`, instansnamn räknaren finns i följande format:

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionId* är strängrepresentation av Service Fabric partitions-ID som prestandaräknarinstans är kopplad till. Partitions-ID är ett GUID och dess strängrepresentation genereras via [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) med Formatspecificeraren ”D”.

*ServiceFabricReplicaId* är det ID som är associerade med en viss replik av en tillförlitlig tjänst. Replik-ID ingår i instansen prestandaräknarnamnet att säkerställa dess unika och undvika konflikter med andra prestandaräknaren instanser som skapats av samma partition. Mer information om repliker och deras roll i reliable services finns [här](service-fabric-concepts-replica-lifecycle.md).

*ServiceFabricStateProviderId* är det ID som är associerade med en tillståndsprovidern inom en tillförlitlig tjänst. Tillstånd Provider-ID ingår i instansen prestandaräknarnamnet att skilja mellan en TStore från en annan.

*PerformanceCounterInstanceDifferentiator* är olika-ID som är associerade med en prestandaräknarinstans inom en tillståndsprovidern. Den här fördelar ingår i instansen prestandaräknarnamnet att säkerställa dess unika och undvika konflikter med andra prestandaräknaren instanser som skapats av samma tillståndsprovidern.

Följande räknare instansnamnet är typiska för en räknare under den `Service Fabric TStore` kategori:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

I föregående exempel `00d0126d-3e36-4d68-98da-cc4f7195d85e` är strängrepresentation av Service Fabric partitions-ID `131652217797162571` är replik-ID `142652217797162571` är tillstånd provider-ID och `1337` är prestandaräknaren instans fördelar.

### <a name="transactional-replicator-performance-counters"></a>Transaktionell replikatorn prestandaräknare

Körningsmiljön Reliable Services genererar följande händelser under den `Service Fabric Transactional Replicator` kategori

 Räknarens namn | Beskrivning |
| --- | --- |
| Påbörja transaktion-åtgärder/sek | Antal nya skrivtransaktioner som skapats per sekund.|
| Transaktionsåtgärder/sek | Antalet Lägg till/uppdatera och ta bort åtgärder som utförs på tillförlitliga samlingar per sekund.|
| Genomsn. Rensa svarstid (ms) | Antalet byte som replikatorn tömmer till disken transaktionella per sekund |
| Begränsade åtgärder/sek | Antal åtgärder per sekund av transaktionella replikatorn på grund av begränsningar som avvisas. |
| Genomsn. Ms/genomförande | Genomsnittlig commit latens per transaktion i millisekunder |
| Genomsn. Rensa svarstid (ms) | Genomsnittlig varaktighet för tömning diskåtgärder initieras av transaktionella replikatorn i millisekunder |

### <a name="tstore-performance-counters"></a>TStore prestandaräknare

Körningsmiljön Reliable Services genererar följande händelser under den `Service Fabric TStore` kategori

 Räknarens namn | Beskrivning |
| --- | --- |
| Objektsantal | Antalet nycklar i arkivet.|

## <a name="next-steps"></a>Nästa steg
[EventSource providrar på förhandsgranskning](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
