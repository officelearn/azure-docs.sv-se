---
title: 'Azure Cosmos DB: SQL asynkrona Java API, SDK & resurser | Microsoft Docs'
description: Lär dig mer om SQL asynkrona Java API och SDK inklusive frisläppningsdatum, tillbakadragning datum och ändringar mellan varje version av Azure Cosmos DB SQL asynkrona Java SDK.
services: cosmos-db
documentationcenter: java
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/28/2018
ms.author: mimig
ms.openlocfilehash: d117f0571bef8f8a2ec48a067d7796f03c0e535a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
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
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

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

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK med stöd för slutpunkt till slutpunkt för icke-blockerande i/o med hjälp av den [Netty biblioteket](http://netty.io/) i gateway-läge. 

## <a name="release-and-retirement-dates"></a>Versionen och tillbakadragning datum
Microsoft meddelar notification minst **12 månader** innan du tar bort en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar bara lägga till den aktuella SDK, som vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt.

Alla förfrågningar till Cosmos-databasen med en pensionerad SDK avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Datumet för tillbakadragandet |
| --- | --- | --- |
| [1.0.0](#1.0.0) |27 februari 2018|--- |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Läs mer om Cosmos-DB i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) sida.

