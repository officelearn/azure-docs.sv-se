---
title: Gränser för Azure Cosmos DB Gremlin
description: Referensdokumentation för körningsbegränsningar för Graph-motorn
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029843"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Gremlin-gränser i Azure Cosmos DB
Den här artikeln beskriver gränserna för Azure Cosmos DB Gremlin-motorn och förklarar hur de kan påverka kundtrasor.

Cosmos DB Gremlin är byggd ovanpå Cosmos DB infrastruktur. På grund av detta gäller fortfarande alla begränsningar som förklaras i [Azure Cosmos DB-tjänstgränser.](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) 

## <a name="limits"></a>Begränsningar

När Gremlin-gränsen har nåtts avbryts traversal med en **x-ms-status-kod** på 429 som anger ett begränsningsfel. Mer information finns i [Gremlin-serversvarshuvudena.](gremlin-limits.md)

**Resurs**    | **Standardgräns** | **Förklaring**
--- | --- | ---
*Skriptets längd* | **64 kB** | Maximal längd för ett Gremlin-bläddringsskript per begäran.
*Operatorns djup* | **400** |  Totalt antal unika steg i en bläddring. Har till ```g.V().out()``` exempel ett operatorantal på 2: V() och ut(), ```g.V('label').repeat(out()).times(100)``` har operatörsdjup på ```.times(100)``` 3: ```.repeat()``` V(), repeat() och out() eftersom är en parameter till operatorn.
*Grad av parallellitet* | **32** | Maximalt antal lagringspartitioner i en enskild förfrågan till lagringsskiktet. Diagram med hundratals partitioner påverkas av den här gränsen.
*Upprepningsgräns* | **32** | Maximalt antal iterationer en ```.repeat()```-operator kan köra. Varje iteration ```.repeat()``` av steg i de flesta fall kör bredd-första traversal, vilket innebär att alla traversal är begränsad till högst 32 humle mellan hörn.
*Tidsgräns för bläddring* | **30 sekunder** | Traversal avbryts när det överstiger den här gången. Cosmos DB Graph är en OLTP-databas där ett stort antal bläddringar kan slutföras på bara några millisekunder. Om du vill köra OLAP-frågor i Cosmos DB Graph använder du [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) med [graph dataramar](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) och [Cosmos DB Spark Connector](https://github.com/Azure/azure-cosmosdb-spark).
*Tidsgräns för vilande anslutning* | **1 timme** | Hur lång tid gremlin-tjänsten håller inaktiva webbsocketanslutningar öppna. TCP keep-alive paket eller HTTP keep-alive begäranden förlänger inte anslutningslivslängden utöver denna gräns. Cosmos DB Graph-motorn anser att websocketanslutningar är inaktiva om det inte finns några aktiva Gremlin-begäranden som körs på den.
*Resurstoken per timme* | **100** | Antal unika resurstoken som Gremlin-klienter använder till att ansluta till Gremlin-kontot i en region. När programmet överskrider den unika `"Exceeded allowed resource token limit of 100 that can be used concurrently"` tokengränsen per timme returneras det vid nästa autentiseringsbegäran.

## <a name="next-steps"></a>Nästa steg
* [Svarshuvuden för Azure Cosmos DB Gremlin](gremlin-headers.md) 
* [Azure Cosmos DB-resurstokens med Gremlin](how-to-use-resource-tokens-gremlin.md)
