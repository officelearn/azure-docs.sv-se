---
title: Azure Cosmos DB .NET ändra feed processor API, SDK viktig information
description: Lär dig allt om API:et för ändringsflödesbehandlare och SDK, inklusive utgivningsdatum, pensioneringsdatum och ändringar som gjorts mellan varje version av .NET Change Feed Processor SDK.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 9252e3e41d0c639231a2abe20202499c6b3ee32a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444863"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Ändra Feed Processor SDK: Ladda ner och viktig information

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Resten](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulkutnrutören - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK nedladdning**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-dokumentation**|[Ändra API-referensdokumentation för feed processorbibliotek](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Kom igång**|[Komma igång med ändringsmatningsprocessorn .NET SDK](change-feed.md)|
|**Nuvarande ramverk som stöds**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET-kärna](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Om du använder ändringsflödesprocessorn kan du se den senaste versionen 3.x av [.NET SDK](change-feed-processor.md), som har ändrat feed inbyggt i SDK. 

## <a name="release-notes"></a>Viktig information

### <a name="v2-builds"></a>v2 bygger

### <a name="228"></a><a name="2.2.8"/>2.2.8
* Förbättringar av stabilitet och diagnositet:
  * Lagt till stöd för att upptäcka läsändring foder tar lång tid. När det tar längre tid än `ChangeFeedProcessorOptions.ChangeFeedTimeout` det värde som anges av egenskapen vidtas följande åtgärder:
    * Åtgärden för att läsa ändringsflödet på den problematiska partitionen avbryts.
    * Ändringsfeedprocessorinstansen minskar äganderätten till det problematiska lånet. Det bortsläppta lånet kommer att plockas upp under nästa lånehämtningssteg som kommer att göras av samma eller olika ändringsflödesprocessorinstans. På så sätt börjar läsändringsflödet om.
    * Ett problem rapporteras till hälsoövervakaren. Standardheden övervakare skickar alla rapporterade problem till spårningsloggen.
  * Lade till en `ChangeFeedProcessorOptions.ChangeFeedTimeout`ny offentlig egendom: . Standardvärdet för den här egenskapen är 10 minuter.
  * Lade till ett nytt `Monitoring.MonitoredOperation.ReadChangeFeed`offentligt uppräkningsvärde: . När värdet `HealthMonitoringRecord.Operation` för är `Monitoring.MonitoredOperation.ReadChangeFeed`inställt på anger det att hälsoproblemet är relaterat till läsändringsflödet.

### <a name="227"></a><a name="2.2.7"/>2.2.7
* Förbättrad lastutjämningsstrategi för scenario när det tar längre tid att få alla leasingavtal än lånets utgångsintervall, t.ex.
  * I det här scenariot belastningsutjämning algoritm som används för att felaktigt betrakta lån som löpt ut, vilket leder till att stjäla lån från aktiva ägare. Detta kan utlösa onödig ombalansering en hel del leasingavtal.
  * Det här problemet åtgärdas i den här versionen genom att undvika återförsök i konflikt samtidigt som du skaffar utgånget lån som ägaren inte har ändrats och posponing förvärva utgångna lån till nästa belastningsutjämning iteration.

### <a name="226"></a><a name="2.2.6"/>2.2.6
* Förbättrad hantering av Observer undantag.
* Rikare information om Observer fel:
  * När en observatör stängs på grund av ett undantag som genereras av Observers ProcessChangesAsync får CloseAsync nu orsaksparametern som anges till ChangeFeedObserverCloseReason.ObserverError.
  * Lade till spårningar för att identifiera fel i användarkoden i en Observatör.

### <a name="225"></a><a name="2.2.5"/>2.2.5
* Lagt till stöd för hantering av delning i samlingar som använder delat databasdataflöde.
  * Den här versionen åtgärdar ett problem som kan uppstå under delning i samlingar med hjälp av delat databasdataflöde när delat resultat i partitionen balanserar om med endast ett underordnat partitionsnyckelintervall som skapats i stället för två. När detta inträffar kan Ändringsflödesprocessorn fastna när du tar bort lånet för det gamla partitionsnyckelintervallet och inte skapar nya lån. Problemet åtgärdas i den här versionen.

### <a name="224"></a><a name="2.2.4"/>2.2.4
* Lade till den nya egenskapen ChangeFeedProcessorOptions.StartContinuation för att stödja start av ändringsfeed från begäranomförskottstoken. Detta används endast när lånesamlingen är tom eller om ett lån inte har continuationtoken set. För lån i lånesamling som har ContinuationToken set används ContinuationToken och ChangeFeedProcessorOptions.StartContinuation ignoreras.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Lade till stöd för att använda anpassade lagringslager för att bevara fortsättningstoken per partition.
  * Ett anpassat lånearkiv kan till exempel azure cosmos DB-lånesamling partitioneras på något anpassat sätt.
  * Anpassade lånebutiker kan använda nya utökningsbara punkt ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) och ILeaseStoreManager offentligt gränssnitt.
  * Refactored ILeaseManager-gränssnittet i flera rollgränssnitt.
* Mindre brytningsändring: borttagen utökningspunkt ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), använd ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) i stället.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Den här versionen åtgärdar ett problem som uppstår under bearbetningen av en delning i övervakad samling och med hjälp av en partitionerad lånesamling. När du bearbetar ett lån för delad partition kan det hända att lånet som motsvarar den partitionen inte tas bort. Problemet åtgärdas i den här versionen.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Fast uppskattningsberäkning för multimasterkonton och nytt sessionstokenformat.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Lade till stöd för partitionerade lånesamlingar. Partitionsnyckeln måste definieras som /id.
* Mindre brytningsändring: metoderna för gränssnittet IChangeFeedDocumentClient och klassen ChangeFeedDocumentClient har ändrats till att omfatta parametrar för RequestOptions och CancellationToken. IChangeFeedDocumentClient är en avancerad utökningspunkt som gör att du kan tillhandahålla anpassad implementering av dokumentklienten som ska användas med Ändringsflödesprocessor, t.ex. Etc. Med den här uppdateringen måste koden som implementerar IChangeFeedDocumentClient ändras för att inkludera nya parametrar i implementeringen.
* Mindre förbättringar av diagnostiken.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Lade till nytt&lt;API, Task&lt;IReadOnlyList RemainingPartitionWork&gt; &gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Detta kan användas för att få uppskattat arbete för varje partition.
* Stöder Microsoft.Azure.DocumentDB SDK 2.0. Kräver Microsoft.Azure.DocumentDB 2.0 eller senare.

### <a name="206"></a><a name="2.0.6"/>2.0.6
* Lade till changefeedeventhost.hostname offentlig egendom för kompatibilitet med v1.

### <a name="205"></a><a name="2.0.5"/>2.0.5
* Fixade ett konkurrenstillstånd som uppstår under partitionsdelning. Tävlingen villkor kan leda till att förvärva leasing och omedelbart förlora den under partition split och orsakar påstående. Problemet med rastillståndet är åtgärdat med den här versionen.

### <a name="204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* Åtgärdat följande problem:
  * När partitionsdelning inträffar kan det finnas dubblettbearbetning av dokument som ändrats före delningen.
  * GetEstimatedRemainingWork API returnerade 0 när inga lån fanns i lånesamlingen.

* Följande undantag offentliggörs. Tillägg som implementerar IPartitionProcessor kan generera dessa undantag.
  * Microsoft.Azure.documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.documents.changeFeedProcessor.exceptions.partitionException. 
  * Microsoft.Azure.documents.changeFeedProcessor.exceptions.partitionNotFoundException.
  * Microsoft.Azure.documents.changeFeedProcessor.exceptions.partitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Mindre API-ändringar:
  * Tog bort ChangeFeedProcessorOptions.IsAutoCheckpointEnabled som har markerats som föråldrad.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Förbättringar av stabilitet:
  * Bättre hantering av leasingbutiksinitisering. När lånearkivet är tomt kan bara en instans av processorn initiera det, medan de andra väntar.
  * Stabilare/effektivare förnyelse/frisättning av leasing. Förnya och släppa ett lån en partition är oberoende av att förnya andra. I v1 som gjordes sekventiellt för alla partitioner.
* Nytt v2-API:
  * Byggmönster för flexibel konstruktion av processorn: klassen ChangeFeedProcessorBuilder.
    * Kan ta någon kombination av parametrar.
    * Kan ta DocumentClient-instans för övervakning och/eller lånesamling (inte tillgängligt i v1).
  * IChangeFeedObserver.ProcessChangesAsync tar nu CancellationToken.
  * IRemainingWorkEstimator - den återstående arbetsuppskattningsanordningen kan användas separat från processorn.
  * Nya utökningspunkter:
    * IPartitionLoadBalancingStrategy - för anpassad belastningsutjämning av partitioner mellan instanser av processorn.
    * ILease, ILeaseManager - för anpassad leasinghantering.
    * IPartitionProcessor - för anpassade bearbetningsändringar på en partition.
* Loggning - använder [LibLog-bibliotek.](https://github.com/damianh/LibLog)
* 100% bakåtkompatibel med v1 API.
* Ny kodbas.
* Kompatibel med [SQL .NET SDK-versioner](sql-api-sdk-dotnet.md) 1.21.1 och senare.

### <a name="v1-builds"></a>v1 bygger

### <a name="133"></a><a name="1.3.3"/>1.3.3
* Lade till mer loggning.
* Fixade en DocumentClient-läcka när du anropade den väntande arbetsuppskattningen flera gånger.

### <a name="132"></a><a name="1.3.2"/>1.3.2
* Fixar i den väntande arbetsuppskattningen.

### <a name="131"></a><a name="1.3.1"/>1.3.1
* Förbättringar av stabiliteten.
  * Åtgärd för hantering av avbrutit aktivitetsproblem som kan leda till stoppade observatörer på vissa partitioner.
* Stöd för manuell kontrollpunkt.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) version 1.21 och högre.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Lägger till stöd för .NET Standard 2.0. Paketet stöder `netstandard2.0` nu `net451` och rammonikers.
* Kompatibel med [SQL .NET SDK-versioner](sql-api-sdk-dotnet.md) 1.17.0 och senare.
* Kompatibel med [SQL .NET Core SDK-versioner](sql-api-sdk-dotnet-core.md) 1.5.1 och senare.

### <a name="111"></a><a name="1.1.1"/>1.1.1
* Åtgärdar ett problem med beräkningen av uppskattningen av återstående arbete när ändringsflödet var tomt eller inget arbete väntade.
* Kompatibel med [SQL .NET SDK-versioner](sql-api-sdk-dotnet.md) 1.13.2 och senare.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Lade till en metod för att få en uppskattning av återstående arbete som ska bearbetas i ändringsflödet.
* Kompatibel med [SQL .NET SDK-versioner](sql-api-sdk-dotnet.md) 1.13.2 och senare.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Kompatibel med [SQL .NET SDK-versioner](sql-api-sdk-dotnet.md) 1.14.1 och senare.

## <a name="release--retirement-dates"></a>Släpp & pensioneringsdatum

Microsoft kommer att meddela minst **12 månader** innan du går i pension en SDK för att underlätta övergången till en nyare / stöds version.

Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK, som sådan rekommenderas att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt. 

Alla förfrågningar till Cosmos DB med hjälp av en pensionerad SDK kommer att avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Pensionering Datum |
| --- | --- | --- |
| [2.2.8](#2.2.8) |den 28 oktober 2019 |--- |
| [2.2.7](#2.2.7) |den 14 maj 2019 |--- |
| [2.2.6](#2.2.6) |den 29 januari 2019 |--- |
| [2.2.5](#2.2.5) |den 13 december 2018 |--- |
| [2.2.4](#2.2.4) |den 29 november 2018 |--- |
| [2.2.3](#2.2.3) |den 19 november 2018 |--- |
| [2.2.2](#2.2.2) |31 oktober 2018 |--- |
| [2.2.1](#2.2.1) |den 24 oktober 2018 |--- |
| [1.3.3](#1.3.3) |den 8 maj 2018 |--- |
| [1.3.2](#1.3.2) |den 18 april 2018 |--- |
| [1.3.1](#1.3.1) |13 mars 2018 |--- |
| [1.2.0](#1.2.0) |31 oktober 2017 |--- |
| [1.1.1](#1.1.1) |den 29 augusti 2017 |--- |
| [1.1.0](#1.1.0) |den 13 augusti 2017 |--- |
| [1.0.0](#1.0.0) |den 7 juli 2017 |--- |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även

Mer information om Cosmos DB finns på tjänstsidan [för Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)
