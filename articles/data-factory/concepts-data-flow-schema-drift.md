---
title: Azure Data Factory mappning Data Flow schemat Drift
description: Skapa elastiska Data flödar i Azure Data Factory med schemat Drift
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 5799386b3ed725c610569caae8dfc72796c654b9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213657"
---
# <a name="azure-data-factory-mapping-data-flow-schema-drift"></a>Azure Data Factory mappning Data Flow schemat Drift

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Begreppet Schema Drift gäller där dina källor ofta ändrar metadata. Fält, kolumner, typer, etc. kan har lagts till, tas bort eller ändras direkt. Utan hantering för schemat Drift blir ditt dataflöde sårbara för ändringar i överordnade ändringar av datakällan. När inkommande kolumner och fält ändras, inte typisk ETL-mönster eftersom de tenderar att vara bundna till dessa namn på datakällor.

För att skydda mot schemat Drift, är det viktigt att ha anläggningarna i ett dataflöde verktyg så att du, som en Data-tekniker kan:

* Definiera källor som har föränderliga fältnamn, datatyper, värden och storlekar
* Definiera omvandling parametrar som fungerar med datamönster i stället för hårdkodade fält och värden
* Definiera uttryck som förstår för mönster så att de matchar inkommande fält, istället för att använda med namnet fält

I Azure Data Factory dataflöde exponeras dessa anläggningar via det här arbetsflödet:

* Välj ”Tillåt schemat Drift” i käll-transformering

<img src="media/data-flow/schemadrift001.png" width="400">

* När du har valt det här alternativet kan alla inkommande fält ska läsas från källan på varje dataflöde körning och kommer att skickas via hela flödet till mottagaren.

* Se till att använda ”automatisk mappning” för att mappa alla nya fält i transformeringen mottagare så att alla nya fält hämta plockas upp och landat på ditt mål:

<img src="media/data-flow/automap.png" width="400">

* Allt fungerar när nya fält introduceras i det här scenariot med en enkel käll -> mottagare (även kallat kopiera) mappning.

* Om du vill lägga till transformeringar i det här arbetsflödet som hanterar schemat drift, kan du använda mönstermatchning för att matcha kolumner efter namn, typ och värde.

* Klicka på ”Lägg till kolumn från” i den härledda kolumnen eller sammanställd omvandling om du vill skapa en transformering som förstår ”Schema Drift”.

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Du måste göra ett arkitekturbeslut i ditt dataflöde för att acceptera schemat drift i hela ditt flöde. När du gör detta kan du skydda mot schemaändringar från källor. Dock förlorar du tidig bindning för dina kolumner och typer i hela ditt dataflöde. Azure Data Factory behandlar schemat drift flöden som sen bindning flöden, så när du skapar dina transformeringar kolumnnamnen inte tillgängliga i vyerna schemat i hela flödet.

<img src="media/data-flow/taxidrift1.png" width="400">

I exemplet Taxi Demo dataflödet finns ett exempel schemat Drift i dataflödet längst ned med TripFare källan. Observera att vi använder ”mönster för kolumnen” för fälten aggregering i sammanställd transformeringen. I stället för namngivning av specifika kolumner eller söker efter kolumner efter position, förutsätter vi att data kan ändra och visas inte i samma ordning mellan körningar.

I det här exemplet på Azure Data Factory-dataflöde schemat drift hantering av vi har byggt och aggregering som söker efter kolumner av typen double, att känna till att data domänen innehåller priser för varje resa. Vi kan sedan utföra en sammanställd matematiska beräkning över alla dubbla fält i källan, oavsett där kolumnen finns och oavsett namngivning av kolumnen.

Azure Data Factory-dataflöde syntaxen använder $ för varje matchande kolumn från matchande mönstret. Du kan också matcha på kolumnnamn med komplexa sträng sökning och reguljära uttryck. I det här fallet ska vi skapa ett nytt aggregerade fältnamn baserat på varje matchning av en ”dubbel” typen för kolumnen och lägga till texten ```_total``` till var och en av de matchade namn: 

```concat($$, '_total')```

Vi kommer sedan avrunda och summera värdena för var och en av de matchade kolumnerna:

```round(sum ($$))```

Du kan testa den här ut med Azure Data Factory-dataflöde exemplet ”Taxi Demo”. Växla för Debug-sessionen med hjälp av växlingsknappen Debug överst på designytan dataflöde så att du kan se resultaten interaktivt:

<img src="media/data-flow/taxidrift2.png" width="800">

