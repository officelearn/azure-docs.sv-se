---
title: Gränser för Azure Cosmos DB Gremlin
description: Referens dokumentation för körnings begränsningar i diagram motor
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911081"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin-gränser
Den här artikeln beskriver gränserna för Azure Cosmos DB Gremlin-motorn och förklarar hur de kan påverka kund bläddringskontroll.

Cosmos DB Gremlin skapas ovanpå Cosmos DB-infrastruktur, vilket är orsaken till att alla begränsningar i [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) fortfarande gäller. 

## <a name="limits"></a>Begränsningar

När Gremlin-gränsen uppnås avbryts traversen med **x-MS-status-Code** = 429 som anger ett begränsnings fel.

**Resurs**    | **Standardgräns** | **Förklaring**
--- | --- | ---
*Minne per begäran* | **2 GB** | Maximal mängd minne som en förfrågan kan förbruka vid bearbetning. Begär Anden som behöver beräkning av stora data mängder använder ytterligare minne. Överväg att ange mindre data uppsättningar för att undvika att den här gränsen passeras eller Använd OLAP-lösningar.
*Skript längd* | **64 KB** | Maximal längd för ett Gremlin-bläddringsskript per begäran.
*Operator djup* | **400** |  Totalt antal unika steg i en bläddring. ```g.V().out()``` Har till exempel antalet operatorer på 2: V () och out () ```g.V('label').repeat(out()).times(100)``` har operatörs djupet på 3: V (), REPEAT () och out () eftersom ```.times(100)``` är en parameter till ```.repeat()``` operatorn.
*Grad av parallellitet* | **32** | Maximalt antal lagringspartitioner i en enskild förfrågan till lagringsskiktet. Grafer med hundratals partitioner påverkas av den här gränsen.
*Upprepnings gräns* | **32** | Maximalt antal iterationer en ```.repeat()```-operator kan köra. Varje iteration av ```.repeat()``` steg i de flesta fall kör en bredd-första genom gång, vilket innebär att all traversr är begränsad till högst 32 hopp mellan hörnen.
*Timeout för bläddringskontroll* | **30 sekunder** | Traversal avbryts när den är för tillfället. Cosmos DB Graph är en OLTP-databas där ett stort antal bläddringar kan slutföras på bara några millisekunder. Om du vill köra OLAP-frågor i Cosmos DB Graph använder du [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) med [diagram Data ramar](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) och [Cosmos DB Spark-anslutning](https://github.com/Azure/azure-cosmosdb-spark).
*Predikat-gräns* | **20** | Antal ```.has()```- eller ```.hasNot()```-steg som tillämpas på en enskild kant eller ett enskilt hörn. När den här gränsen nås visas felet ```The SQL query exceeded the maximum number of joins. The allowed limit is 20``` i programmet. Det är ett tillfälligt besvär som teamet arbetar för att lyfta upp den här gränsen. 
*Timeout för inaktiv anslutning* | **5 timmar** | Den tid som Graph Server håller WebSocket-anslutningen öppen utan trafik på den. TCP Keep-Alive-paket eller HTTP Keep-Alive-begäranden utökar inte anslutnings livs längd utöver den här gränsen, men om de inte skickas kan den underliggande Azure-infrastrukturen stänga anslutningen även snart. Cosmos DB diagram motor anses vara inaktiv om det inte finns några Gremlin-bläddringskontroll som körs på den.
*Resource-token per timme* | **100** | Antal unika resurs-token som används av Gremlin-klienter för att ansluta till Gremlin-kontot i en region. När programmet överskrider den unika Tim gränsen `"Exceeded allowed resource token limit of 100 that can be used concurrently"` för token returneras nästa autentiseringsbegäran.

## <a name="next-steps"></a>Nästa steg
* [Azure Cosmos DB Gremlin-svarshuvuden](gremlin-headers.md) 
* [Azure Cosmos DB-resurspooler med Gremlin](how-to-use-resource-tokens-gremlin.md)
