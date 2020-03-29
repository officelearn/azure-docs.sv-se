---
title: Grafdatamodellering för Azure Cosmos DB Gremlin API
description: Lär dig hur du modellerar en diagramdatabas med hjälp av Azure Cosmos DB Gremlin API. I den här artikeln beskrivs när du ska använda en diagramdatabas och metodtips för att modellera entiteter och relationer.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: lbosq
ms.openlocfilehash: dc9a5616aa2bb1f7e09045b9cfe4f4d7e9c69be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898313"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Grafdatamodellering för Azure Cosmos DB Gremlin API

Följande dokument är utformat för att ge rekommendationer för grafdatamodellering. Det här steget är avgörande för att säkerställa skalbarhet och prestanda för ett grafdatabassystem när data utvecklas. En effektiv datamodell är särskilt viktig med storskaliga grafer.

## <a name="requirements"></a>Krav

Den process som beskrivs i den här guiden baseras på följande antaganden:
 * **Entiteterna** i problemutrymmet identifieras. Dessa entiteter är avsedda att konsumeras _atomärt_ för varje begäran. Med andra ord är databassystemet inte utformat för att hämta en enda entitets data i flera frågebegäranden.
 * Det finns en förståelse för **läs- och skrivkrav** för databassystemet. Dessa krav kommer att vägleda de optimeringar som behövs för grafdatamodellen.
 * Principerna för [Apache Tinkerpop egendom graf standard](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) är väl förstådda.

## <a name="when-do-i-need-a-graph-database"></a>När behöver jag en grafdatabas?

En grafdatabaslösning kan användas optimalt om entiteterna och relationerna i en datadomän har någon av följande egenskaper: 

* Entiteterna är **starkt sammankopplade** genom beskrivande relationer. Fördelen i det här scenariot är det faktum att relationerna sparas i lagring.
* Det finns **cykliska relationer** eller **självrefereratiteter**. Det här mönstret är ofta en utmaning när du använder relations- eller dokumentdatabaser.
* Det finns **dynamiskt föränderliga relationer** mellan entiteter. Det här mönstret är särskilt tillämpligt på hierarkiska eller trädstrukturerade data med många nivåer.
* Det finns **många-till-många-relationer** mellan entiteter.
* Det finns **skriv- och läskrav på både entiteter och relationer**. 

Om ovanstående villkor är uppfyllda är det troligt att en diagramdatabasmetod ger fördelar för **frågekomplexitet,** **datamodellskalbarhet**och **frågeprestanda**.

Nästa steg är att avgöra om diagrammet kommer att användas för analytiska eller transaktionella ändamål. Om diagrammet är avsett att användas för tunga beräknings- och databehandlingsarbetsbelastningar, skulle det vara värt att utforska [Cosmos DB Spark-kontakten](https://docs.microsoft.com/azure/cosmos-db/spark-connector) och användningen av [GraphX-biblioteket](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Så här använder du grafobjekt

[Egenskapsdiagramet Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) definierar två typer av objekt **Hörn** och **kanter**. 

Följande är de bästa metoderna för egenskaperna i diagramobjekten:

| Objekt | Egenskap | Typ | Anteckningar |
| --- | --- | --- |  --- |
| Vertex | ID | String | Unikt verkställs per partition. Om ett värde inte anges vid insättningen lagras ett automatiskt genererat GUID. |
| Vertex | etikett | String | Den här egenskapen används för att definiera vilken typ av entitet som vertex representerar. Om ett värde inte anges används ett standardvärde "vertex". |
| Vertex | properties | Sträng, Boolean, Numerisk | En lista över separata egenskaper som lagras som nyckelvärdespar i varje hörn. |
| Vertex | partitionsnyckel | Sträng, Boolean, Numerisk | Den här egenskapen definierar var hörnet och dess utgående kanter ska lagras. Läs mer om [grafpartitionering](graph-partitioning.md). |
| Edge | ID | String | Unikt verkställs per partition. Genereras automatiskt som standard. Kanter har vanligtvis inte behovet av att vara unikt hämtas av ett ID. |
| Edge | etikett | String | Den här egenskapen används för att definiera vilken typ av relation som två hörn har. |
| Edge | properties | Sträng, Boolean, Numerisk | En lista över separata egenskaper som lagras som nyckelvärdespar i varje kant. |

> [!NOTE]
> Kanter kräver inte ett partitionsnyckelvärde, eftersom dess värde tilldelas automatiskt baserat på deras källvertex. Läs mer i [artikeln om grafpartitionering.](graph-partitioning.md)

## <a name="entity-and-relationship-modeling-guidelines"></a>Riktlinjer för entitets- och relationsmodellering

Följande är en uppsättning riktlinjer för att närma sig datamodellering för en Azure Cosmos DB Gremlin API-grafdatabas. Dessa riktlinjer förutsätter att det finns en befintlig definition av en datadomän och frågor för den.

> [!NOTE]
> De steg som beskrivs nedan presenteras som rekommendationer. Den slutliga modellen bör utvärderas och testas innan den betraktas som produktionsklar. Dessutom är rekommendationerna nedan specifika för Azure Cosmos DB:s Gremlin API-implementering. 

### <a name="modeling-vertices-and-properties"></a>Modellering av hörn och egenskaper 

Det första steget för en diagramdatamodell är att mappa varje identifierad entitet till ett **hörnobjekt**. En en till en mappning av alla entiteter till hörn bör vara ett första steg och kan komma att ändras.

En vanlig fallgrop är att mappa egenskaper för en enda entitet som separata hörn. Tänk på exemplet nedan, där samma entitet representeras på två olika sätt:

* **Hörnbaserade egenskaper**: I den här metoden använder entiteten tre separata hörn och två kanter för att beskriva dess egenskaper. Även om den här metoden kan minska redundansen ökar modellens komplexitet. En ökning av modellens komplexitet kan resultera i ökad svarstid, frågekomplexitet och beräkningskostnad. Den här modellen kan också innebära utmaningar i partitionering.

![Entitetsmodell med hörn för egenskaper.](./media/graph-modeling/graph-modeling-1.png)

* **Egenskapsrelaterade hörn:** Den här metoden utnyttjar listan med nyckelvärdespar för att representera alla egenskaper för entiteten i ett hörn. Den här metoden ger minskad modellkomplexitet, vilket leder till enklare frågor och mer kostnadseffektiva traversals.

![Entitetsmodell med hörn för egenskaper.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Exemplen ovan visar en förenklad diagrammodell för att bara visa jämförelsen mellan de två sätten att dela entitetsegenskaper.

Egenskaps **inbäddat hörnmönster** ger i allmänhet en mer högpresterande och skalbar metod. Standardmetoden för en ny diagramdatamodell bör dras till det här mönstret.

Det finns dock scenarier där hänvisning till en egenskap kan ge fördelar. Till exempel: om den refererade egenskapen uppdateras ofta. Om du använder ett separat hörn för att representera en egenskap som ständigt ändras minimeras mängden skrivåtgärder som uppdateringen skulle kräva.

### <a name="relationship-modeling-with-edge-directions"></a>Relationsmodellering med kantriktningar

När hörnen har modellerats kan kanterna läggas till för att beteckna relationerna mellan dem. Den första aspekten som måste utvärderas är **riktningen för relationen**. 

Kantobjekt har en standardriktning som följs `out()` `outE()` av en traversal när du använder funktionen eller. Med hjälp av denna naturliga riktning resulterar i en effektiv operation, eftersom alla hörn lagras med sina utgående kanter. 

Om du korsar i motsatt riktning av `in()` en kant, med hjälp av funktionen, kommer det alltid att resultera i en fråga över partitionering. Läs mer om [grafpartitionering](graph-partitioning.md). Om det finns ett behov av att `in()` ständigt gå igenom med hjälp av funktionen, rekommenderas att lägga till kanter i båda riktningarna.

Du kan bestämma kantriktningen `.to()` `.from()` genom att använda `.addE()` eller predikat till Gremlin-steget. Eller genom att använda [massutnrevarbiblioteket för Gremlin API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Kantobjekt har en riktning som standard.

### <a name="relationship-labeling"></a>Relationsetikett

Om du använder beskrivande relationsetiketter kan du förbättra effektiviteten i kantupplösningsåtgärder. Det här mönstret kan användas på följande sätt:
* Använd icke-allmänna termer för att märka en relation.
* Associera etiketten för källvertexet med etiketten för målhöret med relationsnamnet.

![Exempel på relationsmärkning.](./media/graph-modeling/graph-modeling-3.png)

Ju mer specifik etiketten som traversern använder för att filtrera kanterna, desto bättre. Detta beslut kan ha en betydande inverkan på frågekostnaden också. Du kan utvärdera frågekostnaden när som helst [med hjälp av steget executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Nästa steg: 
* Kolla in listan över [gremlin-steg](gremlin-support.md)som stöds .
* Lär dig mer om [partitionering av grafdatabaser](graph-partitioning.md) för att hantera storskaliga diagram.
* Utvärdera dina Gremlin-frågor med hjälp av [steget Körningsprofil](graph-execution-profile.md).
