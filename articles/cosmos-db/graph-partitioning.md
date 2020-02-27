---
title: Datapartitionering i Azure Cosmos DB Gremlin-API
description: Lär dig hur du kan använda en partitionerad graph i Azure Cosmos DB. Den här artikeln beskriver också krav och bästa praxis för en partitionerad graf.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623377"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Med hjälp av ett partitionerade diagram i Azure Cosmos DB

En av de viktigaste funktionerna i Gremlin-API i Azure Cosmos DB är möjligheten att hantera storskaliga diagram via horisontell skalning. Behållarna som kan skalas oberoende vad gäller lagring och dataflöde. Du kan skapa behållare i Azure Cosmos DB som kan skalas automatiskt för att lagra en diagramdata. Data bal anse ras automatiskt baserat på den angivna **partitionsnyckel**.

**Partitionering krävs** om behållaren förväntas lagra mer än 20 GB i storlek eller om du vill allokera mer än 10 000 enheter för programbegäran per sekund (ru: er). Samma allmänna principer från mekanismen för [Azure Cosmos DB partitionering](partition-data.md) gäller med några diagram olika optimeringar som beskrivs nedan.

![Diagram partitionering.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Diagram partitionerings funktion

Följande rikt linjer beskriver hur partitionerings strategin i Azure Cosmos DB fungerar:

- **Både hörn och kanter lagras som JSON-dokument**.

- **Hörn kräver en partitionsnyckel**. Den här nyckeln avgör i vilken partition hörnet lagras via en hash-algoritm. Egenskaps namnet för partitionsnyckel definieras när du skapar en ny behållare och har ett format: `/partitioning-key-name`.

- **Kanterna kommer att lagras med deras käll-hörn**. Med andra ord för varje brytpunkt definierar sin partitionsnyckel där de lagras tillsammans med dess utgående kanter. Den här optimeringen görs för att undvika frågor över partitioner när du använder `out()` kardinalitet i graf-frågor.

- **Kanterna innehåller referenser till de hörn som de pekar på**. Alla kanter lagras med partitionsalternativ och ID: n för de hörn som de pekar på. Den här beräkningen gör att alla `out()` riktnings frågor alltid är en begränsad, partitionerad fråga och inte en fråga om en hemlig partition. 

- **Diagram frågor måste ange en partitionsnyckel**. Om du vill dra full nytta av horisontell partitionering i Azure Cosmos DB, anges Partitionsnyckeln när en enskild brytpunkt väljs, när det är möjligt. Här följer några frågor för att välja en eller flera hörn i en partitionerad graph:

    - `/id` och `/label` stöds inte som partitionsnyckel för en behållare i Gremlin-API: et.


    - Välj ett hörn efter ID och **Använd sedan `.has()` steget för att ange nyckel egenskapen för partitionen**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Välja ett formhörn genom att **Ange en tupel, inklusive partitionsnyckel och ID**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Ange en **matris med tupler av värden för partitionsnyckel och ID**:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Välja en uppsättning formhörn med sina ID: n och **Ange en lista med värden för partitionsnyckel**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Använda en **partitions strategi** i början av en fråga och ange en partition för omfånget för resten av Gremlin-frågan: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Bästa praxis när du använder ett partitionerat diagram

Använd följande riktlinjer för att säkerställa prestanda och skalbarhet när du använder partitionerade diagram med obegränsade behållare:

- **Ange alltid värdet för partitionsnyckel vid frågor mot ett hörn**. Hämta hörn från en känd partition är ett sätt att uppnå prestanda. Alla efterföljande angränsande åtgärder kommer alltid att begränsas till en partition eftersom kanterna innehåller referens-ID och partitionsnyckel till sina mål formhörn.

- **Använd den utgående riktningen när du frågar efter kanter när det är möjligt**. Som nämnts ovan är lagras kanter med deras källkod hörn i den utgående riktningen. Så minimeras risken för tillgripa till flera partitioner frågor när data och frågor som är utformade med det här mönstret i åtanke. I motsats är `in()`s frågan alltid en dyr fläkt fråga.

- **Välj en partitionsnyckel som jämnt distribuerar data mellan partitioner**. Det här beslutet beroende kraftigt av datamodellen av lösningen. Läs mer om hur du skapar en lämplig partitionsnyckel i [partitionering och skalning i Azure Cosmos DB](partition-data.md).

- **Optimera frågor för att hämta data inom gränserna för en partition**. En optimal partitioneringsstrategin skulle justeras till frågor mönster. Frågor som hämtar data från en enda partition ger bästa möjliga prestanda.

## <a name="next-steps"></a>Nästa steg

Du kan sedan fortsätta att läsa följande artiklar:

* Lär dig mer om [partition och skalning i Azure Cosmos DB](partition-data.md).
* Läs mer om [Gremlin-stöd i Gremlin-API](gremlin-support.md).
* Lär dig mer om [Introduktion till Gremlin-API](graph-introduction.md).
