---
title: Diagram data modellering för Azure Cosmos DB Gremlin-API
description: Lär dig att modellera en diagram databas med Cosmos DB Gremlin API.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: 2bd8c07b384872f3107b5938380cea4c8eb0abae
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559118"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Diagram data modellering för Azure Cosmos DB Gremlin-API

Följande dokument är utformat för att ge rekommendationer för diagram data modellering. Det här steget är viktigt för att säkerställa skalbarheten och prestandan i ett diagram system som data utvecklas. En effektiv data modell är särskilt viktig med storskaliga diagram.

## <a name="requirements"></a>Krav

Den process som beskrivs i den här guiden baseras på följande antaganden:
 * **Entiteterna** i problem området identifieras. Dessa entiteter är avsedda att förbrukas _atomict_ för varje begäran. Med andra ord är databas systemet inte utformat för att hämta en enskild entitets data i flera fråge förfrågningar.
 * Det finns en förståelse för **Läs-och skriv krav** för databas systemet. Dessa krav vägleder de optimeringar som krävs för diagram data modellen.
 * Principerna för [Apache Tinkerpop-egenskapen Graph standard](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) är väl förstå.

## <a name="when-do-i-need-a-graph-database"></a>När behöver jag en graf-databas?

En diagram databas lösning kan tillämpas optimalt om entiteterna och relationerna i en data domän har någon av följande egenskaper: 

* Entiteterna är **mycket anslutna** via beskrivande relationer. Fördelen med det här scenariot är att relationerna är bestående av lagring.
* Det finns **cykliska relationer** eller **självrefererade entiteter**. Det här mönstret är ofta en utmaning när du använder Relations-eller dokument databaser.
* Det finns **dynamiskt växande relationer** mellan entiteter. Det här mönstret gäller särskilt för hierarkiska eller träd strukturerade data med många nivåer.
* Det finns **många-till-många-relationer** mellan entiteter.
* Det finns **Skriv-och Läs krav för både entiteter och relationer**. 

Om ovanstående villkor är uppfyllt är det troligt att en diagram databas metod ger fördelar för att **fråga komplexitet**, **skalbarhet för data modeller**och **fråga om prestanda**.

Nästa steg är att avgöra om grafen ska användas för analys-eller transaktions syfte. Om grafen är avsedd att användas för tung beräkning och data bearbetnings arbets belastningar, skulle det vara värt att utforska [Cosmos DB Spark-anslutaren](https://docs.microsoft.com/azure/cosmos-db/spark-connector) och användningen av [Graphx-biblioteket](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Använda diagram objekt

[Apache Tinkerpop-egenskapen Graph standard](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) definierar två typer av objekt **hörn** och **kanter**. 

Här följer de rekommenderade metoderna för egenskaperna i graf-objekten:

| Objekt | Egenskap | Typ | Anteckningar |
| --- | --- | --- |  --- |
| Punkten | ID | Sträng | Används unikt per partition. Om ett värde inte anges vid infogning, lagras ett GUID som genereras automatiskt. |
| Punkten | etikett | Sträng | Den här egenskapen används för att definiera den typ av entitet som hörnen representerar. Om ett värde inte anges används ett standardvärde "hörn". |
| Punkten | properties | Sträng, boolesk, numerisk | En lista med separata egenskaper som lagras som nyckel/värde-par i varje hörn. |
| Punkten | partitionsnyckel | Sträng, boolesk, numerisk | Den här egenskapen definierar var form hörnet och dess utgående kanter ska lagras. Läs mer om [diagram partitionering](graph-partitioning.md). |
| Edge | ID | Sträng | Används unikt per partition. Automatiskt genererad som standard. Kanterna har vanligt vis inte behov av att hämtas unikt av ett ID. |
| Edge | etikett | Sträng | Den här egenskapen används för att definiera typen av relation som två hörn har. |
| Edge | properties | Sträng, boolesk, numerisk | En lista med separata egenskaper som lagras som nyckel/värde-par i varje gräns. |

> [!NOTE]
> Kanter kräver ingen partitionsnyckel, eftersom värdet tilldelas automatiskt baserat på deras käll-hörn. Läs mer i artikeln [diagram partitionering](graph-partitioning.md) .

## <a name="entity-and-relationship-modeling-guidelines"></a>Rikt linjer för entitet och Relations modellering

Följande är en uppsättning rikt linjer för att använda data modellering för en Azure Cosmos DB Gremlin API-graf. Dessa rikt linjer förutsätter att det finns en befintlig definition av en data domän och frågor för den.

> [!NOTE]
> Stegen som beskrivs nedan visas som rekommendationer. Den slutliga modellen bör utvärderas och testas innan den anses vara produktions klar. Rekommendationerna nedan är dessutom speciella för Azure Cosmos DB API-implementeringen Gremlin. 

### <a name="modeling-vertices-and-properties"></a>Modellerings hörn och egenskaper 

Det första steget för en diagram data modell är att mappa varje identifierad entitet till ett **hörn objekt**. En en-till-en-mappning av alla entiteter till hörn måste vara ett inledande steg och komma att ändras.

En gemensam Pitfall är att mappa egenskaper för en enskild entitet som separata hörn. Tänk på exemplet nedan, där samma entitet representeras på två olika sätt:

* **Hörnbaserade egenskaper**: i den här metoden använder entiteten tre separata hörn och två kanter för att beskriva dess egenskaper. Även om den här metoden kan minska redundansen ökar modell komplexiteten. En ökning av modell komplexiteten kan leda till ökad latens, fråga om komplexitet och beräknings kostnader. Den här modellen kan också presentera utmaningar vid partitionering.

![Enhets modell med hörn för egenskaper.](./media/graph-modeling/graph-modeling-1.png)

* **Egenskap – inbäddade hörn**: den här metoden utnyttjar nyckel värde par listan för att representera alla egenskaper för entiteten i ett hörn. Den här metoden ger minskad modell komplexitet, vilket leder till enklare frågor och kostnads effektiva bläddringskontroll.

![Enhets modell med hörn för egenskaper.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> I exemplen ovan visas en förenklad diagram modell som bara visar jämförelsen mellan de två sätten att dela egenskaper för entiteten.

Mönstret **-inbäddade hörn** mönster ger vanligt vis en mer bra och skalbar metod. Standard metoden för en ny diagram data modell ska gravitate mot det här mönstret.

Det finns dock scenarier där referenser till en egenskap kan ge fördelar. Exempel: om den refererade egenskapen uppdateras ofta. Om du använder ett separat hörn för att representera en egenskap som ändras ständigt minimeras mängden Skriv åtgärder som uppdateringen kräver.

### <a name="relationship-modeling-with-edge-directions"></a>Relations modellering med kant riktningar

När hörnen har modeller ATS kan kanterna läggas till för att beteckna relationerna mellan dem. Den första aspekt som måste utvärderas är **riktningen på relationen**. 

Kant objekt har en standard riktning som följs av en genom gång av funktionen `out()` eller `outE()`. Att använda den här naturliga riktningen resulterar i en effektiv åtgärd, eftersom alla formhörn lagras med sina utgående kanter. 

Att gå i motsatt riktning i en kant, med hjälp av funktionen `in()`, kommer dock alltid att resultera i en fråga för kors partitioner. Lär dig mer om [diagram partitionering](graph-partitioning.md). Om du behöver förflytta dig hela tiden med hjälp av funktionen `in()`, rekommenderar vi att du lägger till kanter i båda riktningarna.

Du kan bestämma kant riktningen genom att använda `.to()` eller `.from()` predikat i `.addE()` Gremlin-steget. Eller med hjälp av [bulk utförar-biblioteket för Gremlin-API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Kant objekt har en riktning som standard.

### <a name="relationship-labeling"></a>Relations etiketter

Om du använder beskrivande Relations etiketter kan du förbättra effektiviteten för gräns åtgärder. Det här mönstret kan tillämpas på följande sätt:
* Använd icke-generiska villkor för att namnge en relation.
* Koppla etiketten till käll Bryt punkten till etiketten för mål Bryt punkten med relations namnet.

![Exempel på Relations etiketter.](./media/graph-modeling/graph-modeling-3.png)

Den mer detaljerade etiketten som används för att filtrera kanterna, desto bättre. Det här beslutet kan också ha en betydande inverkan på frågans kostnad. Du kan när som helst utvärdera kostnad för frågan [med hjälp av executionProfile-steget](graph-execution-profile.md).


## <a name="next-steps"></a>Nästa steg: 
* Kolla in listan över Gremlin- [steg](gremlin-support.md)som stöds.
* Lär dig mer om [diagram databas partitionering](graph-partitioning.md) för att hantera storskaliga diagram.
* Utvärdera dina Gremlin-frågor med hjälp av [steget kör profil](graph-execution-profile.md).
