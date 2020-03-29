---
title: Skicka pennanteckning till API:et för handskriftsigenkänning
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att anropa Ink Analyzer API för olika program
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221110"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Skicka pennanteckning till API:et för handskriftsigenkänning 

Digitala pennanteckningar avser teknik som möjliggör digitala representationer av indata, till exempel handskrift och ritningar. Detta uppnås vanligtvis med hjälp av en digitaliserare som fångar rörelser indataenheter, till exempel en penna. När enheter fortsätter att aktivera omfattande användning av digitala pennanteckningar kan artificiell intelligens och maskininlärning möjliggöra igenkänning av skriftliga former och text i en kontext. Med API:et för pennanteckningspaneler kan du skicka pennstreck och få detaljerad information om dem. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>API:et för pennanteckningscentralen jämfört med OCR-tjänster

API:et för bläckast igenfärg använder inte optisk teckenigenkänning (OCR). OCR-tjänster bearbetar pixeldata från bilder för att ge handskrift och textigenkänning. Detta kallas ibland offlineigenkänning. I stället kräver INK Recognizer API digitala pennstrecksdata som fångas in när indataenheten används. Bearbetning av digitala bläckdata på detta sätt kan ge mer exakta igenkänningsresultat jämfört med OCR-tjänster. 

## <a name="sending-ink-data"></a>Skicka pennanteckningsdata

Api:et för bläckkonementorer kräver X- och Y-koordinaterna som representerar de pennstreck som skapas av en indataenhet, från det ögonblick den vidrör identifieringsytan till när den lyfts. Punkterna för varje linje måste vara en sträng med kommaavgränsade värden och formateras i JSON som exemplet nedan. Dessutom måste varje pennstreck ha ett unikt ID i varje begäran. Om ID:n upprepas i samma begäran returnerar API:et ett fel. För de mest exakta igenkänningsresultaten, ha minst åtta siffror efter decimalkommat. Arbetsytans ursprung (0,0) antas vara det övre vänstra hörnet av arbetsytan.

> [!NOTE]
> Följande exempel är inte giltigt JSON. Du hittar en fullständig Ink Recognizer JSON-begäran på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Svar på pennanteckningsre recognizeer

API:et för pennanteckningskonetenting returnerar ett analyssvar om de objekt som identifieras från bläckinnehållet. Svaret innehåller igenkänningsenheter som beskriver relationerna mellan olika pennstreck. Till exempel linjer som skapar distinkta, separata former kommer att finnas i olika enheter. Varje enhet innehåller detaljerad information om dess pennstreck, inklusive det tolkade objektet, dess koordinater och andra ritattribut.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Former som känns igen av API:et för bläckreformare

Api:et för pennanteckningsreprimerare kan identifiera de vanligaste formerna i anteckningar. Bilden nedan visar några grundläggande exempel. En fullständig lista över former och annat bläckinnehåll som känns igen av API:et finns i [API-referensartikeln](https://go.microsoft.com/fwlink/?linkid=2089907). 

![Listan över former som känns igen av API:et för bläckreformare](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Rekommenderade samtalsmönster

Du kan anropa INK Recognizer REST API i olika mönster beroende på ditt program. 

### <a name="user-initiated-api-calls"></a>Användarinitierade API-anrop

Om du skapar en app som tar in användaren (till exempel en antecknings- eller anteckningsapp) kanske du vill ge dem kontroll över när och vilken bläck som skickas till Ink Recognizer API. Den här funktionen är särskilt användbar när både text och former finns på arbetsytan och användarna vill utföra olika åtgärder för varje. Överväg att lägga till markeringsfunktioner (som en lasso eller annat geometriskt markeringsverktyg) som gör det möjligt för användare att välja vad som skickas till API:et.  

### <a name="app-initiated-api-calls"></a>Appinitierade API-anrop

Du kan också låta appen anropa Ink Recognizer API efter en timeout. Genom att skicka de aktuella pennstrecken till API:et rutinmässigt kan du lagra igenkänningsresultat när de skapas samtidigt som API:ets svarstid förbättras. Du kan till exempel skicka en rad handskriven text till API:et när du har upptäckt att användaren har slutfört den. 

Med erkännande resultat i förväg ger dig information om egenskaperna hos bläck slag som de avser varandra. Vilka linjer som grupperas för att till exempel bilda samma ord, rad, lista, stycke eller form. Den här informationen kan förbättra appens pennvalsfunktioner genom att till exempel kunna välja grupper av linjer samtidigt.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrera API:et för bläckmed Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) innehåller verktyg och tekniker för att möjliggöra digitala inking-upplevelser på en mängd olika enheter. Du kan kombinera Windows Ink-plattformen med INK Recognizer API för att skapa program som visar och tolkar digitala pennstreck.

## <a name="next-steps"></a>Nästa steg

* [Vad är API för handskriftsigenkänning?](../overview.md)
* [REST API-referens för pennanteckning](https://go.microsoft.com/fwlink/?linkid=2089907)

* Börja skicka data med digitala pennstreck med:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [Javascript](../quickstarts/javascript.md)
