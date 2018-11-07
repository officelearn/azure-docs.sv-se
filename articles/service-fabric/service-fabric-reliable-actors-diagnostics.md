---
title: Aktörer diagnostik och övervakning | Microsoft Docs
description: Den här artikeln beskriver den diagnostik- och prestandaövervakning av funktioner i Service Fabric Reliable Actors-körning, inklusive händelser och prestandaräknare som genereras av den.
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: timlt
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 61c01e8ea3b4cbe7b5f7ab83ab35383d74df3105
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234944"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostik- och prestandaövervakning för Reliable Actors
Reliable Actors-runtime genererar [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) händelser och [prestandaräknare](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Det här ger insikt i hur körningen fungerar och hjälpa till med felsökning och övervakning av programprestanda.

## <a name="eventsource-events"></a>EventSource händelser
Providernamnet EventSource för Reliable Actors-runtime är ”Microsoft-ServiceFabric-aktörer”. Händelser från den här händelsekällan visas i den [diagnostikhändelser](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) fönstret när actor-program som [felsökas i Visual Studio](service-fabric-debugging-your-application.md).

Exempel på Verktyg och tekniker som hjälper till att samla in och/eller visa EventSource händelser är [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [semantisk loggning](https://msdn.microsoft.com/library/dn774980.aspx), och [ Microsofts bibliotek för TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Nyckelord
Alla händelser som hör till tillförlitliga aktörer EventSource är associerade med ett eller flera nyckelord. På så sätt kan du filtrera händelser som samlas in. Följande nyckelord bitar definieras.

| bitars | Beskrivning |
| --- | --- |
| 0x1 |Ange om viktiga händelser som sammanfattar driften av Fabric Actors-runtime. |
| 0x2 |Uppsättning händelser som beskriver aktören metodanrop. Mer information finns i den [inledande ämne aktörer](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Uppsättning händelser relaterade till aktörstillstånd. Mer information finns i avsnittet på [aktören tillståndshantering](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Uppsättning händelser relaterade till tur-baserade samtidighet i aktören. Mer information finns i avsnittet på [samtidighet](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Prestandaräknare
Reliable Actors-runtime definierar följande prestandaräknarkategorier.

| Kategori | Beskrivning |
| --- | --- |
| Service Fabric-aktör |Räknare som är specifika för Azure Service Fabric actors, t.ex. tid tar att spara aktörstillstånd |
| Service Fabric-aktörsmetod |Räknare specifika för metoder som införts av Service Fabric actors, t.ex. hur ofta en aktör-metoden har anropats |

Var och en av kategorierna ovan har en eller flera räknare.

Den [Windows Prestandaövervakaren](https://technet.microsoft.com/library/cc749249.aspx) program som är tillgängligt som standard i Windows-operativsystemet kan användas för att samla in och visa prestandaräknardata. [Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in data från en prestandaräknare och överföra den till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Namn på prestandaräknare instans
Ett kluster som har ett stort antal aktörstjänster eller aktören tjänstpartitioner har ett stort antal aktörsinstanser prestandaräknaren. Instansnamn på prestandaräknare kan identifiera specifikt [partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) och aktörsmetod (om tillämpligt) som prestandaräknarinstans är associerad med.

#### <a name="service-fabric-actor-category"></a>Service Fabric-aktör kategori
För kategorin `Service Fabric Actor`, instans räknarnamnen är i följande format:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* är den sträng som innehåller Service Fabric partitions-ID som prestandaräknarinstans är associerad med. Partitions-ID är ett GUID och dess strängrepresentation genereras via den [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoden med formatspecifierare ”D”.

*ActorRuntimeInternalID* är den sträng som innehåller ett 64-bitars heltal som genereras av Fabric Actors körningen för intern användning. Det ingår i instans prestandaräknarnamnet att se till att dess unikhet och undvika konflikt med andra instansnamn på prestandaräknare. Användare bör inte försöka att tolka den här delen av prestandaräknarnamnet för instansen.

Följande är ett exempel på en räknare instansnamn för en räknare som hör till den `Service Fabric Actor` kategori:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

I exemplet ovan, `2740af29-78aa-44bc-a20b-7e60fb783264` är den sträng som innehåller Service Fabric partitions-ID och `635650083799324046` är 64-bitars-ID som genereras för körningen internt använder.

#### <a name="service-fabric-actor-method-category"></a>Service Fabric-Aktörsmetod kategori
För kategorin `Service Fabric Actor Method`, instans räknarnamnen är i följande format:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* är namnet på metoden aktör som prestandaräknarinstans är associerad med. Formatet för metodnamnet bestäms baserat på logiken i Fabric Actors runtime som balanserar läsbarhet namnet med begränsningar på den maximala längden på instansnamn på prestandaräknare i Windows.

*ActorsRuntimeMethodId* är den sträng som innehåller ett 32-bitars heltal som genereras av Fabric Actors körningen för intern användning. Det ingår i instans prestandaräknarnamnet att se till att dess unikhet och undvika konflikt med andra instansnamn på prestandaräknare. Användare bör inte försöka att tolka den här delen av prestandaräknarnamnet för instansen.

*ServiceFabricPartitionID* är den sträng som innehåller Service Fabric partitions-ID som prestandaräknarinstans är associerad med. Partitions-ID är ett GUID och dess strängrepresentation genereras via den [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoden med formatspecifierare ”D”.

*ActorRuntimeInternalID* är den sträng som innehåller ett 64-bitars heltal som genereras av Fabric Actors körningen för intern användning. Det ingår i instans prestandaräknarnamnet att se till att dess unikhet och undvika konflikt med andra instansnamn på prestandaräknare. Användare bör inte försöka att tolka den här delen av prestandaräknarnamnet för instansen.

Följande är ett exempel på en räknare instansnamn för en räknare som hör till den `Service Fabric Actor Method` kategori:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

I exemplet ovan, `ivoicemailboxactor.leavemessageasync` är metodnamnet på `2` är 32-bitars-ID som genereras för en runtime internt bruk, `89383d32-e57e-4a9b-a6ad-57c6792aa521` är den sträng som innehåller Service Fabric partitions-ID och `635650083804480486` är 64-bitars-ID som genereras för den Runtime internt bruk.

## <a name="list-of-events-and-performance-counters"></a>Lista över händelser och prestandaräknare
### <a name="actor-method-events-and-performance-counters"></a>Aktören metoden händelser och prestandaräknare
Reliable Actors-runtime genererar följande händelser relaterade till [aktören metoder](service-fabric-reliable-actors-introduction.md).

| Händelsenamn | Händelse-ID | Nivå | Nyckelord | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Utförlig |0x2 |Aktörer runtime är håller på att anropa en aktörsmetod. |
| ActorMethodStop |8 |Utförlig |0x2 |En aktörsmetod har avslutats. Det vill säga den runtime asynkrona anrop till metoden aktören har returnerat och aktiviteten som returneras av-aktör-metoden har slutförts. |
| ActorMethodThrewException |9 |Varning |0x3 |Ett undantag uppstod under körningen av en aktörsmetod antingen under den runtime asynkrona anrop till metoden aktör eller under körningen av uppgiften som returneras av metoden aktör. Den här händelsen anger någon typ av fel i koden aktör som undersökas. |

Reliable Actors-runtime publicerar följande prestandaräknare som rör körning av aktör metoder.

| Kategorinamn | Namn på räknare | Beskrivning |
| --- | --- | --- |
| Service Fabric-aktörsmetod |Anrop/sek |Antal gånger som aktörstjänstmetoden anropas per sekund |
| Service Fabric-aktörsmetod |Genomsnittligt antal millisekunder per anrop |Åtgången tid i millisekunder för att köra aktörstjänstmetoden |
| Service Fabric-aktörsmetod |Undantag/sek |Antal gånger som aktörstjänstmetoden utlöste ett undantag per sekund |

### <a name="concurrency-events-and-performance-counters"></a>Samtidighet händelser och prestandaräknare
Reliable Actors-runtime genererar följande händelser relaterade till [samtidighet](service-fabric-reliable-actors-introduction.md#concurrency).

| Händelsenamn | Händelse-ID | Nivå | Nyckelord | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Utförlig |0x8 |Den här händelsen är skriven i början av varje ny aktivera i en aktör. Den innehåller antalet väntande aktörsanrop som väntar på att hämta lås per aktör som tillämpar tur-baserade samtidighet. |

Reliable Actors-runtime publicerar följande prestandaräknare som rör samtidighet.

| Kategorinamn | Namn på räknare | Beskrivning |
| --- | --- | --- |
| Service Fabric-aktör |Antal aktörsanrop som väntar på aktörslås |Antalet väntande aktörsanrop som väntar på att hämta lås per aktör som tillämpar tur-baserade samtidighet |
| Service Fabric-aktör |Genomsnittlig låsväntetid i millisekunder |Tid (i millisekunder) att hämta lås per aktör som tillämpar tur-baserade samtidighet |
| Service Fabric-aktör |Genomsnittlig tid i millisekunder för aktörslåsaktivering |Tid (i millisekunder) som hålls kvar lås per aktör |

### <a name="actor-state-management-events-and-performance-counters"></a>Aktören management händelser och prestandaräknare
Reliable Actors-runtime genererar följande händelser relaterade till [aktören tillståndshantering](service-fabric-reliable-actors-state-management.md).

| Händelsenamn | Händelse-ID | Nivå | Nyckelord | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Utförlig |0x4 |Aktörer runtime är håller på att spara aktörstillstånd. |
| ActorSaveStateStop |11 |Utförlig |0x4 |Aktörer körningen har slutförts sparar aktörstillstånd. |

Reliable Actors-runtime publicerar följande prestandaräknare som rör aktören tillståndshantering.

| Kategorinamn | Namn på räknare | Beskrivning |
| --- | --- | --- |
| Service Fabric-aktör |Genomsnittlig tid i millisekunder per Spara tillstånd-åtgärd |Åtgången tid för att spara aktörstillstånd i millisekunder |
| Service Fabric-aktör |Genomsnittlig tid i millisekunder per Läs in tillstånd-åtgärd |Åtgången tid för att läsa in aktörstillstånd i millisekunder |

### <a name="events-related-to-actor-replicas"></a>Händelser relaterade till aktörreplikeringar
Reliable Actors-runtime genererar följande händelser relaterade till [aktörreplikeringar](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Händelsenamn | Händelse-ID | Nivå | Nyckelord | Beskrivning |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Information |0x1 |Replica aktör ändrade rollen till primär. Detta innebär att aktörer för den här partitionen kommer att skapas i den här repliken. |
| ReplicaChangeRoleFromPrimary |2 |Information |0x1 |Replica aktör ändrade rollen till icke-primär. Detta innebär att aktörer för den här partitionen inte längre kommer att skapas i den här repliken. Inga nya begäranden skickas till aktörer som redan har skapat i den här repliken. Aktörer kommer att raderas när alla pågående begäranden har slutförts. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktören aktivering och inaktivering händelser och prestandaräknare
Reliable Actors-runtime genererar följande händelser relaterade till [aktören aktivering och inaktivering](service-fabric-reliable-actors-lifecycle.md).

| Händelsenamn | Händelse-ID | Nivå | Nyckelord | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Information |0x1 |En aktör har aktiverats. |
| ActorDeactivated |6 |Information |0x1 |En aktör har inaktiverats. |

Reliable Actors-runtime publicerar följande prestandaräknare som rör aktören aktivering och inaktivering.

| Kategorinamn | Namn på räknare | Beskrivning |
| --- | --- | --- |
| Service Fabric-aktör |Genomsnittlig tid i millisekunder för OnActivateAsync |Tid i millisekunder för att köra OnActivateAsync-metoden |

### <a name="actor-request-processing-performance-counters"></a>Prestandaräknare för bearbetning av begäran av aktör
När en klient anropar en metod som via en proxy-aktörobjekt, resulterar det i ett meddelande om begäran som skickas över nätverket till aktörstjänsten. Tjänsten bearbetar meddelandet med begäran och skickar tillbaka ett svar till klienten. Reliable Actors-runtime publicerar följande prestandaräknare som rör bearbetning av begäran aktör.

| Kategorinamn | Namn på räknare | Beskrivning |
| --- | --- | --- |
| Service Fabric-aktör |Antal väntande förfrågningar |Antalet begäranden som bearbetas i tjänsten |
| Service Fabric-aktör |Genomsnittlig tid i millisekunder per begäran |Tid (i millisekunder) av tjänsten kan bearbeta en begäran |
| Service Fabric-aktör |Genomsnittlig tid i millisekunder för deserialiseringsbegäran |Tid (i millisekunder) att deserialisera aktören begärandemeddelandet när den tas emot på tjänsten |
| Service Fabric-aktör |Genomsnittlig tid i millisekunder för serialiseringssvar |Tid (i millisekunder) att serialisera aktören svarsmeddelandet på tjänsten innan svaret skickas till klienten |

## <a name="next-steps"></a>Nästa steg
* [Hur Reliable Actors använder Service Fabric-plattformen](service-fabric-reliable-actors-platform.md)
* [Aktören API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [EventSource providers i PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
