---
title: Azure Service Fabric tillståndskänslig Reliable Services-diagnostik | Microsoft Docs
description: Diagnostisk funktionalitet för tillståndskänsliga Reliable Services i Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: f9ec807f951bbaec0be3f1729ba2a7246ef37c62
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738036"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnostisk funktionalitet för tillståndskänsliga Reliable Services
Azure Service Fabric tillståndskänslig Reliable Services StatefulServiceBase klassen genererar [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) händelser som kan användas för att felsöka tjänsten, som ger insikt i hur körningen operativsystem och underlätta felsökningen.

## <a name="eventsource-events"></a>EventSource händelser
EventSource namnet för klassen tillståndskänslig Reliable Services StatefulServiceBase är ”Microsoft-Service fabric-tjänster”. Händelser från den här händelsekällan visas i den [diagnostikhändelser](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) fönstret när tjänsten håller på att [felsökas i Visual Studio](service-fabric-debugging-your-application.md).

Exempel på Verktyg och tekniker som hjälper till att samla in och/eller visa EventSource händelser är [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), och [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Händelser
| Händelsenamn | Händelse-ID | Nivå | Händelsebeskrivning |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Information |Genereras när tjänsten RunAsync uppgiften har startats |
| StatefulRunAsyncCancellation |2 |Information |Genereras när tjänsten RunAsync uppgiften avbryts |
| StatefulRunAsyncCompletion |3 |Information |Genereras när tjänsten RunAsync aktiviteten har slutförts |
| StatefulRunAsyncSlowCancellation |4 |Varning |Genereras när tjänsten RunAsync aktiviteten tar för lång tid att slutföra annullering |
| StatefulRunAsyncFailure |5 |Fel |Genereras när tjänsten RunAsync uppgiften genererar ett undantag |

## <a name="interpret-events"></a>Tolka händelser
StatefulRunAsyncInvocation och StatefulRunAsyncCompletion StatefulRunAsyncCancellation händelser är användbara för att service-skrivaren kan förstå livscykeln för en tjänst, samt tiden för när en tjänst startar, avbryter eller har slutförts. Den här informationen kan vara användbart när tjänsten felsökningsproblem eller förstå livscykeln för tjänsten.

Tjänsten skrivare bör noggrann StatefulRunAsyncSlowCancellation och StatefulRunAsyncFailure eftersom de visar på problem med tjänsten.

StatefulRunAsyncFailure genereras när tjänsten RunAsync() uppgiften genererar ett undantag. Normalt anger ett undantag uppstod ett fel eller en bugg i tjänsten. Dessutom orsakar undantaget att tjänsten misslyckas, så att den flyttas till en annan nod. Den här åtgärden kan vara dyrt och kan fördröja inkommande begäranden när tjänsten har flyttats. Skrivare för tjänsten ska ta reda på orsaken för undantaget och lösa det om det är möjligt.

StatefulRunAsyncSlowCancellation genereras när en begäran om annullering för RunAsync-aktiviteten tar längre tid än fyra sekunder. När en tjänst tar för lång tid att slutföra annullering påverkar möjligheten för tjänsten att startas snabbt på en annan nod. Det här scenariot kan påverka övergripande tillgänglighet till tjänsten.

## <a name="performance-counters"></a>Prestandaräknare
Reliable Services-runtime definierar följande prestandaräknarkategorier:

| Kategori | Beskrivning |
| --- | --- |
| Transaktionsreplikering i Service Fabric |Räknare för Transaktionsreplikering i det Azure Service Fabric |
| Service Fabric TStore |Räknare som är specifika för Azure Service Fabric TStore |

Transaktionsreplikering i Service Fabric används av den [Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md) att replikera transaktioner inom en viss uppsättning [repliker](service-fabric-concepts-replica-lifecycle.md).

Service Fabric TStore är en komponent som används i [Reliable Collections](service-fabric-reliable-services-reliable-collections-internals.md) för lagring och hämtning av nyckel / värde-par.

Den [Windows Prestandaövervakaren](https://technet.microsoft.com/library/cc749249.aspx) program som är tillgängligt som standard i Windows-operativsystemet kan användas för att samla in och visa prestandaräknardata. [Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in data från en prestandaräknare och överföra den till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Namn på prestandaräknare instans
Ett kluster som har ett stort antal tillförlitliga tjänster eller partitioner för tillförlitlig tjänst har ett stort antal instanser för Transaktionsreplikering i prestandaräknaren. Detta gäller även för TStore prestandaräknare, men även multiplicerat med antalet tillförlitlig ordlistor och tillförlitlig köer som används. Instansnamn på prestandaräknare kan identifiera specifikt [partition](service-fabric-concepts-partitioning.md), tjänsterepliken och tillståndsprovider när det gäller TStore som prestandaräknarinstans är associerad med.

#### <a name="service-fabric-transactional-replicator-category"></a>Transaktionsreplikering i Service Fabric-kategori
För kategorin `Service Fabric Transactional Replicator`, instans räknarnamnen är i följande format:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* är den sträng som innehåller Service Fabric partitions-ID som prestandaräknarinstans är associerad med. Partitions-ID är ett GUID och dess strängrepresentation genereras via [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) med formatspecifierare ”D”.

*ServiceFabricReplicaId* är det ID som är associerade med en viss replik av en tillförlitlig tjänst. Replik-ID är inkluderat i instans prestandaräknarnamnet att se till att dess unikhet och undvika konflikter med andra instanser för räknaren av prestanda som genererats av samma partition. Mer information om repliker och deras roll i reliable services finns [här](service-fabric-concepts-replica-lifecycle.md).

Följande räknare instansnamnet är typiskt för en räknare under den `Service Fabric Transactional Replicator` kategori:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

I föregående exempel `00d0126d-3e36-4d68-98da-cc4f7195d85e` är den sträng som innehåller Service Fabric partitions-ID och `131652217797162571` är replik-ID.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore kategori
För kategorin `Service Fabric TStore`, instans räknarnamnen är i följande format:

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionId* är den sträng som innehåller Service Fabric partitions-ID som prestandaräknarinstans är associerad med. Partitions-ID är ett GUID och dess strängrepresentation genereras via [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) med formatspecifierare ”D”.

*ServiceFabricReplicaId* är det ID som är associerade med en viss replik av en tillförlitlig tjänst. Replik-ID är inkluderat i instans prestandaräknarnamnet att se till att dess unikhet och undvika konflikter med andra instanser för räknaren av prestanda som genererats av samma partition. Mer information om repliker och deras roll i reliable services finns [här](service-fabric-concepts-replica-lifecycle.md).

*ServiceFabricStateProviderId* är det ID som är associerade med en tillståndsprovider inom en tillförlitlig tjänst. Tillstånds-Provider-ID är inkluderat i instans prestandaräknarnamnet skilja mellan en TStore från en annan.

*PerformanceCounterInstanceDifferentiator* är en särskiljande-ID som är associerade med en prestandaräknarinstans inom en tillståndsprovider. Den här skillnaden ingår i instans prestandaräknarnamnet att se till att dess unikhet och undvika konflikter med andra instanser för räknaren av prestanda som genererats av samma tillståndsprovider.

Följande räknare instansnamnet är typiskt för en räknare under den `Service Fabric TStore` kategori:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

I föregående exempel `00d0126d-3e36-4d68-98da-cc4f7195d85e` är den sträng som innehåller Service Fabric partitions-ID `131652217797162571` är replik-ID `142652217797162571` är tillståndet provider-ID och `1337` är prestandaräknaren instans skillnaden.

### <a name="transactional-replicator-performance-counters"></a>Transaktionell replikatorn prestandaräknare

Reliable Services-runtime genererar följande händelser under den `Service Fabric Transactional Replicator` kategori

 Namn på räknare | Beskrivning |
| --- | --- |
| Påbörja transaktion-åtgärder/sek | Antal nya skrivtransaktioner som skapats per sekund.|
| Transaktionsåtgärder/sek | Antalet Lägg till/Uppdatera/ta bort åtgärder som utförs på tillförlitliga samlingar per sekund.|
| Genomsn. Svarstid för tömning (ms) | Antalet byte som replikatorn tömmer till disken av Transaktionsreplikering i per sekund |
| Begränsade åtgärder/sek | Antal åtgärder per sekund av Transaktionsreplikering i på grund av begränsningar som avvisas. |
| Genomsn. Transaktionen ms/incheckning | Genomsnittlig incheckning svarstider per transaktion i millisekunder |
| Genomsn. Svarstid för tömning (ms) | Genomsnittlig varaktighet för tömning diskåtgärder initieras av Transaktionsreplikering i i millisekunder |

### <a name="tstore-performance-counters"></a>Prestandaräknare för TStore

Reliable Services-runtime genererar följande händelser under den `Service Fabric TStore` kategori

 Namn på räknare | Beskrivning |
| --- | --- |
| Objektsantal | Antal objekt i arkivet.|
| Diskstorlek | Totalt antal diskens storlek i byte på kontrollpunktsfiler för arkivet.|
| Skrivna byte/s, kontrollpunktsfil | Antal byte som skrivs per sekund för senaste kontrollpunktsfilen.|
| Överföring vid diskkopiering i byte/s | Antal byte på disk läsas (på den primära repliken) eller skrivas (på en sekundär replik) per sekund under en store-kopia.|

## <a name="next-steps"></a>Nästa steg
[EventSource providers i PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
