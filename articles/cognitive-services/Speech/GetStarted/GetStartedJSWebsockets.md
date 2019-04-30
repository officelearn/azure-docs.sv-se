---
title: Kom igång med igenkänning av API för Bing-taligenkänning i JavaScript | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använd Bing-Taligenkänning för taligenkänning i Cognitive Services för att utveckla program som kontinuerligt omvandla talat ljud till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17901ad40a48e9ee8d1a8b872b04ad52b75b3a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515200"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Kom igång med API för taligenkänning taligenkänning i JavaScript

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Du kan utveckla program som omvandla talat ljud till text med hjälp av de tal-API: T. JavaScript-klientbiblioteket använder den [Speech Service WebSocket-protokoll](../API-Reference-REST/websocketprotocol.md), vilket gör att du prata och ta emot transkriberas text samtidigt. Den här artikeln hjälper dig att komma igång med API för taligenkänning taligenkänning i JavaScript.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på det tal-API: T och få en kostnadsfri utvärderingsprenumeration-nyckel

API för taligenkänning är en del av Cognitive Services. Du kan få kostnadsfri utvärderingsprenumeration nycklar från den [Cognitive Services-prenumeration](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt API för taligenkänning, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar den primära och sekundära nyckeln. Båda nycklarna är knutna till samma kvot, så du kan använda någon av nycklarna.

> [!IMPORTANT]
> Få en prenumerationsnyckel. Innan du kan använda tal-klientbiblioteken, måste du ha en [prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Kom igång

I det här avsnittet vägleder vi dig genom stegen för att läsa in en exempel-HTML-sida. Exemplet finns i vår [GitHub-lagringsplatsen](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Du kan **öppna exemplet direkt** från databasen, eller **öppna exemplet från en lokal kopia** av databasen.

> [!NOTE]
> Vissa webbläsare blockera åtkomst till mikrofonen på att ta bort skydd ursprung. Därför rekommenderar vi att vara värd för i exemplet / din app på https till detta ska fungera på alla webbläsare som stöds.

### <a name="open-the-sample-directly"></a>Öppna exemplet direkt

Skaffa en prenumerationsnyckel som beskrivs ovan. Öppna sedan den [länk till exemplet](https://htmlpreview.github.io/? https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Detta kommer att läsa in sidan i din standardwebbläsare (återges med [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Öppna exemplet från en lokal kopia

Klona den här databasen om du vill prova exemplet lokalt:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Kompilera TypeScript-källor och sedan paketera dem i en JavaScript-fil ([npm](https://www.npmjs.com/) måste vara installerad på din dator). Ändra till roten för den klonade lagringsplatsen och kör kommandon:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Öppna `samples\browser\Sample.html` i webbläsaren.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du inkludera SDK: N i din egen webbsida finns [här](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Kommentarer

- Det tal-API: T stöder tre [erkännande lägen](../concepts.md#recognition-modes). Du kan växla läge genom att uppdatera den **Setup()** funktionen finns i filen Sample.html. Exemplet anger läget till `Interactive` som standard. Om du vill ändra läget uppdatera parametern `SR.RecognitionMode.Interactive` till ett annat läge. Till exempel ändra parametern att `SR.RecognitionMode.Conversation`.
- En fullständig lista över språk som stöds finns i [språk som stöds](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Relaterade ämnen

- [JavaScript tal-API: T exempellagringsplatsen](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Kom igång med REST API](GetStartedREST.md)
