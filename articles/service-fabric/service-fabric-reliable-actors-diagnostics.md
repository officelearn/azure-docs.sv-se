---
title: "Övervakning och aktörer diagnostik | Microsoft Docs"
description: "Den här artikeln beskriver diagnostik- och prestandaövervakning funktioner i Service Fabric Reliable Actors körning, inklusive händelser och prestandaräknare som sänds av den."
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: timlt
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: abhisram
ms.openlocfilehash: 1c53a6bbe0152f6f2b9666e6059af7c6d02e481f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostik- och prestandaövervakning för Reliable Actors
Körningsmiljön Reliable Actors avger [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) händelser och [prestandaräknare](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Dessa ger insikter om hur körningsmiljön fungerar och hjälp med felsökning och övervakning av programprestanda.

## <a name="eventsource-events"></a>EventSource händelser
EventSource providernamn för Reliable Actors runtime är ”Microsoft-ServiceFabric-aktörer”. Händelser från den här händelsekällan visas i den [diagnostikhändelser](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) fönstret när programmet aktören [felsökas i Visual Studio](service-fabric-debugging-your-application.md).

Exempel på Verktyg och tekniker som hjälper till att samla in och/eller visa EventSource händelser är [förhandsgranskning](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [semantiska loggning](https://msdn.microsoft.com/library/dn774980.aspx), och [ Microsofts bibliotek för TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Nyckelord
Alla händelser som hör till tillförlitliga aktörer EventSource är associerade med ett eller flera nyckelord. På så sätt kan du filtrera händelser som samlas in. Följande nyckelord bitar definieras.

| bitar | Beskrivning |
| --- | --- |
| 0x1 |En uppsättning viktiga händelser som summerar driften av körningsmiljön Fabric aktörer. |
| 0x2 |Uppsättningen av händelser som beskriver aktören metodanrop. Mer information finns i [inledande avsnittet på aktörer](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Uppsättningen av händelser relaterade till aktörstillstånd. Mer information finns i avsnittet på [aktören tillståndshantering](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Uppsättningen av händelser relaterade till bygger samtidighet i aktören. Mer information finns i avsnittet på [samtidighet](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Prestandaräknare
Körningsmiljön Reliable Actors definierar följande prestandaräknarkategorier.

| Kategori | Beskrivning |
| --- | --- |
| Service Fabric aktören |Räknare som är specifika för Azure Service Fabric aktörer, t.ex. tid att spara aktörstillstånd |
| Service Fabric-Aktörsmetod |Räknare för specifika för metoder som införts av Service Fabric aktörer, t.ex. hur ofta en aktören-metoden har anropats |

Ovanstående kategorier har en eller flera räknare.

Den [Windows Prestandaövervakaren](https://technet.microsoft.com/library/cc749249.aspx) program som är tillgängligt som standard i Windows-operativsystemet kan användas för att samla in och visa information om prestandaräknare. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in prestandaräknardata och överföra den till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Namn på prestandaräknarinstanser
Ett kluster med ett stort antal aktörstjänster eller aktören service partitioner har ett stort antal aktören prestandaräknaren instanser. Namn på prestandaräknarinstanser hjälper dig identifiera specifikt [partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) och aktörsmetod (om tillämpligt) som prestandaräknarinstans är kopplad till.

#### <a name="service-fabric-actor-category"></a>Service Fabric aktören kategori
För kategori `Service Fabric Actor`, instansnamn räknaren finns i följande format:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* är strängrepresentation av Service Fabric partitions-ID som prestandaräknarinstans är kopplad till. Partitions-ID är ett GUID och dess strängrepresentation genereras via den [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metod med Formatspecificeraren ”D”.

*ActorRuntimeInternalID* är strängrepresentation av ett 64-bitars heltal som genereras av Fabric aktörer runtime för intern användning. Detta ingår i instansen prestandaräknarnamnet att säkerställa dess unika och undvika konflikter med andra namn på prestandaräknarinstanser. Användare försöka inte tolka den här delen av prestandaräknarnamnet för instansen.

Följande är ett exempel på ett räknaren instansnamn för en räknare som tillhör den `Service Fabric Actor` kategori:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

I exemplet ovan, `2740af29-78aa-44bc-a20b-7e60fb783264` är strängrepresentation av Service Fabric partitions-ID och `635650083799324046` är 64-bitars-ID som genereras för körningsmiljön internt använder.

#### <a name="service-fabric-actor-method-category"></a>Service Fabric-Aktörsmetod kategori
För kategori `Service Fabric Actor Method`, instansnamn räknaren finns i följande format:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* är namnet på metoden aktören räknarinstansen prestanda är associerad med. Formatet för metodnamnet bestäms baserat på viss logik i Fabric aktörer körningsmiljön som balanserar läsbarhet på namn med begränsningar på den maximala längden på namn på prestandaräknarinstanser i Windows.

*ActorsRuntimeMethodId* är strängrepresentation av en 32-bitars heltal som genereras av Fabric aktörer runtime för intern användning. Detta ingår i instansen prestandaräknarnamnet att säkerställa dess unika och undvika konflikter med andra namn på prestandaräknarinstanser. Användare försöka inte tolka den här delen av prestandaräknarnamnet för instansen.

*ServiceFabricPartitionID* är strängrepresentation av Service Fabric partitions-ID som prestandaräknarinstans är kopplad till. Partitions-ID är ett GUID och dess strängrepresentation genereras via den [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metod med Formatspecificeraren ”D”.

*ActorRuntimeInternalID* är strängrepresentation av ett 64-bitars heltal som genereras av Fabric aktörer runtime för intern användning. Detta ingår i instansen prestandaräknarnamnet att säkerställa dess unika och undvika konflikter med andra namn på prestandaräknarinstanser. Användare försöka inte tolka den här delen av prestandaräknarnamnet för instansen.

Följande är ett exempel på ett räknaren instansnamn för en räknare som tillhör den `Service Fabric Actor Method` kategori:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

I exemplet ovan, `ivoicemailboxactor.leavemessageasync` är metodnamnet på `2` är 32-bitars-ID som genererades för intern användning av den runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` är strängrepresentation av Service Fabric partitions-ID och `635650083804480486` är 64-bitars-ID genereras för den Runtime intern användning.

## <a name="list-of-events-and-performance-counters"></a>Lista över händelser och prestandaräknare
### <a name="actor-method-events-and-performance-counters"></a>Aktören metoden händelser och prestandaräknare
Körningsmiljön Reliable Actors genererar följande händelser relaterade till [aktören metoder](service-fabric-reliable-actors-introduction.md).

| händelsenamnet | Händelse-ID | Nivå | Nyckelordet | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Utförlig |0x2 |Aktörer runtime håller på att anropa en aktörsmetod. |
| ActorMethodStop |8 |Utförlig |0x2 |En aktörsmetod har avslutats. Det vill säga den runtime asynkront anrop till metoden aktören har returnerat och aktiviteten som returneras av metoden aktören har slutförts. |
| ActorMethodThrewException |9 |Varning |0x3 |Ett undantag uppstod under körningen av en aktörsmetod, antingen under den runtime asynkront anrop till metoden aktören eller under körning av uppgiften som returneras av metoden aktören. Den här händelsen indikerar någon form av fel i aktören kod som behöver undersökning. |

Körningsmiljön Reliable Actors publicerar följande prestandaräknare som rör körning av aktören metoder.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric-Aktörsmetod |Anrop/sek |Antalet gånger som aktörstjänstmetoden anropas per sekund |
| Service Fabric-Aktörsmetod |Genomsnittlig tid i millisekunder per anrop |Åtgången tid i millisekunder för att köra aktörstjänstmetoden |
| Service Fabric-Aktörsmetod |Undantag/sek |Antal gånger som aktörstjänstmetoden utlöste ett undantag per sekund |

### <a name="concurrency-events-and-performance-counters"></a>Concurrency-händelser och prestandaräknare
Körningsmiljön Reliable Actors genererar följande händelser relaterade till [samtidighet](service-fabric-reliable-actors-introduction.md#concurrency).

| händelsenamnet | Händelse-ID | Nivå | Nyckelordet | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Utförlig |0x8 |Den här händelsen är skriven i början av varje ny Stäng i en aktör. Den innehåller antalet väntande aktörsanrop som väntar på att låsa per aktör, som tillämpar bygger samtidighet. |

Körningsmiljön Reliable Actors publicerar följande prestandaräknare som rör samtidighet.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric aktören |Antal aktörsanrop som väntar på aktörslås |Antalet väntande aktörsanrop som väntar på att låsa per aktör, som tillämpar bygger samtidighet |
| Service Fabric aktören |Genomsnittlig låsväntetid i millisekunder |Tid (i millisekunder) att låsa per aktör, som tillämpar bygger samtidighet |
| Service Fabric aktören |Genomsnittlig tid i millisekunder för aktörslåsaktivering |Tid (i millisekunder) som hålls lås per aktör |

### <a name="actor-state-management-events-and-performance-counters"></a>Aktören management händelser och prestandaräknare
Körningsmiljön Reliable Actors genererar följande händelser relaterade till [aktören tillståndshantering](service-fabric-reliable-actors-state-management.md).

| händelsenamnet | Händelse-ID | Nivå | Nyckelordet | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Utförlig |0x4 |Aktörer runtime är att spara tillståndet aktören. |
| ActorSaveStateStop |11 |Utförlig |0x4 |Aktörer runtime är klar sparar aktörstillstånd. |

Körningsmiljön Reliable Actors publicerar följande prestandaräknare relaterat till hantering av aktören tillstånd.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric aktören |Genomsnittlig tid i millisekunder per Spara tillstånd-åtgärd |Åtgången tid för att spara aktörstillstånd i millisekunder |
| Service Fabric aktören |Genomsnittlig tid i millisekunder per Läs in tillstånd-åtgärd |Åtgången tid för att läsa in aktörstillstånd i millisekunder |

### <a name="events-related-to-actor-replicas"></a>Händelser relaterade till aktören repliker
Körningsmiljön Reliable Actors genererar följande händelser relaterade till [aktören repliker](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| händelsenamnet | Händelse-ID | Nivå | Nyckelordet | Beskrivning |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Information |0x1 |Aktören replik ändrade rollen till primär. Detta innebär att aktörer för den här partitionen kommer att skapas i den här repliken. |
| ReplicaChangeRoleFromPrimary |2 |Information |0x1 |Aktören replik ändrade rollen till icke-primär. Detta innebär att aktörer för den här partitionen inte längre kommer att skapas i den här repliken. Inga nya begäranden skickas till aktörer som redan har skapats i den här repliken. Aktörer kommer att raderas när alla pågående begäranden har slutförts. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktören aktivering och inaktivering av händelser och prestandaräknare
Körningsmiljön Reliable Actors genererar följande händelser relaterade till [aktören aktivering och inaktivering av](service-fabric-reliable-actors-lifecycle.md).

| händelsenamnet | Händelse-ID | Nivå | Nyckelordet | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Information |0x1 |En aktör har aktiverats. |
| ActorDeactivated |6 |Information |0x1 |En aktör har inaktiverats. |

Körningsmiljön Reliable Actors publicerar följande prestandaräknare som rör aktören aktivering och inaktivering.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric aktören |Genomsnittlig tid i millisekunder OnActivateAsync |Tid i millisekunder för att köra OnActivateAsync-metoden |

### <a name="actor-request-processing-performance-counters"></a>Aktören begäranbearbetningen prestandaräknare
När en klient anropar en metod via ett aktören proxy-objekt, resulterar det i ett meddelande om begäran som skickas över nätverket till tjänsten aktören. Tjänsten bearbetar meddelandet med begäran och skickar tillbaka ett svar till klienten. Körningsmiljön Reliable Actors publicerar följande Prestandaräknare relaterade till behandling av begäranden aktören.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric aktören |Antal väntande förfrågningar |Antalet begäranden som bearbetas i tjänsten |
| Service Fabric aktören |Genomsnittlig tid i millisekunder per begäran |Tid (i millisekunder av tjänsten för att bearbeta en begäran) |
| Service Fabric aktören |Genomsnittlig tid i millisekunder för deserialiseringsbegäran |Tid (i millisekunder) att avserialisera begärandemeddelandet aktören när den tas emot med tjänsten |
| Service Fabric aktören |Genomsnittlig tid i millisekunder för serialiseringssvar |Tid (i millisekunder) att serialisera svarsmeddelandet aktören på tjänsten innan svaret skickas till klienten |

## <a name="next-steps"></a>Nästa steg
* [Hur Reliable Actors använda Service Fabric-plattformen](service-fabric-reliable-actors-platform.md)
* [Aktören API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exempelkod](https://github.com/Azure/servicefabric-samples)
* [EventSource providrar på förhandsgranskning](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
