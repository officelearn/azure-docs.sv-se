---
title: Datapartitionering i Azure Cosmos DB Gremlin API
description: Lär dig hur du kan använda ett partitionerat diagram i Azure Cosmos DB. I den här artikeln beskrivs också kraven och metodtipsen för ett partitionerat diagram.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623377"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Använda en partitionerad graf i Azure Cosmos DB

En av de viktigaste funktionerna i Gremlin API i Azure Cosmos DB är möjligheten att hantera storskaliga grafer genom horisontell skalning. Behållarna kan skalas oberoende av lagring och dataflöde. Du kan skapa behållare i Azure Cosmos DB som kan skalas automatiskt för att lagra en grafdata. Data balanseras automatiskt baserat på den angivna **partitionsnyckeln**.

**Partitionering krävs** om behållaren förväntas lagra mer än 20 GB i storlek eller om du vill allokera mer än 10 000 begärandeenheter per sekund (RU). Samma allmänna principer från [Azure Cosmos DB-partitioneringsmekanismen](partition-data.md) gäller med några diagramspecifika optimeringar som beskrivs nedan.

![Grafpartitionering.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Mekanism för grafpartitionering

Följande riktlinjer beskriver hur partitioneringsstrategin i Azure Cosmos DB fungerar:

- **Både hörn och kanter lagras som JSON-dokument**.

- **Hörn kräver en partitionsnyckel**. Den här nyckeln avgör i vilken partition hörnet ska lagras via en hash-algoritm. Egenskapsnamnet för partitionsnyckeln definieras när en ny `/partitioning-key-name`behållare skapas och har ett format: .

- **Kanter lagras med källhörn**. Med andra ord definierar dess partitionsnyckel för varje hörn var de lagras tillsammans med dess utgående kanter. Den här optimeringen görs för att `out()` undvika korspartitionsfrågor när du använder kardinaliteten i diagramfrågor.

- **Kanter innehåller referenser till de hörn som de pekar på**. Alla kanter lagras med partitionsnycklarna och ID:na för de hörn som de pekar på. Den här beräkningen `out()` gör att alla riktningsfrågor alltid är en begränsad partitionerad fråga och inte en blind fråga mellan partitioner. 

- **Diagramfrågor måste ange en partitionsnyckel**. För att dra full nytta av den horisontella partitioneringen i Azure Cosmos DB bör partitionsnyckeln anges när ett enda hörn har valts, när det är möjligt. Följande är frågor om hur du väljer en eller flera hörn i ett partitionerat diagram:

    - `/id`och `/label` stöds inte som partitionsnycklar för en behållare i Gremlin API.


    - Välja ett hörn efter ID och sedan **använda `.has()` steget för att ange egenskapen partitionsnyckel:** 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Välja ett hörn genom **att ange en tuppel med partitionsnyckelvärde och ID:** 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Ange en **matris med tupplar med partitionsnyckelvärden och ID:a**
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Välja en uppsättning hörn med deras ID:er och **ange en lista med partitionsnyckelvärden:** 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Använda **partitionsstrategin** i början av en fråga och ange en partition för resten av Gremlin-frågan: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Metodtips när du använder ett partitionerat diagram

Använd följande riktlinjer för att säkerställa prestanda och skalbarhet när du använder partitionerade diagram med obegränsade behållare:

- **Ange alltid värdet för partitionsnyckeln när du frågar ett hörn**. Att få hörn från en känd partition är ett sätt att uppnå prestanda. Alla efterföljande adjacency-åtgärder kommer alltid att begränsas till en partition eftersom kanter innehåller referens-ID och partitionsnyckel till sina målver.

- **Använd utgående riktning när du frågar kanter när det är möjligt**. Som nämnts ovan lagras kanter med sina källver i utgående riktning. Så chanserna att tillgripa korspartitionsfrågor minimeras när data och frågor är utformade med detta mönster i åtanke. Tvärtom kommer `in()` frågan alltid att vara en dyr fan-out fråga.

- **Välj en partitionsnyckel som jämnt kommer att distribuera data över partitioner**. Detta beslut beror i hög grad på lösningens datamodell. Läs mer om hur du skapar en lämplig partitionsnyckel i [Partitionering och skala i Azure Cosmos DB](partition-data.md).

- **Optimera frågor för att hämta data inom gränserna för en partition**. En optimal partitioneringsstrategi skulle justeras till frågemönstren. Frågor som hämtar data från en enda partition ger bästa möjliga prestanda.

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta att läsa följande artiklar:

* Lär dig mer om [Partition och skala i Azure Cosmos DB](partition-data.md).
* Läs mer om [Gremlin-stödet i Gremlin API](gremlin-support.md).
* Läs mer om [Introduktion till Gremlin API](graph-introduction.md).
