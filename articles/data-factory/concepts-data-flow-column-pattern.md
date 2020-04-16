---
title: Kolumnmönster i Azure Data Factory-mappningsdataflöde
description: Skapa generaliserade dataomvandlingsmönster med hjälp av kolumnmönster i Azure Data Factory-mappningsdataflöden
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 5e1c0eb1c4f1fc74e56db8f83f1bab6406ec5306
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415556"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Använda kolumnmönster vid mappning av dataflöde

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Med flera mappningsdataflödesomvandlingar kan du referera till mallkolumner baserat på mönster i stället för hårdkodade kolumnnamn. Den här matchningen kallas *kolumnmönster*. Du kan definiera mönster som matchar kolumner baserat på namn, datatyp, ström eller position i stället för att kräva exakta fältnamn. Det finns två scenarier där kolumnmönster är användbara:

* Om inkommande källfält ändras ofta, till exempel om du ändrar kolumner i textfiler eller NoSQL-databaser. Det här scenariot kallas [schemadrift](concepts-data-flow-schema-drift.md).
* Om du vill göra en gemensam åtgärd på en stor grupp kolumner. Om du till exempel vill casta varje kolumn som har "total" i kolumnnamnet till en dubbel.

Kolumnmönster är för närvarande tillgängliga i de härledda kolumn-, mängd-, select- och sinkomvandlingarna.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Kolumnmönster i härledd kolumn och aggregat

Om du vill lägga till ett kolumnmönster i en härledd kolumn eller fliken Aggregerade för en mängdomvandling klickar du på plusikonen till höger om en befintlig kolumn. Välj **Lägg till kolumnmönster**. 

![kolumnmönster](media/data-flow/columnpattern.png "Kolumnmönster")

Använd [uttrycksverktyget](concepts-data-flow-expression-builder.md) för att ange matchningsvillkoret. Skapa ett booleiskt uttryck som `name` `type`matchar `stream`kolumner `position` baserat på kolumnen , , och. Mönstret påverkar alla kolumner, drifted eller definierade, där villkoret returnerar sant.

De två uttrycksrutorna under matchningsvillkoret anger de nya namnen och värdena för de berörda kolumnerna. Används `$$` för att referera till det befintliga värdet för det matchade fältet. Rutan vänster uttryck definierar namnet och rutan för höger uttryck definierar värdet.

![kolumnmönster](media/data-flow/columnpattern2.png "Kolumnmönster")

Kolumnmönstret ovan matchar varje kolumn av typen dubbel och skapar en mängdkolumn per matchning. Namnet på den nya kolumnen är den matchade kolumnens namn som sammanfogats med "_total". Värdet för den nya kolumnen är den avrundade, aggregerade summan av det befintliga dubbla värdet.

Om du vill kontrollera att matchningsvillkoret är korrekt kan du validera utdataschemat för definierade kolumner på fliken **Granska** eller få en ögonblicksbild av data på fliken **Dataförhandsgranskning.** 

![kolumnmönster](media/data-flow/columnpattern3.png "Kolumnmönster")

## <a name="rule-based-mapping-in-select-and-sink"></a>Regelbaserad mappning i välj och sänk

När du mappar kolumner i käll och väljer omvandlingar kan du lägga till antingen fasta mappningar eller regelbaserade mappningar. Matcha baserat `name`på `type` `stream`kolumnerna `position` , , och. Du kan ha valfri kombination av fasta och regelbaserade mappningar. Som standard kommer alla prognoser med större än 50 kolumner som standard att en regelbaserad mappning som matchar på varje kolumn och matar ut det indataade namnet. 

Om du vill lägga till en regelbaserad mappning klickar du på **Lägg till mappning** och väljer **Regelbaserad mappning**.

![regelbaserad mappning](media/data-flow/rule2.png "Regelbaserad mappning")

Varje regelbaserad mappning kräver två indata: villkoret som ska matchas efter och vad som ska namnges varje mappad kolumn. Båda värdena matas in via [uttrycksverktyget](concepts-data-flow-expression-builder.md). Ange ditt booleska matchningsvillkor i rutan vänster uttryck. I rutan för rätt uttryck anger du vad den matchade kolumnen ska mappas till.

![regelbaserad mappning](media/data-flow/rule-based-mapping.png "Regelbaserad mappning")

Använd `$$` syntaxen för att referera till indatanamnet för en matchad kolumn. Använd ovanstående bild som exempel, säg att en användare vill matcha på alla strängkolumner vars namn är kortare än sex tecken. Om en inkommande `test`kolumn har `$$ + '_short'` fått namnet `test_short`byter uttrycket namn på kolumnen . Om det är den enda mappningen som finns, kommer alla kolumner som inte uppfyller villkoret att tas bort från utdata.

Mönster matchar både drivoch definierade kolumner. Om du vill se vilka definierade kolumner som mappas av en regel klickar du på glasögonikonen bredvid regeln. Verifiera utdata med hjälp av förhandsgranskning av data.

### <a name="regex-mapping"></a>Regex-mappning

Om du klickar på ikonen nedåt kan du ange ett villkor för regex-mappning. Ett regex-mappningsvillkor matchar alla kolumnnamn som matchar det angivna regex-villkoret. Detta kan användas i kombination med standardregelbaserade mappningar.

![regelbaserad mappning](media/data-flow/regex-matching.png "Regelbaserad mappning")

Exemplet ovan matchar på `(r)` regex-mönstret eller ett kolumnnamn som innehåller ett gemener r. I likhet med standardregelbaserad mappning ändras alla matchade kolumner `$$` av villkoret till höger med syntaxen.

### <a name="rule-based-hierarchies"></a>Regelbaserade hierarkier

Om den definierade projektionen har en hierarki kan du använda regelbaserad mappning för att mappa underkolumnerna för hierarkier. Ange ett matchande villkor och den komplexa kolumn vars underkolumner du vill mappa. Varje matchad underkolumn matas ut med regeln "Namn som" som anges till höger.

![regelbaserad mappning](media/data-flow/rule-based-hierarchy.png "Regelbaserad mappning")

Exemplet ovan matchar på alla underkolumner i komplex kolumn `a`. `a`innehåller två `b` underkolumner `c`och . Utdataschemat kommer `b` att `c` innehålla två kolumner och `$$`som villkoret "Namn som" är .

## <a name="pattern-matching-expression-values"></a>Mönstermatchningsuttrycksvärden.

* `$$`översätter till namnet eller värdet för varje matchning vid körning
* `name`representerar namnet på varje inkommande kolumn
* `type`representerar datatypen för varje inkommande kolumn
* `stream`representerar namnet som är associerat med varje ström, eller omvandling i flödet
* `position`är ordinal position för kolumner i ditt dataflöde

## <a name="next-steps"></a>Nästa steg
* Läs mer om språk för att mappa [dataflödesuttryck](data-flow-expression-functions.md) för dataomvandlingar
* Använd kolumnmönster i [sink-omformningen](data-flow-sink.md) och [välj omformning](data-flow-select.md) med regelbaserad mappning
