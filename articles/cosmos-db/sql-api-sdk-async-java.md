---
title: 'Azure Cosmos DB: SQL-asynkron Java-API, SDK &-resurser'
description: Lär dig allt om SQL Async Java API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL Async Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 156699b8d8c1a645961f4e919bdd843d995a3d18
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142654"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB asynkron Java SDK för SQL API: Viktig information och resurser
> [!div class="op_single_selector"]
> * [NET](sql-api-sdk-dotnet.md)
> * [.NET-Ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
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

SQL API Async Java SDK skiljer sig från SQL API Java-SDK: N genom att tillhandahålla asynkrona åtgärder med stöd för den [Netty biblioteket](https://netty.io/). Ett befintligt [SQL API Java SDK](sql-api-sdk-java.md) har inte stöd för asynkrona åtgärder. 

| |  |
|---|---|
| **Hämta SDK** | [Maven 3.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-dokumentation** |[Java API-referensdokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Bidra till SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Kom igång** | [Kom igång med Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Kodexempel** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Prestandatips**| [GitHub – viktigt](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Lägsta stödda körningar**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Viktig information

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0
* TCP-läget är nu aktiverat som standard
* Fråga efter mått i Cross partition returnerar nu alla partitioner
* Global stark fungerar nu korrekt
* Redundans för frågor som inte går att använda korrekt för flera huvud servrar
* Sambands ojämnheter för säkerhets snabb korrigeringar

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Bugfix för hash v2-stöd

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* Bugfix för resurs läcka på klienten # Close () ([github #88](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Stöd för stöd för fortsatt token för kors partitions frågor har lagts till.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Vissa buggar i direkt läge har åtgärd ATS.
* Förbättrad loggning i direkt läge.
* Förbättrad anslutnings hantering.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Anslutning till direkt läge är nu allmänt tillgänglig (GA). Ett exempel som använder anslutning via direkt läge finns i [Azure-cosmosdb-Java GitHub-](https://github.com/Azure/azure-cosmosdb-java) lagringsplatsen.
* Stöd har lagts till för QueryMetrics.
* Ändrade API: erna som accepterar Java. util. Collection för vilken ordning är viktigt att godkänna Java. util. list i stället. Nu ConnectionPolicy # getPreferredLocations (), JsonSerialization och PartitionKey (.) Accept (.).

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Stöd har lagts till för anslutning till direkt läge.
* Ändrade API: erna som accepterar Java. util. Collection för vilken ordning är viktigt att godkänna Java. util. list i stället.
  Nu ConnectionPolicy # getPreferredLocations (), JsonSerialization och PartitionKey (.) Accept (.).
* Åtgärdat ett sessions-fel för dokument fråga i Gateway-läge.
* Uppgraderade beroenden (nett 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Korrigeringar hanterar mycket stora fråge svar.
* Korrigerar hantering av resursens token när klienten instansieras ([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Uppgraderat sårbart beroende Jackson-DataBind ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* En bugg resource dataläckor.
* Stöd har lagts till för MultiPolygon
* Tillagt stöd för anpassade huvuden i RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* En bugg paketering.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Ett NPE-fel har åtgärdats i sökvägen för skrivning försök igen.
* Ett NPE-fel har åtgärdats i slutpunkten management.
* Uppgraderade sårbara beroenden ([GitHub #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Stöd har lagts till för Netty nätverk loggning för felsökning.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Tillagt stöd för flera regioner skrivning.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Stöd har lagts till för Proxy.
* Tillagt stöd för token resursauktorisering.
* En bugg har åtgärd ATS vid hantering av stora partitionsnyckel ([GitHub #63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Dokumentation om bättre.
* SDK har strukturerats i mer detaljerade moduler.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Ett fel har åtgärd ATS för icke-engelska språk ([GitHub #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Har lagts till hjälpmetoder i konflikt resurs.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Ett beroende av org. JSON har ersatts av Jackson på grund av prestanda orsaker och licensiering ([GitHub #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Ta bort föråldrad OfferV2 klass.
* Har lagts till accessor metod till erbjudandet klass för dataflöde innehåll.
* Valfri metod i dokumentet/resurs returnerar org.json typer ändras för att returnera en jackson objekttyp.
* getObject(.)-metoden för klasser utöka JsonSerializable ändras för att returnera en jackson ObjectNode typ.
* getCollection(.) metod har ändrats för att returnera en samling av ObjectNode.
* Borttagna JsonSerializable underklasser konstruktorer med org.json.JSONObject arg.
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) använder nu två mellanslag för indrag.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Stöd har lagts till för unikt Index-principen.
* Stöd har lagts till för att begränsa fortsättning token svarsstorlek i alternativen för feed.
* Stöd har lagts till för Partition Split i mellan Partitionsfrågan.
* En bugg har åtgärd ATS i[GitHub #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)(JSON timestamp).
* Ett fel har åtgärdats i Json-serialisering för enum.
* Ett fel har åtgärd ATS i hanteringen av dokument på 2 MB ([GitHub #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Beroende com. fasterxml. Jackson. Core: Jackson-DataBind uppgraderat till 2.9.5 på grund av ett[fel (Jackson-DataBind: GitHub #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Beroende av rxjava-extrafunktioner som uppgraderats till 0.8.0.17 på grund av ett[fel (rxjava-tillägg: GitHub #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Metadatabeskrivning i pom-filen uppdateras för att vara infogad med resten av dokumentationen.
* [GitHub #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([GitHub #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Ytterligare belastning stöd har lagts till i frågan.
* Stöd har lagts till för partitions-id nyckelintervall i fråga.
* Korrigera för att tillåta större fortsättnings-token i begär ande huvudet (Bugfix GitHub #24).
* Netty beroende uppgraderas till 4.1.22.Final så JVM stängs av när huvudtråden har slutförts.
* Åtgärda och Undvik att sessionstoken vid läsning av master-resurser.
* Lägga till fler exempel.
* Lägga till fler benchmarking scenarier.
* Fast Java header-filer för generering av rätt java-dokument

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK med stöd för slutpunkt till slutpunkt för icke-blockerande i/o med hjälp av den [Netty biblioteket](https://netty.io/) i gateway-läge. 

## <a name="release-and-retirement-dates"></a>Versionen och dras tillbaka datum
Microsoft meddelar meddelande minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar läggs endast till den aktuella SDK. Så rekommenderar vi att du alltid uppgraderar till den senaste versionen av SDK: N så tidigt som möjligt.

Varje begäran till Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.

> [!WARNING]
> Alla versioner **1. x** av ASYNKRON Java SDK för SQL API kommer att dras tillbaka den **30 augusti 2020**.
> 
>
<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.4.3](#2.4.3) |Mar 5, 2019|--- |
| [2.4.2](#2.4.2) |Mar 1, 2019|--- |
| [2.4.1](#2.4.1) |Feb 20, 2019|--- |
| [2.4.0](#2.4.0) |Feb 8, 2019|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |Feb 4, 2019|--- |
| [2.3.1](#2.3.1) |15 Jan 2019|--- |
| [2.3.0](#2.3.0) |Den 29 november 2018|--- |
| [2.2.2](#2.2.2) |8 november 2018|--- |
| [2.2.1](#2.2.1) |Den 2 november 2018|--- |
| [2.2.0](#2.2.0) |Den 22 september 2018|--- |
| [2.1.0](#2.1.0) |Den 5 september 2018|--- |
| [2.0.1](#2.0.1) |16 augusti 2018|--- |
| [2.0.0](#2.0.0) |20 juni 2018|--- |
| [1.0.2](#1.0.2) |Den 18 maj 2018|30 augusti 2020 |
| [1.0.1](#1.0.1) |Den 20 april 2018|30 augusti 2020 |
| [1.0.0](#1.0.0) |27 februari 2018|30 augusti 2020 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

