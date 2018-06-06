---
title: 'Azure Cosmos DB: SQL asynkrona Java API, SDK & resurser | Microsoft Docs'
description: Lär dig mer om SQL asynkrona Java API och SDK inklusive frisläppningsdatum, tillbakadragning datum och ändringar mellan varje version av Azure Cosmos DB SQL asynkrona Java SDK.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/18/2018
ms.author: sngun
ms.openlocfilehash: 4b12652783c94d132a5c1f4d4aa352d4e2318edf
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797676"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB asynkrona Java SDK för SQL-API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET ändra Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

SQL API asynkrona Java SDK skiljer sig från SQL API Java SDK genom att tillhandahålla asynkrona åtgärder med stöd från den [Netty biblioteket](http://netty.io/). Ett befintligt [SQL API Java SDK](sql-api-sdk-java.md) stöder inte asynkrona åtgärder. 

<table>

<tr><td>**SDK-hämtningen**</td><td>[Maven 3.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**API-dokumentationen**</td><td>[Java API-referensdokumentation](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Bidra till SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Kom igång**</td><td>[Kom igång med Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**kodexempel**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Prestandatips**</td><td>[Github viktigt](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minsta stöds runtime**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Viktig information

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Stöd har lagts till för unikt Index principen.
* Stöd för att begränsa fortsättning token svarsstorlek i feed alternativ har lagts till.
* Stöd har lagts till för partitionen delning i mellan Partitionsfrågan.
* Fast ett programfel i Json-serialisering tidsstämpel ([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Fast ett programfel i Json-serialisering för uppräkning.
* Fast ett programfel i Hantera dokument 2MB storlek ([github #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Beroende com.fasterxml.jackson.core:jackson-databind uppgraderas till 2.9.5 på grund av ett programfel ([jackson databind: github #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Beroende på rxjava tillägg uppgraderas till 0.8.0.17 på grund av ett programfel ([rxjava-tillägg: github #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Metadatabeskrivning i pom filen har uppdaterats infogad resten av dokumentationen.
* Syntaxen improvement ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Ytterligare belastning stöd lagts till i frågan.
* Stöd har lagts till för partitions-id viktiga intervallet i frågan.
* Åtgärda att tillåta större fortsättningstoken i huvudet i begäran (bugfix github #24).
* netty beroende uppgraderas till 4.1.22.Final så JVM stängs av när huvudtråden har slutförts.
* Korrigera att undvika att skicka sessionstoken vid läsning av master resurser.
* Lägga till fler exempel.
* Lägga till flera benchmarking scenarier.
* Fast Java-rubrikfiler för generering av rätt java-dokument.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK med stöd för slutpunkt till slutpunkt för icke-blockerande i/o med hjälp av den [Netty biblioteket](http://netty.io/) i gateway-läge. 

## <a name="release-and-retirement-dates"></a>Versionen och tillbakadragning datum
Microsoft meddelar notification minst **12 månader** innan du tar bort en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar bara lägga till den aktuella SDK. Det rekommenderas så att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt.

Alla förfrågningar till Cosmos-databasen med en pensionerad SDK avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Datumet för tillbakadragandet |
| --- | --- | --- |
| [1.0.2](#1.0.2) |18 maj 2018|--- |
| [1.0.1](#1.0.1) |20 april 2018|--- |
| [1.0.0](#1.0.0) |27 februari 2018|--- |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Läs mer om Cosmos-DB i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) sida.

