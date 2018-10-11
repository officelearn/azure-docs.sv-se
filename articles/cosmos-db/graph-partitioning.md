---
title: Partitionering i Azure Cosmos DB Gremlin-API | Microsoft Docs
description: Lär dig hur du kan använda en partitionerad Graph i Azure Cosmos DB.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: conceptual
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: bf91ec77f8d7248c6fa2047636f295c187f626c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078230"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Med hjälp av ett partitionerade diagram i Azure Cosmos DB

En av de viktigaste funktionerna i Gremlin-API i Azure Cosmos DB är möjligheten att hantera storskaliga diagram via horisontell skalbarhet. Den här processen uppnås via den [partitionering funktioner i Azure Cosmos DB](partition-data.md#how-does-partitioning-work), vilket gör användning av behållare som kan skalas oberoende vad gäller lagring och dataflöde. Azure Cosmos DB stöder följande typer av behållare i alla API: er:

- **Fast behållare**: de här behållarna kan lagra ett diagram databasen upp till 10 GB i storlek med högst 10 000 enheter för programbegäran per sekund som tilldelas till den. Om du vill skapa en fast behållare är det inte nödvändigt att ange en nyckelegenskap för partitionen i data.

- **Obegränsad behållare**: de här behållarna kan automatiskt skala för att lagra en graf utöver gränsen på 10 GB via horisontell partitionering. Varje partition lagras 10 GB och data automatiskt balanseras baserat på den **angivna partitionsnyckel**, som kommer att vara en obligatorisk parameter när du använder en obegränsad behållare. Den här typen av behållare kan lagra ett praktiskt taget obegränsade datastorlek och tillåta upp till 100 000 enheter för programbegäran per sekund eller mer [genom att kontakta supporten](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

I det här dokumentet beskrivs ärendets på hur grafdatabaser partitioneras tillsammans med dess konsekvenser för både hörn (eller noder) och kanter.

## <a name="requirements-for-partitioned-graph"></a>Krav för partitionerade graph

Nedan visas information som behöver förstå när du skapar en partitionerad grafbehållare:

- **Ställa in partitionering är nödvändigt för** om behållaren förväntas vara större än 10 GB i storlek och/eller om tilldelning av över 10 000 enheter för programbegäran per sekund (RU/s) kommer att krävas.

- **Både hörn och kanter lagras som JSON-dokument** i serverdelen av en Gremlin-API för Azure Cosmos DB-behållare.

- **Hörn kräver en partitionsnyckel**. Den här nyckeln avgör i vilken partition hörnet lagras via en hash-algoritm. Namnet på den här partitionsnyckel är ett enstaka ord sträng utan blanksteg eller specialtecken och definieras när du skapar en ny behållare i formatet `/partitioning-key-name` på portalen.

- **Kanter lagras tillsammans med deras källvertex**. Med andra ord för varje brytpunkt definierar sin partitionsnyckel var de lagras tillsammans med dess utgående kanter. Detta görs för att undvika flera partitioner frågor när du använder den `out()` kardinalitet i graph-frågor.

- **Graph-frågor måste du ange en partitionsnyckel**. Om du vill dra full nytta av horisontell partitionering i Azure Cosmos DB, anges Partitionsnyckeln när en enskild brytpunkt väljs, när det är möjligt. Här följer några frågor för att välja en eller flera hörn i en partitionerad graph:

    - `/id` och `/label` stöds inte som partitionsnycklar för en behållare i Gremlin-API: et...


    - Att välja ett hörn med ID: T, sedan **med hjälp av den `.has()` steg för att ange egenskapen partitions**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Att välja ett hörn av **att ange en tuppel inklusive partitionsnyckelvärde och ID**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Ange en **mängd tupplar partitionsnyckelvärdena och ID: N**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Att välja en uppsättning hörn och **att ange en lista över partitionsnyckelvärdena**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Bästa praxis när du använder ett partitionerat diagram

Här följer några riktlinjer som ska följas för att säkerställa effektiv prestanda och skalbarhet när du använder partitionerade grafer, obegränsade behållare:

- **Ange alltid partitionsnyckelvärdet vid frågor till en brytpunkt**. Skaffa ett hörn från en känd partition är det effektivaste sättet i termer av prestanda.

- **Använda utgående riktning vid fråga kanter när det är möjligt**. Som nämnts ovan är lagras kanter med deras källkod hörn i den utgående riktningen. Det innebär att minimeras risken för tillgripa till flera partitioner frågor när data och frågor som är utformade med det här mönstret i åtanke.

- **Välj en partitionsnyckel som ska distribuera data jämnt över partitionerna**. Det här beslutet beroende kraftigt av datamodellen av lösningen. Läs mer om hur du skapar en lämplig partitionsnyckel i [partitionering och skalning i Azure Cosmos DB](partition-data.md).

- **Optimera frågor för att få data inom gränserna för en partition när det är möjligt**. En optimal partitioneringsstrategin skulle justeras till frågor mönster. Frågor som hämtar data från en enda partition ger bästa möjliga prestanda.

## <a name="next-steps"></a>Nästa steg
En översikt över koncepten och bästa praxis för partitionering med en Azure Cosmos DB Gremlin-API har angetts i den här artikeln. 

* Lär dig mer om [partitionera och skala i Azure Cosmos DB](partition-data.md).
* Lär dig mer om den [Gremlin-support i Gremlin-API: et](gremlin-support.md).
* Lär dig mer om [introduktion till Gremlin-API](graph-introduction.md).