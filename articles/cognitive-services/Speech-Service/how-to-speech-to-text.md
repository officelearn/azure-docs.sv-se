---
title: Använd tal till Text | Microsoft Docs
description: Lär dig hur du använder tal till Text i Speech-tjänsten
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 26cecedfc3ad2d472b9686e25054fe08253cee77
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068530"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>Använd ”tal till Text” i Speech-tjänsten

Du kan använda **tal till Text** i dina program på två olika sätt.

| Metod | Beskrivning |
|-|-|
| [SDK](speech-sdk.md) | Enklaste metoden för C/C++, C# och Java-utvecklare |
| [REST](rest-apis.md) | Identifiera kort yttranden med hjälp av en HTTP POST-begäran | 

## <a name="using-the-sdk"></a>Med SDK

Den [tal SDK](speech-sdk.md) ger det enklaste sättet att använda **tal till Text** i ditt program med full funktionalitet.

1. Skapa en tal-fabrik, vilket ger en prenumerationsnyckel för tal-tjänst och [region](regions.md) eller en autentiseringstoken. Du kan också konfigurera alternativ, till exempel språket eller en anpassad slutpunkt för din egen talmodeller taligenkänning, nu.

2. Få en identifierare från fabriken. Det finns tre olika typer av identifierare. Varje typ av identifierare kan använda din enhets standard mikrofon, en ljudström eller ljud från en fil.

    Igenkännande | Funktion
    -|-
    Taligenkänningens|Innehåller text avskrift tal
    Avsiktshantering Igenkännande|Härleds talare avsikt via [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) efter taligenkänning\*
    Översättning Igenkännande|Omvandlar den transkriberade texten till ett annat språk (se [Talöversättning](how-to-translate-speech.md))

    \* *Du måste använda en separat LUIS-prenumerationsnyckel när du skapar en tal-fabrik för avsiktlig Igenkännande för taligenkänning.*
    
4. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten. Annars kan får ditt program en slutlig avskrift resultatet.

5. Starta taligenkänning.
   Inleveransen erkännande som kommando eller en fråga erkännande, använda `RecognizeAsync()`, vilket returnerar den första uttryck som känns igen.
   Igenkänning av tidskrävande, som taltranskription, använda `StartContinuousRecognitionAsync()` och foga samman på händelser för asynkron igenkänningsresultat.

### <a name="sdk-samples"></a>SDK-exempel

Den senaste uppsättningen exempel, finns det [Cognitive Services tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>Med hjälp av REST-API

REST API är det enklaste sättet att känna igen tal om du inte använder ett språk som stöds av SDK: N. Du gör en HTTP POST-begäran till tjänstslutpunkten, skicka hela uttryck i brödtexten i begäran. Du får ett svar som innehåller den tolkade texten.

> [!NOTE]
> Yttranden är begränsade till 15 sekunder eller mindre när du använder REST-API.

Mer information om den **tal till Text** REST API, se [REST API: er](rest-apis.md#speech-to-text). Om du vill se hur det fungerar i praktiken kan du ladda ned den [REST API-exempel](https://github.com/Azure-Samples/SpeechToText-REST) från GitHub.

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Hur du känna igen tal i C++](quickstart-cpp-windows.md)
- [Så identifierar du tal i C#](quickstart-csharp-dotnet-windows.md)
- [Hur du känna igen tal i Java](quickstart-java-android.md)
