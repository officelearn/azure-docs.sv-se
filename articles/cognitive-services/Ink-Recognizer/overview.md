---
title: Vad är hand SKRIFTS igenkänning? – Ink-API för igenkänning
titleSuffix: Azure Cognitive Services
description: Integrera hand SKRIFTS tolken i dina program, webbplatser, verktyg och andra lösningar för att tillåta att Penn data identifieras och används som indata.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6c1a720e7e9bd9c71f925f104ca7fc70a1a5ef59
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89051074"
---
# <a name="what-is-the-ink-recognizer-api"></a>Vad är API för handskriftsigenkänning?

[!INCLUDE [ink-recognizer-deprecation](includes/deprecation-note.md)]

Hand SKRIFTS tolken är en molnbaserad REST API för att analysera och identifiera digitalt bläck innehåll. Till skillnad från tjänster som använder optisk tecken igenkänning (OCR) kräver API: n digitala Penn strecks data som indata. Digitala penndrag är tidsordnade uppsättningar av 2D-punkter (X, Y-koordinater) som representerar rörelsen i inmatnings verktyg som digitala pennor eller fingrar. Den identifierar sedan formerna och det handskrivna innehållet från inmatarna och returnerar ett JSON-svar som innehåller alla identifierade entiteter.

![Ett flödes schema som beskriver hur du skickar inmatningar för Penn streck till API: et](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Funktioner

Med bläck igenkännings-API: et kan du enkelt identifiera handskrivna innehåll i dina program. 

|Funktion  |Beskrivning  |
|---------|---------|
| Hand SKRIFTS igenkänning | Identifiera handskrivet innehåll på 63 Core- [språk och nationella inställningar](language-support.md). | 
| Layout igenkänning | Få strukturell information om innehållet i digitala pennan teckningar. Dela upp innehållet i att skriva regioner, stycken, rader, ord, punkt listor. Dina program kan sedan använda layoutinformation för att bygga fler funktioner, t. ex. automatisk List formatering och justering av form. |
| Form igenkänning | Identifiera de oftast använda [geometriska formerna](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) när du gör anteckningar. |
| Kombinerade former och text igenkänning | Identifiera vilka penndrag som tillhör former eller handskrivet innehåll, och klassificera dem separat.|

## <a name="workflow"></a>Arbetsflöde

Pennan tecknings tolkens API är en RESTful-webb tjänst som gör det enkelt att anropa från valfritt programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Efter registreringen:

1. Ta pennan tecknings data och [formatera dem](concepts/send-ink-data.md#sending-ink-data) i giltig JSON. API: n accepterar upp till 1500 penndrag per begäran. 
1. Skicka en begäran till hand SKRIFTS tolkens API med dina data.
1. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Prova en snabb start på följande språk för att börja ringa till Ink-API: et.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

Ta en titt på följande exempel program på GitHub för att se hur API: et för färg igenkänning fungerar i en digital inkungs app:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
