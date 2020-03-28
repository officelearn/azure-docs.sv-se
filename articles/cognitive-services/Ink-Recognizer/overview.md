---
title: Vad är Bläckre recognizer? - Bläck Recognizeer API
titleSuffix: Azure Cognitive Services
description: Integrera Bläckrepliken i dina program, webbplatser, verktyg och andra lösningar så att pennstrecksdata kan identifieras och användas som indata.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2d00a489af35f5960aaeb1dda7add5337d48d57d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448157"
---
# <a name="what-is-the-ink-recognizer-api"></a>Vad är API för handskriftsigenkänning?


Ink Recognizer Cognitive Service tillhandahåller ett molnbaserat REST API för att analysera och känna igen digitalt bläckinnehåll. Till skillnad från tjänster som använder OCR (Optical Character Recognition) kräver API:et data för digital bläckstreck som indata. Digitala pennstreck är tidsbeställda uppsättningar med 2D-punkter (X,Y-koordinater) som representerar rörelsen för inmatningsverktyg som digitala pennor eller fingrar. Den känner sedan igen formerna och det handskrivna innehållet från indata och returnerar ett JSON-svar som innehåller alla erkända entiteter.

![Ett flödesschema som beskriver att skicka en pennlinjeindata till API:et](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Funktioner

Med API:et för bläckrecenset kan du enkelt känna igen handskrivet innehåll i dina program. 

|Funktion  |Beskrivning  |
|---------|---------|
| Handskriftsigenkänning | Känn igen handskrivet innehåll på 63 grundläggande [språk och språk](language-support.md). | 
| Layoutigenkänning | Få strukturell information om innehållet med digitalt bläck. Dela upp innehållet i att skriva regioner, stycken, rader, ord, punktlistor. Programmen kan sedan använda layoutinformationen för att skapa ytterligare funktioner som automatisk listformatering och formjustering. |
| Formigenkänning | Känna igen de vanligaste [geometriska formerna](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) när du gör anteckningar. |
| Kombinerade former och textigenkänning | Identifiera vilka pennstreck som tillhör former eller handskrivet innehåll och klassificera dem separat.|

## <a name="workflow"></a>Arbetsflöde

Ink Recognizer API är en RESTful webbtjänst, vilket gör det enkelt att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Efter registreringen:

1. Ta dina pennanteckningsdata och [formatera dem](concepts/send-ink-data.md#sending-ink-data) till giltiga JSON.
1. Skicka en begäran till Ink Recognizer API med dina data.
1. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Prova en snabbstart på följande språk för att börja ringa till Ink Recognizer API.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [Javascript](quickstarts/javascript.md)

Om du vill se hur API:et för bläckigenkänning fungerar i en digital pennanteckningsapp kan du ta en titt på följande exempelprogram på GitHub:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
