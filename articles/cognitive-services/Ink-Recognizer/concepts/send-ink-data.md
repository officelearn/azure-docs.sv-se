---
title: Skicka ink-data till Ink Igenkännande API
titlesuffix: Azure Cognitive Services
description: 'Lär dig om att anropa API: et för pennanteckningar Analyzer för olika program'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 5a212c7332d085c15baabef8650572162c47903d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027098"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Skicka ink-data till Ink Igenkännande API 

Digitala pennanteckningar avser teknik som möjliggör digitala garantier för indata, till exempel handskrift och ritningar. Detta är vanligtvis uppnås med hjälp av en digitizer som samlar in förflyttningar av typ av inkommande enheter, till exempel en penna. När enheter fortsätter att aktivera rika digitala upplevelser för digital penna, kan artificiell intelligens och machine learning igenkänning av skriftliga former och text i en kontext. Ink Igenkännande API kan du skicka ink linjer och få detaljerad information om dem. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Ink Igenkännande API vs. OCR-tjänster

Ink Igenkännande API använder inte optiska tecken Recognition(OCR). OCR tjänster bearbeta pixeldata från bilder för att tillhandahålla igenkänning av handskrift och text. Detta kallas ibland offline erkännande. I stället kräver Ink Igenkännande API digitala ink linje data som samlas in enheten används. Bearbetning av digitala ink data på det här sättet kan producera mer exakta resultat jämfört med OCR-tjänster. 

## <a name="sending-ink-data"></a>Skicka ink-data

Ink Igenkännande API kräver X- och Y-koordinaterna som representerar ink-linjer som skapats av en enhet, från det ögonblick då det vidrör identifiering yta till när den hävs. Punkterna i varje linje måste vara en sträng med kommateckenavgränsade värden och formateras i JSON som exemplet nedan. Varje ink-linje måste dessutom ha ett unikt ID i varje begäran. Om ID upprepas inom samma begäran och returnerar API: et ett fel. För bästa resultat för taligenkänning, bestå av minst åtta siffror efter decimaltecknet. Arbetsytan ursprung (0,0) antas vara det övre vänstra hörnet av digital penna arbetsytan.

> [!NOTE]
> I följande exempel är inte giltig JSON. Du hittar en fullständig Ink Igenkännande JSON-begäran på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
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

## <a name="ink-recognizer-response"></a>Ink Igenkännande svar

Ink Igenkännande API: et returnerar ett analysis-svar om objekt som det känns igen från ink-innehåll. Svaret innehåller igenkänning av enheter som beskriver relationerna mellan olika ink linjer. Till exempel finns linjer som skapar distinkta, separata former i olika enheter. Varje enhet innehåller detaljerad information om dess ink linjer som det identifierade objektet, dess koordinater och andra attribut för ritningen.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Former som identifieras av Ink Igenkännande API

Ink Igenkännande API kan identifiera de vanligaste formerna i anteckningar. I bilden nedan visas några grundläggande exempel. En fullständig lista över former och andra ink innehåll kan identifieras av API: et finns i den [API referensartikeln](https://go.microsoft.com/fwlink/?linkid=2089907). 

![Listan över former som identifieras av Ink Igenkännande API](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Rekommenderade anropande mönster

Du kan anropa Ink Igenkännande REST API i olika mönster enligt ditt program. 

### <a name="user-initiated-api-calls"></a>Användarinitierad API-anrop

Om du bygger en app som samlar in användarinformation (till exempel Observera att möjliggöra eller anteckning app), kan du ge dem kontroll över när och vilka ink skickas till Ink Igenkännande API: et. Den här funktionen är särskilt användbart när text och former är båda finns på arbetsytan och användare vill utföra olika åtgärder för var och en. Överväg att lägga till val av funktioner (till exempel en lasso eller andra geometriska markeringsverktyget) som gör att användarna kan välja vad som skickas till API: et.  

### <a name="app-initiated-api-calls"></a>Appen initieras API-anrop

Du kan också ha dina anrop Ink Igenkännande API när du har en tidsgräns. Du kan lagra resultat som de skapas samtidigt förbättra svarstiden för API: er genom att skicka aktuella ink linjer-API: et regelbundet. Du kan exempelvis skicka en verksamhetsspecifik handskriven text-API: et när du identifierar dina användare så att den har slutförts. 

Med igenkänning av resultaten i förväg innehåller information om egenskaperna för pennanteckningar linjer som är relaterade till varandra. Till exempel vilka linjer grupperas för att skapa samma ord, rad, lista, punkt eller formen. Den här informationen kan förbättra din app ink val av funktioner genom att välja grupper av linjer samtidigt, till exempel.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrera Ink Igenkännande API med Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) erbjuder verktyg och tekniker för att aktivera digitala digital penna upplevelser på flera olika för olika enheter. Du kan kombinera Windows Ink-plattform med Ink Talarigenkänning att skapa program som visas och tolka digitala ink linjer.

## <a name="next-steps"></a>Nästa steg

* [Vad är Ink Igenkännande API?](../overview.md)
* [Ink Igenkännande REST API-referens](https://go.microsoft.com/fwlink/?linkid=2089907)

* Börja skicka digitala ink linje data med hjälp av:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)