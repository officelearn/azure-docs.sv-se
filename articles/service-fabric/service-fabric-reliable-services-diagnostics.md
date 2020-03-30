---
title: Azure Service Fabric Stateful Reliable Services diagnostik
description: Diagnostikfunktioner för tillståndskänsliga tillförlitliga tjänster i Azure Service Fabric
author: dkkapur
ms.topic: conceptual
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 37162287e130b05dc41453c579b3a628ac878fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282268"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnostisk funktionalitet för tillståndskänsliga Reliable Services
Klassen Azure Service Fabric Stateful Reliable Services StatefulServiceBase avger [EventSource-händelser](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) som kan användas för att felsöka tjänsten, ge insikter om hur körningen fungerar och hjälp med felsökning.

## <a name="eventsource-events"></a>EventSource-händelser
EventSource-namnet för klassen Stateful Reliable Services StatefulServiceBase är "Microsoft-ServiceFabric-Services". Händelser från den här händelsekällan visas i fönstret [Diagnostikhändelser](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) när tjänsten [avsöks i Visual Studio](service-fabric-debugging-your-application.md).

Exempel på verktyg och tekniker som hjälper till att samla in och/eller visa EventSource-händelser är [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)och [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Händelser
| Händelsenamn | Händelse-ID | Nivå | Händelsebeskrivning |
| --- | --- | --- | --- |
| TillståndskänsligaRunAsyncInkall |1 |Information |Utges när tjänsten RunAsync-aktiviteten startas |
| TillståndskänsligaRunAsyncCancellation |2 |Information |Utges när tjänsten RunAsync-aktiviteten avbryts |
| Tillståndskänsliga 2010-00-2018 |3 |Information |Utges när tjänsten RunAsync-aktiviteten är klar |
| TillståndskänsligaRunasyncSlowKanacellation |4 |Varning |Ut när tjänsten RunAsync-uppgiften tar för lång tid att slutföra annulleringen |
| TillståndskänsligaRunAsyncFailure |5 |Fel |Ut när tjänsten RunAsync-aktiviteten genererar ett undantag |

## <a name="interpret-events"></a>Tolka händelser
StatefulRunAsyncInvocation, StatefulRunAsyncCompletion och StatefulRunAsyncCancellation händelser är användbara för tjänstförfattaren för att förstå livscykeln för en tjänst, samt tidpunkten för när en tjänst startar, avbryter eller avslutas. Den här informationen kan vara användbar när du felsöker tjänstproblem eller förstå tjänstens livscykel.

Service författare bör ägna stor uppmärksamhet åt StatefulRunAsyncSlowCancellation och StatefulRunAsyncFailure händelser eftersom de anger problem med tjänsten.

StatefulRunAsyncFailure avges när aktiviteten RunAsync() genererar ett undantag. Vanligtvis indikerar ett undantag som genereras ett fel eller ett fel i tjänsten. Dessutom medför undantaget att tjänsten misslyckas, så den flyttas till en annan nod. Den här åtgärden kan vara dyr och kan fördröja inkommande begäranden medan tjänsten flyttas. Service författare bör fastställa orsaken till undantaget och, om möjligt, mildra den.

StatefulRunAsyncSlowCancellation avges när en annulleringsbegäran för körningens asynkronisering tar längre tid än fyra sekunder. När en tjänst tar för lång tid att slutföra avbokningen påverkar det tjänstens förmåga att snabbt startas om på en annan nod. Det här scenariot kan påverka tjänstens övergripande tillgänglighet.

## <a name="performance-counters"></a>Prestandaräknare
Körningen Tillförlitliga tjänster definierar följande prestandaräknarekategorier:

| Kategori | Beskrivning |
| --- | --- |
| Transaktionsreplikator för servicevävnad |Räknare som är specifika för Transactiontor för Azure Service Fabric |
| Service Tyg TStore |Räknare som är specifika för Azure Service Fabric TStore |

Service Fabric Transactional Replicator används av [Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md) för att replikera transaktioner inom en viss uppsättning [repliker](service-fabric-concepts-replica-lifecycle.md).

Service Fabric TStore är en komponent som används i [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections-internals.md) för att lagra och hämta nyckelvärdespar.

Windows [Performance Monitor-programmet](https://technet.microsoft.com/library/cc749249.aspx) som är tillgängligt som standard i Operativsystemet Windows kan användas för att samla in och visa prestandaräkna data. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in prestandaräknardata och överföra dem till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Namn på prestandaräknareinstans
Ett kluster som har ett stort antal tillförlitliga tjänster eller tillförlitliga tjänstpartitioner har ett stort antal transaktionella replikatorprestandaräknarinstanser. Detta är också fallet för TStore-prestandaräknare, men multipliceras också med antalet tillförlitliga ordlistor och tillförlitliga köer som används. Namn på prestandaräknareinstans kan hjälpa dig att identifiera den specifika [partitionen,](service-fabric-concepts-partitioning.md)tjänstrepliken och tillståndsprovidern när det gäller TStore, som prestandaräkringsinstansen är associerad med.

#### <a name="service-fabric-transactional-replicator-category"></a>Kategori Transactional Replicator för service fabric
För kategorin `Service Fabric Transactional Replicator`är räknarinstansnamnen i följande format:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* är strängrepresentationen av partitions-ID för Tjänst fabric som prestandaräkringsinstansen är associerad med. Partitions-ID är ett GUID och dess [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) strängrepresentation genereras igenom med formatspecificeraren "D".

*ServiceFabricReplicaId* är ID som är associerat med en viss replik av en tillförlitlig tjänst. Replik-ID ingår i prestanda räknarinstansnamnet för att säkerställa dess unika och undvika konflikt med andra prestandaräknarinstanser som genereras av samma partition. Mer information om repliker och deras roll i tillförlitliga tjänster finns [här](service-fabric-concepts-replica-lifecycle.md).

Följande räknarinstansnamn är typiskt `Service Fabric Transactional Replicator` för en räknare under kategorin:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

I föregående exempel `00d0126d-3e36-4d68-98da-cc4f7195d85e` är strängrepresentationen av partitions-ID:t för tjänstinfrastruktur och `131652217797162571` replik-ID.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore kategori
För kategorin `Service Fabric TStore`är räknarinstansnamnen i följande format:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* är strängrepresentationen av partitions-ID för Tjänst fabric som prestandaräkringsinstansen är associerad med. Partitions-ID är ett GUID och dess [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) strängrepresentation genereras igenom med formatspecificeraren "D".

*ServiceFabricReplicaId* är ID som är associerat med en viss replik av en tillförlitlig tjänst. Replik-ID ingår i prestanda räknarinstansnamnet för att säkerställa dess unika och undvika konflikt med andra prestandaräknarinstanser som genereras av samma partition. Mer information om repliker och deras roll i tillförlitliga tjänster finns [här](service-fabric-concepts-replica-lifecycle.md).

*StateProviderId* är ID som är associerat med en tillståndsprovider inom en tillförlitlig tjänst. Tillståndsprovider-ID ingår i prestandaräknarens förekomstnamn för att skilja en TStore från en annan.

*PerformanceCounterInstanceDifferentiator* är ett särskiljande ID som är associerat med en prestandaräknareinstans inom en tillståndsprovider. Den här differentiatorn ingår i prestandaräknarens instansnamn för att säkerställa dess unika och undvika konflikt med andra prestandaräknarinstanser som genereras av samma tillståndsprovider.

*StateProviderName* är namnet som är associerat med en tillståndsprovider inom en tillförlitlig tjänst. Tillståndsproviderns namn ingår i prestandaräknarens förekomstnamn för användare att enkelt identifiera vilket tillstånd det ger.

Följande räknarinstansnamn är typiskt `Service Fabric TStore` för en räknare under kategorin:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

I föregående exempel `00d0126d-3e36-4d68-98da-cc4f7195d85e` är strängrepresentationen av partitions-ID för Service Fabric, `131652217797162571` är replik-ID, `142652217797162571` är tillståndsprovider-ID och `1337` är differentieraren för prestandaräknareinstans. `urn:MyReliableDictionary/dataStore`är namnet på den tillståndsprovider som `urn:MyReliableDictionary`lagrar data för samlingen med namnet .

### <a name="transactional-replicator-performance-counters"></a>Prestandaräknare för transaktionsrepliktor

Körningen Tillförlitliga tjänster avger följande `Service Fabric Transactional Replicator` händelser under kategorin

 Räknarens namn | Beskrivning |
| --- | --- |
| Påbörja Txn-operationer/sek | Antalet nya skrivtransaktioner som skapas per sekund.|
| Txn-operationer/sek | Antalet åtgärder för att lägga till/uppdatera/ta bort utförs på tillförlitliga samlingar per sekund.|
| Logga inspolningsbyten per sekund | Antalet byte som spolas till disken av transaktionsreplikatorn per sekund |
| Begränsade operationer/sek | Antalet åtgärder som avvisats varje sekund av transaktionsreplikatorn på grund av begränsning. |
| Genomsnittlig transaktion ms/commit | Genomsnittlig genomförandefördröjning per transaktion i millisekunder |
| Genomsnittlig färg latens (ms) | Genomsnittlig varaktighet för diskrutningsåtgärder som initierats av transaktionsreplikator i millisekunder |

### <a name="tstore-performance-counters"></a>TStore-prestandaräknare

Körningen Tillförlitliga tjänster avger följande `Service Fabric TStore` händelser under kategorin

 Räknarens namn | Beskrivning |
| --- | --- |
| Antal objekt | Antalet artiklar i butiken.|
| Diskstorlek | Den totala diskstorleken, i byte, av kontrollpunktsfiler för arkivet.|
| Skrivbyte för kontrollpunktsfil/sek | Antalet byte som skrivs per sekund för den senaste kontrollpunktsfilen.|
| Kopiera byte för disköverföring per sekund | Antalet diskbyte som läss (på den primära repliken) eller skrivits (på en sekundär replik) per sekund under en butikskopia.|

## <a name="next-steps"></a>Nästa steg
[EventSource-leverantörer i PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
