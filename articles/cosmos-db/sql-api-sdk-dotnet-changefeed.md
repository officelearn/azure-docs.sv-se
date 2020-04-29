---
title: Azure Cosmos DB .NET Change feed processor-API, viktig information om SDK
description: Lär dig allt om Change feeds processor-API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av .NET Change feed processor SDK.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 5820778d46f5701b82bb289192350a9e13739d37
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619448"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change feed processor SDK: Hämta och viktig information

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-ändra feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK-hämtning**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-dokumentation**|[Ändra dokumentation om API-referens för feeds-bibliotek](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Kom igång**|[Kom igång med Change feeds-processorn .NET SDK](change-feed.md)|
|**Aktuellt ramverk som stöds**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Om du använder ändra flödes processor kan du se den senaste versionen 3. x av [.NET SDK](change-feed-processor.md), som har en inbyggd ändrings-feed i SDK: n. 

## <a name="release-notes"></a>Viktig information

### <a name="v2-builds"></a>v2-versioner

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Lade till en ny `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` Metod och motsvarande offentligt `ICheckpointPartitionProcessorFactory`gränssnitt. Detta gör att en implementering av `IPartitionProcessor` gränssnittet kan använda inbyggd mekanism för kontroll punkter. Den nya fabriken liknar den befintliga `IPartitionProcessorFactory`, förutom att dess `Create` metod även använder- `ILeaseCheckpointer` parametern.
* Endast en av de två metoderna, `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` eller `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`, kan användas för samma `ChangeFeedProcessorBuilder` instans.

### <a name="228"></a><a name="2.2.8"/>2.2.8
* Förbättringar av stabilitet och diagnos:
  * Stöd har lagts till för att identifiera läsning av ändrings flöden med lång tid. När det tar längre tid än värdet som anges av `ChangeFeedProcessorOptions.ChangeFeedTimeout` egenskapen vidtas följande steg:
    * Åtgärden för att läsa ändrings flödet på den problematiska partitionen avbryts.
    * Change feed processor-instansen tappar ägarskap för det problematiska lånet. Det borttagna lånet hämtas under nästa steg för att erhålla lånet som utförs av samma eller olika processor instanser för förändrings flödet. På så sätt börjar läsning av ändrings flöde över.
    * Ett problem rapporteras till hälso övervakaren. Standard övervakaren för hälso skickar alla rapporterade problem till spårnings loggen.
  * En ny offentlig egenskap har lagts `ChangeFeedProcessorOptions.ChangeFeedTimeout`till:. Standardvärdet för den här egenskapen är 10 minuter.
  * Ett nytt offentligt Enum-värde har `Monitoring.MonitoredOperation.ReadChangeFeed`lagts till:. När värdet för `HealthMonitoringRecord.Operation` är inställt `Monitoring.MonitoredOperation.ReadChangeFeed`på, anger det att hälso problemet är relaterat till att läsa ändrings flöde.

### <a name="227"></a><a name="2.2.7"/>2.2.7
* Förbättrad strategi för belastnings utjämning för scenariot när du hämtar alla lån tar längre tid än lånets förfallo intervall, t. ex. på grund av nätverks problem:
  * I den här scenario belastnings Utjämnings algoritmen som används för att betrakta lån som utgångna, vilket orsakar att stjäla lån från aktiva ägare. Detta kan utlösa onödig åter balansering av många lån.
  * Det här problemet åtgärdas i den här versionen genom att undvika återförsök vid en konflikt vid förvärv av utgånget lån som ägaren inte har ändrat och posponing förvärvat lån till nästa belastnings utjämning iteration.

### <a name="226"></a><a name="2.2.6"/>2.2.6
* Förbättrad hantering av observatörs undantag.
* Utförlig information om fel i Övervakare:
  * När en observatör stängs på grund av ett undantag som har utlösts av observatörens ProcessChangesAsync, kommer CloseAsync nu att få orsaks parametern inställd på ChangeFeedObserverCloseReason. ObserverError.
  * Spårning för identifiering av fel i användar kod har lagts till i en övervakare.

### <a name="225"></a><a name="2.2.5"/>2.2.5
* Stöd har lagts till för hantering av delning i samlingar som använder data flöde för delad databas.
  * I den här versionen åtgärdas ett problem som kan uppstå under delning i samlingar som använder data flöde för delade databaser när dela resultat i ombalansering av partitioner med bara ett underordnat nyckel intervall för nycklar som skapats, i stället för två. När detta inträffar kan byte av flödes processor fastna för att ta bort lånet för det gamla partitionsnamnet och inte skapa nya lån. Problemet har korrigerats i den här versionen.

### <a name="224"></a><a name="2.2.4"/>2.2.4
* Den nya egenskapen ChangeFeedProcessorOptions. StartContinuation har lagts till för att stödja start av ändrings flöde från begär ande token för begäran Detta används endast när en Lease-samling är tom eller om ett lån inte har angetts för ContinuationToken. För lån i en Lease-samling som har ContinuationToken angetts används ContinuationToken och ChangeFeedProcessorOptions. StartContinuation ignoreras.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Stöd har lagts till för att använda anpassade Arkiv för att bevara fortsättnings-token per partition.
  * Till exempel kan ett anpassat leasing lager Azure Cosmos DB en lånad samling partitionerad på ett anpassat sätt.
  * Anpassade hyres lager kan använda nya utöknings punkter ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager) och ILeaseStoreManager offentliga gränssnitt.
  * Omstrukturering av ILeaseManager-gränssnittet i flera roll gränssnitt.
* Mindre brytande ändring: borttagen utöknings punkt ChangeFeedProcessorBuilder. WithLeaseManager (ILeaseManager), Använd ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager) i stället.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* I den här versionen åtgärdas ett problem som uppstår under bearbetning av en delad i övervakad samling och en partitionerad låne samling. När ett lån bearbetas för delad partition får inte det lån som motsvarar den partitionen tas bort. Problemet har korrigerats i den här versionen.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Fast uppskattnings beräkning för flera huvud konton och nytt format för sessionstoken.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Stöd har lagts till för partitionerade låne samlingar. Partitionsnyckel måste definieras som/ID.
* Mindre avbrytande ändring: metoderna i IChangeFeedDocumentClient-gränssnittet och ChangeFeedDocumentClient-klassen ändrades till att omfatta RequestOptions-och CancellationToken-parametrar. IChangeFeedDocumentClient är en avancerad utöknings punkt som gör att du kan tillhandahålla anpassad implementering av dokument klienten som ska användas med Change feed-processor, t. ex. dekorera DocumentClient och snappa upp alla anrop till den för att utföra extra spårning, fel hantering osv. Med den här uppdateringen måste den kod som implementerar IChangeFeedDocumentClient ändras för att inkludera nya parametrar i implementeringen.
* Förbättringar av mindre diagnostik.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Nytt API,&lt;aktivitet IReadOnlyList&lt;RemainingPartitionWork&gt; &gt; IRemainingWorkEstimator. GetEstimatedRemainingWorkPerPartitionAsync () har lagts till. Detta kan användas för att få uppskattat arbete för varje partition.
* Stöder Microsoft. Azure. DocumentDB SDK 2,0. Kräver Microsoft. Azure. DocumentDB 2,0 eller senare.

### <a name="206"></a><a name="2.0.6"/>2.0.6
* Lade till egenskapen ChangeFeedEventHost. HostName offentlig för kompatibilitet med v1.

### <a name="205"></a><a name="2.0.5"/>2.0.5
* Åtgärdat ett tävlings tillstånd som inträffar när partitionen delas. Tävlings tillståndet kan leda till att lånet tas emot och att det omedelbart förlorar det när partitionen delas och orsakar konkurrens. Problemet med konkurrens villkoret är löst i den här versionen.

### <a name="204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-för hands version
* Åtgärdat följande problem:
  * När partitions delning sker kan det finnas dubbel bearbetning av dokument som har ändrats före delningen.
  * GetEstimatedRemainingWork-API: n returnerade 0 när det inte fanns några lån i låne samlingen.

* Följande undantag görs offentliga. Tillägg som implementerar IPartitionProcessor kan utlösa dessa undantag.
  * Microsoft. Azure. Documents. ChangeFeedProcessor. Exceptions. LeaseLostException. 
  * Microsoft. Azure. Documents. ChangeFeedProcessor. Exceptions. PartitionException. 
  * Microsoft. Azure. Documents. ChangeFeedProcessor. Exceptions. PartitionNotFoundException.
  * Microsoft. Azure. Documents. ChangeFeedProcessor. Exceptions. PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-för hands version
* Mindre API-ändringar:
  * Tog bort ChangeFeedProcessorOptions. IsAutoCheckpointEnabled som marker ATS som föråldrad.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-för hands version
* Stabilitets förbättringar:
  * Bättre hantering av initiering av leasing lager. När leasing lagret är tomt kan bara en instans av processor initiera den, de andra väntar.
  * Mer stabil/effektiv låne förnyelse/-lansering. Att förnya och släppa ett lån för en partition är oberoende av att förnya andra. I v1 som utfördes sekventiellt för alla partitioner.
* Ny v2-API:
  * Builder-mönster för flexibel konstruktion av processorn: klassen ChangeFeedProcessorBuilder.
    * Kan ta en valfri kombination av parametrar.
    * Kan ta DocumentClient-instans för övervakning och/eller Lease-samling (inte tillgängligt i v1).
  * IChangeFeedObserver. ProcessChangesAsync tar nu CancellationToken.
  * IRemainingWorkEstimator – den återstående arbets uppskattningen kan användas separat från processorn.
  * Nya utöknings punkter:
    * IPartitionLoadBalancingStrategy – för anpassad belastnings utjämning av partitioner mellan instanser av processorn.
    * ILease, ILeaseManager – för anpassad hantering av lån.
    * IPartitionProcessor – för anpassade bearbetnings ändringar på en partition.
* Logging – använder [LibLog](https://github.com/damianh/LibLog) -bibliotek.
* 100% bakåtkompatibel med v1-API.
* Ny kodbas.
* Kompatibel med [SQL SDK](sql-api-sdk-dotnet.md) -versioner 1.21.1 och senare.

### <a name="v1-builds"></a>v1-versioner

### <a name="133"></a><a name="1.3.3"/>1.3.3
* Ytterligare loggning har lagts till.
* En DocumentClient-läcka har åtgärd ATS vid anrop av väntande arbets uppskattning flera gånger.

### <a name="132"></a><a name="1.3.2"/>1.3.2
* Korrigeringar i den väntande arbets beräkningen.

### <a name="131"></a><a name="1.3.1"/>1.3.1
* Stabilitets förbättringar.
  * Korrigering för hantering av avbrutna uppgifts problem som kan leda till stoppade observatörer på vissa partitioner.
* Stöd för manuell kontroll punkt.
* Kompatibel med [SQL SDK](sql-api-sdk-dotnet.md) -versionerna 1,21 och senare.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Lägger till stöd för .NET standard 2,0. Paketet stöder `netstandard2.0` nu och `net451` Ramverks-monikers.
* Kompatibel med [SQL SDK](sql-api-sdk-dotnet.md) -versioner 1.17.0 och senare.
* Kompatibel med [SQL .net Core SDK](sql-api-sdk-dotnet-core.md) -versioner 1.5.1 och senare.

### <a name="111"></a><a name="1.1.1"/>1.1.1
* Åtgärdar ett problem med beräkningen av beräkningen av återstående arbete när ändrings flödet var tomt eller inget arbete väntades.
* Kompatibel med [SQL SDK](sql-api-sdk-dotnet.md) -versioner 1.13.2 och senare.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Har lagt till en metod för att få en uppskattning av återstående arbete som bearbetas i ändrings flödet.
* Kompatibel med [SQL SDK](sql-api-sdk-dotnet.md) -versioner 1.13.2 och senare.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Kompatibel med [SQL SDK](sql-api-sdk-dotnet.md) -versioner 1.14.1 och nedan.

## <a name="release--retirement-dates"></a>Frisläpp & indragnings datum

Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds.

Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK: n, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt. 

Alla förfrågningar till Cosmos DB med hjälp av en tillbakadragen SDK avvisas av tjänsten.

<br/>

| Version | Lanserings datum | Datum för indragning |
| --- | --- | --- |
| [2.3.0](#2.3.0) |2 april 2020 |--- |
| [2.2.8](#2.2.8) |28 oktober 2019 |--- |
| [2.2.7](#2.2.7) |14 maj, 2019 |--- |
| [2.2.6](#2.2.6) |29 januari 2019 |--- |
| [2.2.5](#2.2.5) |13 december 2018 |--- |
| [2.2.4](#2.2.4) |29 november 2018 |--- |
| [2.2.3](#2.2.3) |19 november 2018 |--- |
| [2.2.2](#2.2.2) |31 oktober 2018 |--- |
| [2.2.1](#2.2.1) |24 oktober 2018 |--- |
| [1.3.3](#1.3.3) |08 maj, 2018 |--- |
| [1.3.2](#1.3.2) |18 april 2018 |--- |
| [1.3.1](#1.3.1) |13 mars 2018 |--- |
| [1.2.0](#1.2.0) |31 oktober 2017 |--- |
| [1.1.1](#1.1.1) |29 augusti 2017 |--- |
| [1.1.0](#1.1.0) |13 augusti 2017 |--- |
| [1.0.0](#1.0.0) |Den 07 juli 2017 |--- |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även

Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.
