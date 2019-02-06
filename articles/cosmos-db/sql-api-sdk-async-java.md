---
title: 'Azure Cosmos DB: SQL Async Java API, SDK och resurser'
description: Lär dig allt om SQL Async Java API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL Async Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 2/4/2019
ms.author: moderakh
ms.openlocfilehash: 15bab6af4054085d00ec5c2edb7afb993a620b55
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756265"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK för SQL-API: Viktig information och resurser
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
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SDK skiljer sig från SQL API Java-SDK: N genom att tillhandahålla asynkrona åtgärder med stöd för den [Netty biblioteket](https://netty.io/). Ett befintligt [SQL API Java SDK](sql-api-sdk-java.md) har inte stöd för asynkrona åtgärder. 

<table>

<tr><td>**Hämta SDK**</td><td>[Maven 3.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**API-dokumentation**</td><td>[Java API-referensdokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable)</td></tr>

<tr><td>**Bidra till SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Kom igång**</td><td>[Kom igång med Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Kodexempel**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Prestandatips**</td><td>[GitHub viktigt](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Lägsta stödda körningar**</td><td>[JDK 8](https://aka.ms/azure-jdks)</td></tr>
</table></br>

## <a name="release-notes"></a>Viktig information

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Stöd har lagts till för direkt.
* Ändra API: er som tar emot java.util.Collection där ordningen är viktig att acceptera java.util.List i stället.
  Nu acceptera ConnectionPolicy#getPreferredLocations() och JsonSerialization PartitionKey(.) lista.
* En bugg session för dokumentet frågan i gateway-läge.
* Uppgraderas beroenden (netty 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Korrigeringar hantera mycket stora frågesvar.
* Åtgärdar token resurshantering när instanser skapades av klienten ([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Uppgraderas sårbara beroende jackson-databind ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* En bugg resource dataläckor.
* Stöd har lagts till för MultiPolygon
* Tillagt stöd för anpassade huvuden i RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* En bugg paketering.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Ett NPE-fel har åtgärdats i sökvägen för skrivning försök igen.
* Ett NPE-fel har åtgärdats i slutpunkten management.
* Uppgraderas sårbara beroenden ([GitHub #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Stöd har lagts till för Netty nätverk loggning för felsökning.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Tillagt stöd för flera regioner skrivning.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Stöd har lagts till för Proxy.
* Tillagt stöd för token resursauktorisering.
* En bugg i hantering av stora partitionsnycklar ([GitHub #63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Dokumentation om bättre.
* SDK har strukturerats i mer detaljerade moduler.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* En bugg för andra språk än engelska ([GitHub #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Har lagts till hjälpmetoder i konflikt resurs.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Ersatts org.json beroende av jackson på grund av prestandaskäl och licensiering ([GitHub #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
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
* Ett fel har åtgärdats i Json-serialisering för tidsstämpel ([GitHub #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Ett fel har åtgärdats i Json-serialisering för enum.
* En bugg i hanteringen av dokument för storlek på 2MB ([GitHub #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Beroende com.fasterxml.jackson.core:jackson-databind uppgraderas till 2.9.5 på grund av ett fel ([jackson databind: GitHub #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Beroende på rxjava-tillägg som uppgraderas till 0.8.0.17 på grund av ett fel ([rxjava-tillägg: GitHub #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Metadatabeskrivning i pom-filen uppdateras för att vara infogad med resten av dokumentationen.
* Syntax improvement ([GitHub #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([GitHub #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Ytterligare belastning stöd har lagts till i frågan.
* Stöd har lagts till för partitions-id nyckelintervall i fråga.
* Åtgärda att tillåta större fortsättningstoken i huvudet i begäran (bugfix GitHub #24).
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

<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.4.0-beta-1](#2.4.0-beta-1) |4 februari 2019|--- |
| [2.3.1](#2.3.1) |15 jan 2019|--- |
| [2.3.0](#2.3.0) |Den 29 november 2018|--- |
| [2.2.2](#2.2.2) |8 november 2018|--- |
| [2.2.1](#2.2.1) |Den 2 november 2018|--- |
| [2.2.0](#2.2.0) |Den 22 september 2018|--- |
| [2.1.0](#2.1.0) |Den 5 september 2018|--- |
| [2.0.1](#2.0.1) |16 augusti 2018|--- |
| [2.0.0](#2.0.0) |20 juni 2018|--- |
| [1.0.2](#1.0.2) |Den 18 maj 2018|--- |
| [1.0.1](#1.0.1) |Den 20 april 2018|--- |
| [1.0.0](#1.0.0) |27 februari 2018|--- |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

