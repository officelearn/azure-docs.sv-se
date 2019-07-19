---
title: Azure Data Factory mappning av data flödets schema avvikelse
description: Bygg elastiska data flöden i Azure Data Factory med schema avvikelse
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 562daa024985a546ffb49c4da11eace3bc81a659
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314809"
---
# <a name="mapping-data-flow-schema-drift"></a>Mappa data flöde schema avvikelse

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Begreppet schema avvikelse är det fall där dina källor ofta ändrar metadata. Fält, kolumner, typer osv. kan läggas till, tas bort eller ändras i farten. Utan hantering av schema avvikelser blir ditt data flöde sårbart för ändringar i överordnade data käll ändringar. Vanliga ETL-mönster fungerar inte när inkommande kolumner och fält ändras, eftersom de tenderar att vara knutna till dessa käll namn.

För att skydda mot schema drift är det viktigt att du har funktioner i ett data flödes verktyg som gör det möjligt för dig som en data tekniker att:

* Definiera källor som har föränderligt fält namn, data typer, värden och storlekar
* Definiera omvandlings parametrar som kan användas med data mönster i stället för hårdkodade fält och värden
* Definiera uttryck som förstår mönster för att matcha inkommande fält, i stället för att använda namngivna fält

## <a name="how-to-implement-schema-drift-in-adf-mapping-data-flows"></a>Implementera schema avvikelse i data flöden för ADF-mappning
ADF har inbyggt stöd för flexibla scheman som ändras från körning till körning så att du kan bygga Generic data Transformation Logic utan att behöva kompilera om dina data flöden.

* Välj "Tillåt schema avvikelse" i din käll omvandling

<img src="media/data-flow/schemadrift001.png" width="400">

* När du har valt det här alternativet kommer alla inkommande fält att läsas från källan vid varje data flödes körning och skickas genom hela flödet till mottagaren.

* Alla nyligen identifierade kolumner (invisade kolumner) kommer att tas som standard som sträng data typ. I din käll omvandling väljer du härledda kolumn typer om du vill att ADF automatiskt ska härleda data typer från källan.

* Se till att använda "Auto-Map" för att mappa alla nya fält i Sink-omvandlingen så att alla nya fält hämtas och landas på ditt mål och ange "Tillåt schema avvikelse" på mottagaren.

<img src="media/data-flow/automap.png" width="400">

* Allt fungerar när nya fält introduceras i det scenariot med en enkel mappning för källa-> mottagare (kopia).

* Om du vill lägga till omvandlingar i det arbets flödet som hanterar schema avvikelser kan du använda mönster matchning för att matcha kolumner efter namn, typ och värde.

* Klicka på Lägg till kolumn mönster i den härledda kolumnen eller mängd omvandlingen om du vill skapa en omvandling som förstår "schema avvikelse".

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Du måste fatta ett arkitektur beslut i ditt data flöde för att acceptera schema avvikelser i flödet. När du gör detta kan du skydda dig mot schema ändringar från källorna. Du kommer dock att förlora tidig bindning av dina kolumner och typer i ditt data flöde. Azure Data Factory behandlar schema avvikelse flöden som sena bindnings flöden, så när du skapar dina omvandlingar är kolumn namnen inte tillgängliga i schema vyerna i hela flödet.

<img src="media/data-flow/taxidrift1.png" width="400">

I data flödet för taxi demonstrations exemplet finns det ett exempel på schema i det nedre data flödet med TripFare-källan. I den sammanställda omvandlingen ser vi att vi använder designen "kolumn mönster" för agg regerings fälten. I stället för att namnge vissa kolumner eller leta efter kolumner efter position antar vi att data kan ändras och att de inte visas i samma ordning mellan körningarna.

I det här exemplet Azure Data Factory hantering av data flödes schema, har vi skapat och agg regering som söker efter kolumner av typen Double, och vet att data domänen innehåller priser för varje resa. Vi kan sedan utföra en sammanställd matematik beräkning i alla dubbla fält i källan, oavsett var kolumnen finns och oavsett kolumnens namn.

I syntaxen för Azure Data Factory-dataflöde används $ $ för att representera varje matchad kolumn från matchnings mönstret. Du kan också matcha i kolumn namn med hjälp av komplexa Strängs ökningar och reguljära uttrycks funktioner. I det här fallet ska vi skapa ett nytt sammanställt fält namn baserat på varje matchning av en kolumn av typen "dubbel" och lägga till texten ```_total``` i vart och ett av de matchade namnen: 

```concat($$, '_total')```

Sedan kommer vi att avrunda och summera värdena för var och en av de matchade kolumnerna:

```round(sum ($$))```

Du kan testa detta med Azure Data Factory data flödes exempel "taxi demonstration". Växla till felsökningssessionen med fel söknings funktionen och växla längst upp i design ytan för data flödet så att du kan se dina resultat interaktivt:

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Komma åt nya kolumner, underordnade
När du genererar nya kolumner med kolumn mönster, kan du komma åt dessa nya kolumner senare i dina data flödes omvandlingar med följande metoder:

* Använd "byPosition" för att identifiera de nya kolumnerna efter positions nummer.
* Använd "byName" för att identifiera de nya kolumnerna efter deras namn.
* I kolumn mönster använder du "name", "Stream", "position" eller "Type" eller någon kombination av dem för att matcha nya kolumner.

## <a name="next-steps"></a>Nästa steg
I [språket Data Flow-uttryck](data-flow-expression-functions.md) hittar du ytterligare funktioner för kolumn mönster och schema avvikelser, inklusive "byName" och "byPosition".
