---
title: Aktörsdiagnostik och övervakning
description: I den här artikeln beskrivs diagnostik- och prestandaövervakningsfunktionerna i service fabric Reliable Actors-körningen, inklusive de händelser och prestandaräknare som den avger.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: e6e9fb66368461e0d3ebdd2709f4ced0e796bea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282333"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostik- och prestandaövervakning för Reliable Actors
Körningen Tillförlitliga aktörer avger [EventSource-händelser](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) och [prestandaräknare](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Dessa ger insikter om hur körningen fungerar och hjälper till med felsökning och prestandaövervakning.

## <a name="eventsource-events"></a>EventSource-händelser
EventSource-providernamnet för körningen Reliable Actors är "Microsoft-ServiceFabric-Actors". Händelser från den här händelsekällan visas i fönstret [Diagnostikhändelser](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) när aktörsprogrammet [avsöks i Visual Studio](service-fabric-debugging-your-application.md).

Exempel på verktyg och tekniker som hjälper till att samla in och/eller visa EventSource-händelser är [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Azure Diagnostics,](../cloud-services/cloud-services-dotnet-diagnostics.md) [Semantic Logging](https://msdn.microsoft.com/library/dn774980.aspx)och [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Nyckelord
Alla händelser som tillhör Reliable Actors EventSource associeras med ett eller flera nyckelord. Detta möjliggör filtrering av händelser som samlas in. Följande nyckelordsbitar definieras.

| Bitars | Beskrivning |
| --- | --- |
| 0x1 (0x1) |Uppsättning viktiga händelser som sammanfattar driften av Fabric Actors runtime. |
| 0x2 |Uppsättning händelser som beskriver aktörsmetodanrop. För mer information, se [det inledande ämnet om aktörer](service-fabric-reliable-actors-introduction.md). |
| 0x4 (0x4) |Uppsättning händelser relaterade till aktörstillstånd. Mer information finns i avsnittet om [hantering av aktörstillstånd](service-fabric-reliable-actors-state-management.md). |
| 0x8 (0x8) |Uppsättning händelser relaterade till tur-baserade samtidighet i aktören. Mer information finns i avsnittet om [samtidighet](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Prestandaräknare
Körningen Tillförlitliga aktörer definierar följande prestandaräknarekategorier.

| Kategori | Beskrivning |
| --- | --- |
| Service Fabric Skådespelare |Räknare som är specifika för Azure Service Fabric-aktörer, t.ex. |
| Metod för skådespelarmetod för service fabric |Räknare som är specifika för metoder som implementerats av Service Fabric-aktörer, t.ex. |

Var och en av ovanstående kategorier har en eller flera räknare.

Windows [Performance Monitor-programmet](https://technet.microsoft.com/library/cc749249.aspx) som är tillgängligt som standard i Operativsystemet Windows kan användas för att samla in och visa prestandaräkna data. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in prestandaräknardata och överföra dem till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Namn på prestandaräknareinstans
Ett kluster som har ett stort antal aktörstjänster eller aktörstjänstpartitioner har ett stort antal förekomster av aktörsprestandaräknare. Namn på prestandaräknareinstans kan hjälpa dig att identifiera den specifika [partitions-](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) och aktörsmetod (om tillämpligt) som prestandaräkringsinstansen är associerad med.

#### <a name="service-fabric-actor-category"></a>Kategori Service Fabric Actor
För kategorin `Service Fabric Actor`är räknarinstansnamnen i följande format:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* är strängrepresentationen av partitions-ID för Tjänst fabric som prestandaräkringsinstansen är associerad med. Partitions-ID är ett GUID och dess [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) strängrepresentation genereras via metoden med formatspecificeraren "D".

*ActorRuntimeInternalID* är strängrepresentationen av ett 64-bitars heltal som genereras av Fabric Actors-körningen för internt bruk. Detta ingår i prestandaräknarens instansnamn för att säkerställa dess unika och undvika konflikt med andra namn på prestandaräknareinstans. Användare bör inte försöka tolka den här delen av prestandaräknarens förekomstnamn.

Följande är ett exempel på ett räknareinstansnamn `Service Fabric Actor` för en räknare som tillhör kategorin:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

I exemplet ovan `2740af29-78aa-44bc-a20b-7e60fb783264` är strängrepresentationen av partitions-ID för Service Fabric och `635650083799324046` det 64-bitars-ID som genereras för körningens interna användning.

#### <a name="service-fabric-actor-method-category"></a>Kategori för service fabric-aktörsmetod
För kategorin `Service Fabric Actor Method`är räknarinstansnamnen i följande format:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* är namnet på den aktörsmetod som prestandaräknareinstansen är associerad med. Formatet på metodnamnet bestäms baserat på viss logik i fabric actors-körningen som balanserar namnets läsbarhet med begränsningar för den maximala längden på prestandaräknareinstansnamnen i Windows.

*ActorsRuntimeMethodId* är strängrepresentationen av ett 32-bitars heltal som genereras av Fabric Actors-körningen för internt bruk. Detta ingår i prestandaräknarens instansnamn för att säkerställa dess unika och undvika konflikt med andra namn på prestandaräknareinstans. Användare bör inte försöka tolka den här delen av prestandaräknarens förekomstnamn.

*ServiceFabricPartitionID* är strängrepresentationen av partitions-ID för Tjänst fabric som prestandaräkringsinstansen är associerad med. Partitions-ID är ett GUID och dess [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) strängrepresentation genereras via metoden med formatspecificeraren "D".

*ActorRuntimeInternalID* är strängrepresentationen av ett 64-bitars heltal som genereras av Fabric Actors-körningen för internt bruk. Detta ingår i prestandaräknarens instansnamn för att säkerställa dess unika och undvika konflikt med andra namn på prestandaräknareinstans. Användare bör inte försöka tolka den här delen av prestandaräknarens förekomstnamn.

Följande är ett exempel på ett räknareinstansnamn `Service Fabric Actor Method` för en räknare som tillhör kategorin:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

I exemplet `ivoicemailboxactor.leavemessageasync` ovan, är metodnamnet, `2` är 32-bitars-ID som genereras `89383d32-e57e-4a9b-a6ad-57c6792aa521` för körningens interna användning, är `635650083804480486` strängrepresentationen av PartitionS-ID för Service Fabric och är 64-bitars-ID som genereras för körningens interna användning.

## <a name="list-of-events-and-performance-counters"></a>Lista över händelser och prestandaräknare
### <a name="actor-method-events-and-performance-counters"></a>Aktörsmetodhändelser och prestandaräknare
Körningen Tillförlitliga aktörer avger följande händelser relaterade till [aktörsmetoder](service-fabric-reliable-actors-introduction.md).

| Händelsenamn | Händelse-ID | Nivå | Sökord | Beskrivning |
| --- | --- | --- | --- | --- |
| SkådespelareMethodStart |7 |Verbose |0x2 |Skådespelarna runtime är på väg att anropa en aktörsmetod. |
| SkådespelareMethodStop |8 |Verbose |0x2 |En aktörsmetod har körts klart. Det vill än så har körningens asynkrona anrop till aktörsmetoden returnerats och uppgiften som returneras av aktörsmetoden har slutförts. |
| SkådespelareMethodThrewException |9 |Varning |0x3 (0x3) |Ett undantag skapades under körningen av en aktörsmetod, antingen under körningens asynkrona anrop till aktörsmetoden eller under körningen av aktiviteten som returnerades med aktörsmetoden. Den här händelsen indikerar någon form av fel i aktörskoden som behöver undersökas. |

Körningen Tillförlitliga aktörer publicerar följande prestandaräknare relaterade till körningen av aktörsmetoder.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Metod för skådespelarmetod för service fabric |Åkallor/sek |Antal gånger som aktörstjänsten anropas per sekund |
| Metod för skådespelarmetod för service fabric |Genomsnittliga millisekunder per anrop |Tid det tar att utföra aktörstjänstmetoden i millisekunder |
| Metod för skådespelarmetod för service fabric |Undantag som har genererats/Sek |Antal gånger som aktörstjänstens metod har kastat ett undantag per sekund |

### <a name="concurrency-events-and-performance-counters"></a>Samtidighetshändelser och prestandaräknare
Körningen Tillförlitliga aktörer avger följande händelser relaterade till [samtidighet](service-fabric-reliable-actors-introduction.md#concurrency).

| Händelsenamn | Händelse-ID | Nivå | Sökord | Beskrivning |
| --- | --- | --- | --- | --- |
| SkådespelareMethodCallsWaitingForLock |12 |Verbose |0x8 (0x8) |Denna händelse är skriven i början av varje ny tur i en skådespelare. Den innehåller antalet väntande aktörsanrop som väntar på att få låset per aktör som upprätthåller turbaserad samtidighet. |

Körningen Tillförlitliga aktörer publicerar följande prestandaräknare relaterade till samtidighet.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric Skådespelare |# av skådespelaren samtal väntar på skådespelare lås |Antal väntande aktörssamtal som väntar på att få låset per aktör som framtvingar turbaserad samtidighet |
| Service Fabric Skådespelare |Genomsnittliga millisekunder per lås vänta |Tid det tar (i millisekunder) att förvärva det lås per aktör som framtvingar turbaserad samtidighet |
| Service Fabric Skådespelare |Genomsnittlig millisekunder skådespelare lås hålls |Tid (i millisekunder) för vilken lyset per aktör hålls |

### <a name="actor-state-management-events-and-performance-counters"></a>Aktörstillståndshanteringshändelser och prestandaräknare
Den tillförlitliga aktörer runtime avger följande händelser relaterade till [aktör state management](service-fabric-reliable-actors-state-management.md).

| Händelsenamn | Händelse-ID | Nivå | Sökord | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verbose |0x4 (0x4) |Aktörer runtime är på väg att rädda aktören staten. |
| ActorSaveStateStop |11 |Verbose |0x4 (0x4) |Skådespelarna har sparat igen aktörstillståndet. |

Körningen Tillförlitliga aktörer publicerar följande prestandaräknare relaterade till hantering av aktörstillstånd.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric Skådespelare |Genomsnittlig millisekunder per spara tillstånd operation |Tid tar att spara aktör tillstånd i millisekunder |
| Service Fabric Skådespelare |Genomsnittlig millisekunder per belastningslägesåtgärd |Tid det tar att ladda aktörstillstånd i millisekunder |

### <a name="events-related-to-actor-replicas"></a>Händelser relaterade till aktörsrepliker
Körningen Tillförlitliga aktörer avger följande händelser relaterade till [aktörsrepliker](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Händelsenamn | Händelse-ID | Nivå | Sökord | Beskrivning |
| --- | --- | --- | --- | --- |
| ReplikChangeRoleToPrimary |1 |Information |0x1 (0x1) |Aktörsrepliken har ändrat rollen till Primär. Detta innebär att aktörerna för den här partitionen kommer att skapas i den här repliken. |
| ReplicaChangeRoleFrånPrimary |2 |Information |0x1 (0x1) |Aktörsrepliken ändrade rollen till icke-Primär. Detta innebär att aktörerna för den här partitionen inte längre kommer att skapas i den här repliken. Inga nya begäranden kommer att levereras till aktörer som redan har skapats i den här repliken. Aktörerna kommer att förstöras efter att alla pågående förfrågningar har slutförts. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktörsaktiverings- och avaktiveringshändelser och prestandaräknare
Körningen Tillförlitliga aktörer avger följande händelser relaterade till [aktivering och avaktivering av aktörer](service-fabric-reliable-actors-lifecycle.md).

| Händelsenamn | Händelse-ID | Nivå | Sökord | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorAktiverad |5 |Information |0x1 (0x1) |En skådespelare har aktiverats. |
| ActorDeactivated |6 |Information |0x1 (0x1) |En aktör har inaktiverats. |

Körningen Tillförlitliga aktörer publicerar följande prestandaräknare relaterade till aktörsaktivering och inaktivering.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric Skådespelare |Genomsnitt påaktiveraA-millisekunder |Tid det tar att köra OnActivateAsync-metoden i millisekunder |

### <a name="actor-request-processing-performance-counters"></a>Prestandaräknare för hantering av aktörsbegäran
När en klient anropar en metod via ett aktörsproxyobjekt resulterar det i att ett begärandemeddelande skickas över nätverket till aktörstjänsten. Tjänsten bearbetar meddelandet för begäran och skickar ett svar tillbaka till klienten. Körningen Tillförlitliga aktörer publicerar följande prestandaräknare relaterade till bearbetning av aktörsbegäran.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric Skådespelare |Antal utestående förfrågningar |Antal begäranden som behandlas i tjänsten |
| Service Fabric Skådespelare |Genomsnittliga millisekunder per begäran |Tid (i millisekunder) av tjänsten för att behandla en begäran |
| Service Fabric Skådespelare |Genomsnittliga millisekunder för deserialisering av begäran |Tid (i millisekunder) för att avserialisera meddelande om aktörsbegäran när det tas emot vid tjänsten |
| Service Fabric Skådespelare |Genomsnittliga millisekunder för svarsserier |Tid det tar (i millisekunder) att serialisera aktörssvarsmeddelandet vid tjänsten innan svaret skickas till klienten |

## <a name="next-steps"></a>Nästa steg
* [Hur reliable actors använder plattformen Service Fabric](service-fabric-reliable-actors-platform.md)
* [Dokumentation för aktörs-API-referens](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [EventSource-leverantörer i PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
