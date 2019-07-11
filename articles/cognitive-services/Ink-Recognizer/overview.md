---
title: Vad är Ink Igenkännande? -Ink Igenkännande API
titleSuffix: Azure Cognitive Services
description: Integrera Ink-identifierare i dina program, webbplatser, verktyg och andra lösningar för att tillåta ink linje data att identifieras och användas som indata.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 95121523c510e72894086740ad340bdfd33f3c32
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721401"
---
# <a name="what-is-the-ink-recognizer-api"></a>Vad är API för handskriftsigenkänning?


Ink Igenkännande Cognitive Service tillhandahåller en molnbaserad REST API för att analysera och känna igen digitala ink-innehåll. Till skillnad från tjänster som använder optisk teckenläsning (OCR), kräver API: et digitala ink linje data som indata. Digitala ink linjer är en tidsordnad uppsättning 2D punkter (X, Y-koordinaterna) som representerar indata verktyg, till exempel digitala pennor eller fingrar rörelse. Den identifierar former och handskriven innehåll från indata och returnerar ett JSON-svar som innehåller alla identifierade entiteter.

![Ett flödesschema över skicka indata ink linje-API: et](media/ink-recognizer-pen-graph.png)

## <a name="features"></a>Funktioner

Med API: et för pennanteckningar Igenkännande beskrivande handskriven innehållet i dina program. 

|Funktion  |Beskrivning  |
|---------|---------|
| Igenkänning av handskrift | Identifiera handskriven innehåll i 63 core [språk och nationella inställningar](language-support.md). | 
| Igenkänning av layout | Hämta strukturinformation om digital ink-innehåll. Dela upp innehållet i skriva regioner, stycken, rader, ord, punktlistor. Dina program kan sedan använda layoutinformationen för att skapa ytterligare funktioner som automatisk lista formatering och forma justering. |
| Form-taligenkänning | Identifiera de mest använda [geometriska former](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) foton. |
| Kombinerade former och textigenkänning | Identifiera vilka ink linjer tillhör figurer eller handskriven innehåll och klassificera dem separat.|

## <a name="workflow"></a>Arbetsflöde

Ink Igenkännande API är en RESTful-webb-tjänst som gör det enkelt att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

När du registrerar dig:

1. Ta dina ink linje data och [formatera den](concepts/send-ink-data.md#sending-ink-data) i giltig JSON.
1. Skicka en begäran till Ink Igenkännande API med dina data.
1. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Testa en Snabbstart på följande språk att börja anrop till API: et för pennanteckningar Igenkännande.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/csharp.md)

Om du vill se hur den Ink-API: T fungerar i en digital digital penna app, ta en titt på de följande exempelprogram på GitHub:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
