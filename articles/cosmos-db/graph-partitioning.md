---
title: Graph API partitionering | Microsoft Docs
description: Lär dig hur du kan använda ett partitionerade diagram i Azure Cosmos-databasen.
services: cosmos-db
author: luisbosquez
documentationcenter: ''
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: db41efeee467d2cda89f62e0a18cf89cec2d9e63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Använder ett partitionerat diagram i Azure Cosmos DB

En av de viktigaste funktionerna i Graph API i Azure Cosmos DB är möjligheten att hantera stora diagram via vågräta skalbarhet. Den här processen uppnås genom den [partitionering funktioner i Azure Cosmos DB](partition-data.md#how-does-partitioning-work), som gör använda samlingar, kallas även behållare som kan skalas oberoende vad gäller lagring och genomflöde. Azure Cosmos-DB stöder följande typer av behållare för alla API: er:

- **Fast samling**: dessa samlingar kan lagra ett diagram databasen upp till 10 GB i storlek med högst 10 000 frågeenheter per sekund som tilldelas till den. Om du vill skapa en fast samling du behöver inte ange en partition nyckelegenskap i data.

- **Obegränsade samling**: dessa samlingar kan anpassas automatiskt för att lagra ett diagram gräns 10 GB via horisontell partitionering. Varje partition lagrar 10 GB och data automatiskt balanseras baserat på de **angivna partitionsnyckel**, som kommer att vara en obligatorisk parameter när du använder en obegränsad samling. Den här typen av behållare kan lagra en nästan obegränsad storlek och kan upp till 100 000 frågeenheter per sekund eller mer [genom att kontakta supporten](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

I det här dokumentet beskrivs närmare information om hur diagrammet databaser är partitionerad tillsammans med dess konsekvenser för både formhörnen (eller noder) och kanter.

## <a name="requirements-for-partitioned-graph"></a>Krav för partitionerade diagram

Nedan visas information som behöver för att förstå när du skapar en partitionerad graph-behållare:
- **Konfigurera partitionering blir det nödvändigt** om samlingen förväntas vara mer än 10 GB i storlek och/eller om tilldelning av fler än 10 000 frågeenheter per sekund (RU/s) kommer att krävas.
- **Både formhörnen och kanter lagras som JSON-dokument** i serverdelen av en Azure Cosmos DB Graph API-behållare.
- **Formhörnen kräver en partitionsnyckel**. Den här nyckeln avgör i vilken partition vertex lagras via en hash-algoritm. Namnet på den här Partitionsnyckeln är en enstaka ord sträng utan blanksteg eller specialtecken och definieras när du skapar en ny samling i formatet `/partitioning-key-name` på portalen.
- **Kanter lagras tillsammans med deras källa vertex**. Med andra ord för varje nod definierar sin partitionsnyckel var de ska lagras tillsammans med dess utgående kanter. Detta görs för att undvika cross-partition frågor när du använder den `out()` kardinalitet i diagrammet frågor.
- **Diagrammet frågor måste du ange en partitionsnyckel**. Om du vill dra full nytta av horisontell partitionering i Azure Cosmos DB, anges Partitionsnyckeln när en enskild nod är valt, när det är möjligt. Följande är några frågor för att markera en eller flera formhörnen i ett partitionerade diagram:

    - Markera en nod-ID: t, sedan **med hjälp av den `.has()` steg för att ange egenskapen partitions**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Markera en nod av **att ange en tuppel inklusive partitionsnyckelvärde och ID**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Ange en **matris av tupplar för partitionsnyckelvärden och ID: N**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Markera en uppsättning formhörnen och **att ange en lista över partitionsnyckelvärden**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Rekommenderade metoder när du använder ett partitionerat diagram

Nedan följer riktlinjer som ska följas för att säkerställa bästa möjliga prestanda och skalbarhet när du använder partitionerade diagram i obegränsade samlingar:
- **Alltid ange partitionsnyckelvärde när du frågar en brytpunkt**. Hämta en nod från en känd partition är det mest effektiva sättet vad gäller prestanda.
- **Använda utgående riktning när du frågar kanter när det är möjligt**. Som nämnts ovan är lagras kanter med deras källa formhörnen i utgående riktning. Det innebär att minimeras risken för att sortera till cross-partition frågor när data och frågor som är utformade med det här mönstret i åtanke.
- **Välj en partitionsnyckel som ska distribuera data jämnt över partitioner**. Beslutet beroende kraftigt av datamodellen i lösningen. Läs mer om att skapa en lämplig partitionsnyckel i [Partitining och skala i Azure Cosmos DB](partition-data.md).
- **Optimera frågor för att hämta data inom ramen för en partition när det är möjligt**. En optimal partitioneringsstrategi skulle justeras mot frågar mönster. Frågor som hämtar data från en enda partition ger bästa möjliga prestanda.

## <a name="next-steps"></a>Nästa steg
En översikt över begrepp och bästa praxis för partitionering med en Azure Cosmos DB Graph API har angetts i den här artikeln. 

* Lär dig mer om [Partition och skala i Azure Cosmos DB](partition-data.md).
* Lär dig mer om den [Gremlin stöd i Graph API](gremlin-support.md).
* Lär dig mer om [introduktion till Graph API](graph-introduction.md).
