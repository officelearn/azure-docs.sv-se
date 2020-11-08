---
title: Skicka pennanteckning till API:et för handskriftsigenkänning
titleSuffix: Azure Cognitive Services
description: Läs mer om hur du anropar Ink Analyzer API för olika program
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: aahi
ms.openlocfilehash: 9ecf3ad43b46d491d5f15270790251a0bc4e882e
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365371"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Skicka pennanteckning till API:et för handskriftsigenkänning 

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Digitala pennanteckningar avser teknik som möjliggör digitala representationer av indata, till exempel handskrift och ritningar. Detta uppnås vanligt vis med en digitaliserare som samlar in ingångs enheter, till exempel en penna. När enheter fortsätter att aktivera omfattande användning av digitala pennanteckningar kan artificiell intelligens och maskininlärning möjliggöra igenkänning av skriftliga former och text i en kontext. Med pennan tecknings-API kan du skicka penndrag och få detaljerad information om dem. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Bläck igenkännings-API jämfört med OCR-tjänster

Bläck igenkännings-API: t använder inte optisk tecken igenkänning (OCR). OCR-tjänster bearbetar pixel data från bilder för att tillhandahålla hand SKRIFTS-och text igenkänning. Detta kallas ibland för offline-igenkänning. I stället kräver API-identifieraren digitala Penn strecks data som samlas in som inmatnings enhet. Att bearbeta digitala pennan tecknings data på det här sättet kan ge mer exakta igenkännings resultat jämfört med OCR-tjänster. 

## <a name="sending-ink-data"></a>Skicka pennan tecknings data

Pennan tecknings-API: n kräver X-och Y-koordinaterna som representerar de penndrag som skapats av en inmatnings enhet, från det ögonblick som identifierings ytan vidrörs när den lyfts upp. Punkterna på varje linje måste vara en sträng med kommaavgränsade värden och vara formaterade i JSON som exemplet nedan. Dessutom måste varje penndrag ha ett unikt ID i varje begäran. Om ID upprepas inom samma begäran returnerar API: t ett fel. För de mest exakta igenkännings resultaten måste du ha minst åtta siffror efter decimal tecknet. Arbets ytans ursprung (0,0) antas vara det övre vänstra hörnet på arbets ytan för pennan teckningar.

> [!NOTE]
> Följande exempel är inte giltigt JSON. Du hittar en fullständig pennan tecknings-JSON-begäran på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
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

## <a name="ink-recognizer-response"></a>Hand SKRIFTS tolkens svar

Pennan tecknings-API: et returnerar ett analys svar om de objekt som identifieras av hand SKRIFTS innehållet. Svaret innehåller igenkännings enheter som beskriver relationerna mellan olika Penn streck. Linjer som skapar distinkta, separata former kommer att finnas i olika enheter. Varje enhet innehåller detaljerad information om dess penndrag, inklusive det identifierade objektet, dess koordinater och andra Drawing-attribut.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Former som identifieras av pennan tecknings tolkens API

Färg igenkännings-API: et kan identifiera de vanligaste formerna i Obs! I bilden nedan visas några grundläggande exempel. En fullständig lista över former och annat bläck innehåll som identifieras av API: et finns i [referens artikeln för API](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer). 

![Listan över former som identifieras av pennan tecknings tolkens API](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Rekommenderade anrops mönster

Du kan anropa hand SKRIFTS tolken REST API i olika mönster efter ditt program. 

### <a name="user-initiated-api-calls"></a>Användarinitierade API-anrop

Om du skapar en app som tar med användar indata (till exempel en anteckning-eller antecknings app), kanske du vill ge dem kontrollen av när och vilka pennan teckningar som skickas till pennan tecknings-API: et. Den här funktionen är särskilt användbar när text och former finns både på arbets ytan och användare vill utföra olika åtgärder för var och en. Överväg att lägga till markerings funktioner (till exempel ett lasso eller ett annat verktyg för geometrisk markering) som gör att användarna kan välja vad som ska skickas till API: et.  

### <a name="app-initiated-api-calls"></a>App-initierade API-anrop

Du kan också använda appen för att anropa API: et för hand SKRIFTS igenkänning efter en tids gräns. Genom att skicka de aktuella penndrag till API-rutinen kan du lagra igenkännings resultat när de skapas samtidigt som du förbättrar API: ns svars tid. Du kan till exempel skicka en rad med handskriven text till API: et när du har identifierat att användaren har slutfört den. 

Med igenkännings resultatet får du information om egenskaperna för penndrag när de relaterar till varandra. Till exempel vilka linjer grupperas för att bilda samma ord, linje, lista, stycke eller form. Den här informationen kan förbättra appens funktioner för pennan tecknings funktioner genom att kunna välja grupper av linjer på samma gång, till exempel.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrera pennan tecknings tolkens API med Windows Ink

[Windows Ink](/windows/uwp/design/input/pen-and-stylus-interactions) innehåller verktyg och tekniker som möjliggör digitala pennan tecknings upplevelser på flera olika enheter. Du kan kombinera Windows Ink-plattformen med pennan tecknings-API: et för att skapa program som visar och tolkar digitala Penn streck.

## <a name="next-steps"></a>Nästa steg

* [Vad är API för handskriftsigenkänning?](../overview.md)
* [Referens för hand SKRIFTS igenkänning REST API](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)

* Börja skicka digitala Penn strecks data med:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)