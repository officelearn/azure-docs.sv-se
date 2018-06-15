---
title: Använda tal till Text | Microsoft Docs
description: Lär dig hur du använder tal till Text i tjänsten tal
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 878a31992415b1f8688afcfb186fcd94ce2567b4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356230"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>Använd ”tal texten” i tjänsten tal

Du kan använda **tal till Text** i dina program på två olika sätt.

| Metod | Beskrivning |
|-|-|
| [SDK](speech-sdk.md) | Enklaste sättet för utvecklare för C/C++, C# och Java * |
| [REST](rest-apis.md) | Identifiera kort utterances med en HTTP POST-begäran | 

\* *Java SDK är en del av den [tal enheter SDK](speech-devices-sdk.md).*

## <a name="using-the-sdk"></a>Med SDK

Den [tal SDK](speech-sdk.md) ger det enklaste sättet att använda **tal till Text** i ditt program med full funktionalitet.

1. Skapa en tal fabrik som tillhandahåller en prenumeration tjänstnyckeln tal eller en autentiseringstoken. Du kan också konfigurera alternativ, till exempel recognition språk eller en anpassad slutpunkt för egna recognition modeller tal nu.

2. Hämta en identifierare från fabriken. Det finns tre olika typer av identifierare. Varje typ av tolken kan använda din enhet standard mikrofon, ljudström eller ljud från en fil.

    Tolken | Funktion
    -|-
    Taligenkänningen|Innehåller text utskrift av tal
    Avsiktshantering tolken|Härleds talare avsikt via [THOMAS](https://docs.microsoft.com/azure/cognitive-services/luis/) efter igenkänning\*
    Översättning tolken|Översätter transcribed texten till ett annat språk (se [tal översättning](how-to-translate-speech.md))

    \* *Du måste använda en separat THOMAS prenumeration nyckel när du skapar en tal fabrik för avsiktshantering tolken för avsiktshantering igenkänning.*
    
4. Sysselsätta händelser för asynkron åtgärd om så önskas. Tolken anropar sedan din händelsehanterare när den har tillfälligt och slutliga resultaten. Annars tillämpningsprogrammet kommer att få ett slutligt skrivfel resultat.

5. Starta igenkänning.

### <a name="sdk-samples"></a>SDK-exempel

Senaste uppsättning exempel, finns det [kognitiva Services tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>Med hjälp av REST-API

REST API är det enklaste sättet att känna igen röst om du inte använder ett språk som stöds av SDK. Du gör en HTTP POST-begäran till tjänstslutpunkten, skickar hela utterance i brödtexten i begäran. Du får ett svar som innehåller den tolkade texten.

> [!NOTE]
> Utterances är begränsade till 15 sekunder eller mindre när du använder REST API.


Mer information om den **tal till Text** REST-API: et, se [REST API: er](rest-apis.md#speech-to-text). Om du vill se det i praktiken, hämta den [REST API samples](https://github.com/Azure-Samples/SpeechToText-REST) från GitHub.

## <a name="next-steps"></a>Nästa steg

- [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
- [Hur du identifierar tal i C#](quickstart-csharp-windows.md)
