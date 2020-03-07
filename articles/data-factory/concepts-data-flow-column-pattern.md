---
title: Kolumn mönster i Azure Data Factory mappa data flöde
description: Skapa generaliserade data omvandlings mönster med kolumn mönster i Azure Data Factory mappa data flöden
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395928"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Använda kolumn mönster i mappnings data flödet

Med flera mappnings data flödes omvandlingar kan du referera till mall-kolumner baserat på mönster i stället för hårdkodade kolumn namn. Den här matchningen kallas för *kolumn mönster*. Du kan definiera mönster för att matcha kolumner baserat på namn, datatyp, ström eller position i stället för att kräva exakta fält namn. Det finns två scenarier där kolumn mönster är användbara:

* Om inkommande käll fält ändras ofta, till exempel vid ändring av kolumner i textfiler eller NoSQL-databaser. Det här scenariot kallas [schema avvikelse](concepts-data-flow-schema-drift.md).
* Om du vill göra en gemensam åtgärd i en stor grupp kolumner. Om du till exempel vill omvandla varje kolumn som har "total" i dess kolumn namn till ett dubbelt värde.

Kolumn mönster är för närvarande tillgängliga i omvandlingarna härledd kolumn, mängd, Välj och mottagare.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Kolumn mönster i härledd kolumn och mängd

Om du vill lägga till ett kolumn mönster i en härledd kolumn eller fliken Aggregator i en mängd omvandling, klickar du på plus ikonen till höger om en befintlig kolumn. Välj **Lägg till kolumn mönster**. 

![kolumn mönster](media/data-flow/columnpattern.png "Kolumnmönster")

Använd [uttrycks verktyget](concepts-data-flow-expression-builder.md) för att ange matchnings villkor. Skapa ett booleskt uttryck som matchar kolumner baserat på `name`, `type`, `stream`och `position` för kolumnen. Mönstret påverkar alla kolumner, som anges eller definieras, där villkoret returnerar true.

De två uttrycks rutorna under matchnings villkoret anger de nya namnen och värdena för de berörda kolumnerna. Använd `$$` för att referera till det befintliga värdet för det matchade fältet. Rutan till vänster-uttryck definierar det namn och den högra resultat rutan som definierar värdet.

![kolumn mönster](media/data-flow/columnpattern2.png "Kolumnmönster")

Mönstret ovan matchar alla kolumner av typen Double och skapar en sammanställd kolumn per matchning. Namnet på den nya kolumnen är den matchade kolumnens namn sammanfogat med _total. Värdet för den nya kolumnen är den rundade, aggregerade summan av det befintliga dubbla värdet.

För att kontrol lera att ditt matchnings villkor är korrekt kan du validera schemat för definierade kolumner på fliken **Granska** eller hämta en ögonblicks bild av data på fliken **data förhands granskning** . 

![kolumn mönster](media/data-flow/columnpattern3.png "Kolumnmönster")

## <a name="rule-based-mapping-in-select-and-sink"></a>Regel baserad mappning i SELECT och Sink

När du mappar kolumner i källa och väljer omvandlingar kan du lägga till antingen fast mappning eller regelbaserade mappningar. Använd fast mappning om du känner till schemat för dina data och förväntar dig att vissa kolumner från käll data uppsättningen alltid matchar vissa statiska namn. Om du arbetar med flexibla scheman använder du regelbaserade mappningar för att bygga en mönster matchning baserat på `name`, `type`, `stream`och `position` av kolumner. Du kan ha en kombination av fasta och regelbaserade mappningar. 

Om du vill lägga till en regelbaserade mappning klickar du på **Lägg till mappning** och väljer **regel baserad mappning**.

![regel baserad mappning](media/data-flow/rule2.png "Regel baserad mappning")

Ange ditt booleska matchnings villkor i rutan till vänster-uttryck. I rutan till höger uttryck anger du vad den matchade kolumnen ska mappas till. Använd `$$` för att referera till det befintliga namnet på det matchade fältet.

Om du klickar på ikonen för nedåt kan du ange ett regex-mappnings villkor.

Klicka på ikonen glasögon bredvid en regel baserad mappning för att se vilka definierade kolumner som matchar och vad de är mappade till.

![regel baserad mappning](media/data-flow/rule1.png "Regel baserad mappning")

I exemplet ovan skapas två regelbaserade mappningar. Det första tar alla kolumner som inte heter "Movie" och mappar dem till befintliga värden. Den andra regeln använder regex för att matcha alla kolumner som börjar med "Movie" och mappar dem till kolumnen "movieId".

Om regeln resulterar i flera identiska mappningar aktiverar du **hoppa över duplicerade indata** eller **hoppar över dubbla utdata** för att förhindra dubbletter.

## <a name="pattern-matching-expression-values"></a>Mönster matchnings uttrycks värden.

* `$$` översätts till namnet eller värdet för varje matchning vid körnings tillfället
* `name` representerar namnet på varje inkommande kolumn
* `type` representerar data typen för varje inkommande kolumn
* `stream` representerar namnet som är kopplat till varje data ström eller omvandling i ditt flöde
* `position` är ordnings punkten för kolumner i ditt data flöde

## <a name="next-steps"></a>Nästa steg
* Läs mer om data flödes [uttrycks språket](data-flow-expression-functions.md) för data omvandlingar
* Använd kolumn mönster i [omvandling av mottagare](data-flow-sink.md) och [Välj omvandling](data-flow-select.md) med regelbaserade mappning
