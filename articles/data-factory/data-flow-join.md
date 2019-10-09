---
title: Transformering av koppling till Azure Data Factory data flöde
description: Transformering av koppling till Azure Data Factory data flöde
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 48cf9d58c8acd85e545a5bcb5104d7069670e349
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029316"
---
# <a name="mapping-data-flow-join-transformation"></a>Mappa transformering av data flödes koppling



Använd Join för att kombinera data från två tabeller i ditt data flöde. Klicka på den omvandling som ska vara den vänstra relationen och Lägg till en kopplings omvandling från verktygs lådan. Inuti kopplings omvandlingen väljer du en annan data ström från ditt data flöde för att vara rätt relation.

![Koppla omvandlings](media/data-flow/join.png "koppling")

## <a name="join-types"></a>Kopplings typer

Det krävs ett urval av kopplings typ för kopplings omvandlingen.

### <a name="inner-join"></a>Inre koppling

Inre koppling passerar bara genom de rader som matchar kolumn villkoren från båda tabellerna.

### <a name="left-outer"></a>Vänster yttre

Alla rader från den vänstra strömmen som inte uppfyller kopplings villkoret skickas vidare och utdatakolumner från den andra tabellen anges till NULL förutom alla rader som returneras av den inre kopplingen.

### <a name="right-outer"></a>Höger yttre

Alla rader från den högra strömmen som inte uppfyller kopplings villkoret skickas vidare, och utdatakolumner som motsvarar den andra tabellen är inställt på NULL, förutom alla rader som returneras av den inre kopplingen.

### <a name="full-outer"></a>Fullständig yttre

Med fullständig yttre skapas alla kolumner och rader från båda sidor med NULL-värden för kolumner som inte finns i den andra tabellen.

### <a name="cross-join"></a>Kors koppling

Ange den kors produkten av de två strömmarna med ett uttryck. Du kan använda detta för att skapa anpassade kopplings villkor.

## <a name="specify-join-conditions"></a>Ange kopplings villkor

Det vänstra kopplings villkoret är från data strömmen som är ansluten till vänster om kopplingen. Det högra kopplings villkoret är den andra data strömmen som är ansluten till din koppling längst ned, som antingen är en direkt anslutning till en annan ström eller en referens till en annan ström.

Du måste ange minst 1 (1.. n) kopplings villkor. De kan vara fält som antingen refereras till direkt, väljs från den nedrullningsbara menyn eller uttryck.

## <a name="join-performance-optimizations"></a>Delta i prestanda optimering

Till skillnad från sammanfognings koppling i verktyg som SSIS, är sammanfogning i ADF-dataflöde inte en obligatorisk sammanfognings kopplings åtgärd. Kopplings nycklarna behöver därför inte sorteras först. Kopplings åtgärden sker baserat på den optimala kopplings åtgärden i Spark: Sändnings-/kart sidans anslutning:

![Koppla omvandling optimera](media/data-flow/joinoptimize.png "kopplings optimering")

Om din data uppsättning kan anpassas till arbetsnodens minne kan vi optimera dina kopplings prestanda. Du kan också ange partitionering av dina data i Join-åtgärden för att skapa uppsättningar med data som passar bättre i minnet per arbets plats.

## <a name="self-join"></a>Själv koppling

Du kan få självkopplings villkor i ADF-dataflödet genom att använda Välj omvandling för att ange alias för en befintlig data ström. Skapa först en "ny gren" från en data ström och Lägg sedan till en Välj för att ange alias för hela den ursprungliga strömmen.

![](media/data-flow/selfjoin.png "Själv") koppling

I diagrammet ovan är Select Transform överst. Allt det gör är att aliasa den ursprungliga strömmen till "OrigSourceBatting". I den markerade kopplings transformeringen nedan kan du se att vi använder den här valda Ali Aset som höger koppling, så att vi kan referera till samma nyckel i både den vänstra & högra sidan av den inre kopplingen.

## <a name="composite-and-custom-keys"></a>Sammansatta och anpassade nycklar

Du kan bygga anpassade och sammansatta nycklar direkt i Join-omvandlingen. Lägg till rader för ytterligare kopplings kolumner med plus tecknet (+) bredvid varje Relations rad. Eller beräkna ett nytt nyckel värde i uttrycks verktyget för ett kopplings värde på farten.

## <a name="next-steps"></a>Nästa steg

När du har anslutit till data kan du [skapa nya kolumner och lägga](data-flow-derived-column.md) [till data i ett mål data lager](data-flow-sink.md).
