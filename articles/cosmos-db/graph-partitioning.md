---
title: Datapartitionering i Azure Cosmos DB Gremlin-API
description: Lär dig hur du kan använda en partitionerad graph i Azure Cosmos DB. Den här artikeln beskriver också krav och bästa praxis för en partitionerad graf.
author: rockboyfor
ms.author: v-yeche
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 03/18/2019
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888425"
---
<!--Verify sucessfully-->
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Med hjälp av ett partitionerade diagram i Azure Cosmos DB

En av de viktigaste funktionerna i Gremlin-API i Azure Cosmos DB är möjligheten att hantera storskaliga diagram via horisontell skalning. Horisontell skalning uppnås via den [partitionering funktioner i Azure Cosmos DB](partition-data.md). Behållarna som kan skalas oberoende vad gäller lagring och dataflöde. Du kan skapa behållare i Azure Cosmos DB som kan skalas automatiskt för att lagra en diagramdata. Data fördelas automatiskt baserat på den angivna **partitionsnyckel**.

I det här dokumentet beskrivs ärendets på hur grafdatabaser partitioneras tillsammans med dess konsekvenser för både hörn (eller noder) och kanter.

## <a name="requirements-for-partitioned-graph"></a>Krav för partitionerade graph

Nedan visas information som behöver förstå när du skapar en partitionerad grafbehållare:

- **Partitionering krävs** om behållaren är förväntat att lagra mer än 10 GB i storlek eller om du vill allokera mer än 10 000 enheter för programbegäran per sekund (ru: er).

- **Både hörn och kanter lagras som JSON-dokument**.

- **Hörn kräver en partitionsnyckel**. Den här nyckeln avgör i vilken partition hörnet lagras via en hash-algoritm. Namnet på den här partitionsnyckel är ett enstaka ord sträng utan blanksteg eller specialtecken. Partitionsnyckeln definieras när du skapar en ny behållare och den har formatet: `/partitioning-key-name`.

- **Kanter lagras tillsammans med deras källvertex**. Med andra ord för varje brytpunkt definierar sin partitionsnyckel där de lagras tillsammans med dess utgående kanter. Detta görs för att undvika flera partitioner frågor när du använder den `out()` kardinalitet i graph-frågor.

- **Graph-frågor måste du ange en partitionsnyckel**. Om du vill dra full nytta av horisontell partitionering i Azure Cosmos DB, anges Partitionsnyckeln när en enskild brytpunkt väljs, när det är möjligt. Här följer några frågor för att välja en eller flera hörn i en partitionerad graph:

    - `/id` och `/label` stöds inte som partitionsnycklar för en behållare i Gremlin-API.

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

Använd följande riktlinjer för att säkerställa prestanda och skalbarhet när du använder partitionerade diagram med obegränsade behållare:

- **Ange alltid partitionsnyckelvärdet vid frågor till en brytpunkt**. Hämta hörn från en känd partition är ett sätt att uppnå prestanda.

- **Använda utgående riktning vid fråga kanter när det är möjligt**. Som nämnts ovan är lagras kanter med deras källkod hörn i den utgående riktningen. Så minimeras risken för tillgripa till flera partitioner frågor när data och frågor som är utformade med det här mönstret i åtanke.

- **Välj en partitionsnyckel som ska distribuera data jämnt över partitionerna**. Det här beslutet beroende kraftigt av datamodellen av lösningen. Läs mer om hur du skapar en lämplig partitionsnyckel i [partitionering och skalning i Azure Cosmos DB](partition-data.md).

- **Optimera frågor för att få data inom gränserna för en partition**. En optimal partitioneringsstrategin skulle justeras till frågor mönster. Frågor som hämtar data från en enda partition ger bästa möjliga prestanda.

## <a name="next-steps"></a>Nästa steg

Du kan sedan fortsätta att läsa följande artiklar:

* Lär dig mer om [partitionera och skala i Azure Cosmos DB](partition-data.md).
* Lär dig mer om den [Gremlin-support i Gremlin-API: et](gremlin-support.md).
* Lär dig mer om [introduktion till Gremlin-API](graph-introduction.md).

<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->