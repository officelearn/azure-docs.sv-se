---
title: Graph-datamodeller för Azure Cosmos DB Gremlin-API
description: Lär dig mer om att utforma en grafdatabas med Cosmos DB Gremlin-API.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: c6ae23efa90874bbefc2aff35f8798aa6c0da791
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503422"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Graph-datamodeller för Azure Cosmos DB Gremlin-API

Följande dokument är utformad att ge rekommendationer för graph-datamodeller. Det här steget är viktigt för att säkerställa skalbarhet och prestanda för ett system för graph-databasen som data utvecklas. En effektiv datamodellen är särskilt viktigt med storskaliga diagram.

## <a name="requirements"></a>Krav

Den process som beskrivs i den här guiden baseras på följande antaganden:
 * Den **entiteter** identifieras i området problem. De här entiteterna är avsedda att användas _atomiskt_ för varje begäran. Med andra ord är inte databassystemet avsedd att hämta en enda entitet data i flera frågebegäranden.
 * Det finns en förståelse för **läsa och skriva krav** för databassystem. Dessa krav hjälper optimeringar som behövs för graph-datamodellen.
 * Principerna för den [Apache Tinkerpop egenskapen graph standard](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) är väl förstådda.

## <a name="when-do-i-need-a-graph-database"></a>När behöver jag en grafdatabas?

Graph-databaslösning kan tillämpas optimalt om enheter och relationer i en data-domän har något av följande egenskaper: 

* Entiteterna är **mycket anslutna** via beskrivande relationer. Fördelen med det här scenariot är det faktum att relationerna är sparas i minnet.
* Det finns **cykliskt relationer** eller **lokal refererar till entiteter**. Det här mönstret är ofta en utmaning när med relationella eller dokumentera databaser.
* Det finns **utvecklas dynamiskt relationer** mellan entiteter. Det här mönstret gäller särskilt för hierarkisk eller trädstruktur data med många nivåer.
* Det finns **många-till-många-relationer** mellan entiteter.
* Det finns **skriva och läsa kraven på både enheter och relationer**. 

Om ovanstående villkor uppfylls, är det troligt att en metod för graph-databas ger fördelar för **fråga komplexiteten**, **datamodelldata skalbarhet**, och **frågeprestandan**.

Nästa steg är att avgöra om diagrammet ska användas i analytiska eller transaktionella åtgärder. Om diagrammet är avsedd att användas för tung beräkning och bearbetning av arbetsbelastningar, det kan vara värt att utforska den [Cosmos DB Spark-anslutningsappen](https://docs.microsoft.com/azure/cosmos-db/spark-connector) och användning av den [GraphX biblioteket](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Hur du använder graph-objekt

Den [Apache Tinkerpop egenskapen graph standard](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) definierar två typer av objekt **hörn** och **kanter**. 

Här följer de rekommenderade säkerhetsmetoderna för egenskaperna i graph-objekt:

| Object | Egenskap | Typ | Anteckningar |
| --- | --- | --- |  --- |
| Vertex | id | String | Unikt tillämpas per partition. Om ett värde inte är angivna vid infogning, automatiskt genererade lagras GUID. |
| Vertex | label | String | Den här egenskapen används för att definiera vilken typ av enhet som representerar hörnet. Om ett värde inte anges, används standardvärdet ”hörn”. |
| Vertex | properties | Sträng, booleskt värde, numeriskt | En lista över olika egenskaper lagras som nyckel / värde-par i varje hörn. |
| Vertex | Partitionsnyckel | Sträng, booleskt värde, numeriskt | Den här egenskapen definierar var hörnet och kanterna utgående ska lagras. Läs mer om [grafpartitionering](graph-partitioning.md). |
| Edge | id | String | Unikt tillämpas per partition. Automatiskt genererade som standard. Kanter har vanligtvis inte behov unikt kan hämtas av ett ID. |
| Edge | label | String | Den här egenskapen används för att definiera typ av relation som har två hörn. |
| Edge | properties | Sträng, booleskt värde, numeriskt | En lista över olika egenskaper lagras som nyckel / värde-par i varje edge. |

> [!NOTE]
> Kanter kräver ett partitionsnyckelvärde eftersom dess värde tilldelas automatiskt baserat på deras källvertex. Läs mer i den [grafpartitionering](graph-partitioning.md) artikeln.

## <a name="entity-and-relationship-modeling-guidelines"></a>Entitets- och relation modellering riktlinjer

Följande är en uppsättning riktlinjer för att metoden datamodellering för en API för Azure Cosmos DB Gremlin graph-databas. Dessa riktlinjer förutsätter att det finns en befintlig definition av en domän för data och frågor för den.

> [!NOTE]
> Stegen som beskrivs nedan visas som rekommendationer. Den slutliga modellen ska utvärderas och testas innan dess beräkningen som produktionsklar. Dessutom är rekommendationerna nedan specifika för Azure Cosmos DB Gremlin-API-implementering. 

### <a name="modeling-vertices-and-properties"></a>Modellering hörn och egenskaper 

Det första steget för en grafikdatamodell är att mappa varje identifierad entitet till en **hörn objektet**. En en-till-en-mappning av alla enheter ska hörn ska vara ett första steg och komma att ändras.

En gemensam eftersom operativsystemet använder principbaserad är att egenskaper från en enda entitet som separata hörn. Studera exemplet nedan, där samma entitet representeras i två olika sätt:

* **Vertex-baserade egenskaper**: Med den här metoden använder entiteten tre separata hörn och kanter för att beskriva dess egenskaper. Även om den här metoden kan minska redundans, ökar komplexiteten i modellen. En ökning av modellen komplexiteten kan medföra extra svarstid, frågekomplexitet och kostnaden för beräkning. Den här modellen kan också vara ett problem vid partitionering.

![Entiteten modell med hörn för egenskaper.](./media/graph-modeling/graph-modeling-1.png)

* **Egenskapen-inbäddad hörn**: Den här metoden använder listan nyckel / värde-par som representerar alla egenskaper för entiteten i ett hörn. Den här metoden ger minskade modellen komplexitet, vilket leder till enklare frågor och bläddringar med mer kostnadseffektiv.

![Entiteten modell med hörn för egenskaper.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Ovanstående exempel visar en förenklad graph-modell till att bara visa en jämförelse mellan de två sätt att dela upp entitetsegenskaper.

Den **egenskapen-inbäddad hörn** mönster har vanligtvis ett mer effektivt och skalbart sätt. Standard-metod för att en ny grafikdatamodell bör gravitate för det här mönstret.

Det finns dock scenarier där refererar till en egenskap kan ge fördelar. Till exempel: om egenskapen refererade uppdateras ofta. Med hjälp av ett separat hörn som representerar en egenskap som ändras hela tiden kan minska mängden skrivåtgärder som uppdateringen kräver.

### <a name="relationship-modeling-with-edge-directions"></a>Relation modellering med edge riktningar

När hörn modelleras kan kanter läggas till anger relationerna mellan dem. Den första delen som måste utvärderas är den **riktningen i relationen**. 

Edge-objekt har en Standardriktning som följs av en genomgång av när du använder den `out()` eller `outE()` funktion. Med den här naturlig riktning resulterar i en effektiv åtgärd, eftersom alla hörn lagras med sina utgående kanter. 

Dock gå igenom i motsatt riktning för en gräns, med hjälp av den `in()` fungera, leder alltid en fråga över partitioner. Läs mer om [grafpartitionering](graph-partitioning.md). Om du behöver en ständigt bläddra med hjälp av den `in()` funktion, rekommenderar vi att lägga till kanter i båda riktningarna.

Du kan fastställa edge-riktning genom att använda den `.to()` eller `.from()` predikat till den `.addE()` Gremlin-steg. Eller genom att använda den [BulkExecutor-biblioteket för Gremlin-API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Edge-objekt har en riktning som standard.

### <a name="relationship-labeling"></a>Relation märkning

Använda beskrivande relationsetiketter kan förbättra effektiviteten i edge upplösning verksamhet. Det här mönstret kan tillämpas på följande sätt:
* Använd icke-generisk termer för att märka en relation.
* Associera etiketten för källvertex till etiketten för mål-hörn med relationsnamnet.

![Relationen märkning exempel.](./media/graph-modeling/graph-modeling-3.png)

Ju mer specifika etiketten som traverser använder för att filtrera kanter, desto bättre. Det här beslutet kan ha en betydande inverkan på samt kostnaden för frågan. Du kan utvärdera kostnaden frågan när som helst [med hjälp av steget executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Nästa steg: 
* Kolla in listan över stöds [Gremlin-steg](gremlin-support.md).
* Lär dig mer om [graph Databaspartitionering](graph-partitioning.md) utan storskaliga diagram.
* Utvärdera dina Gremlin-frågor med hjälp av den [körning profil steg](graph-execution-profile.md).
