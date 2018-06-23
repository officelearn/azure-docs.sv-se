---
title: Kom igång med Microsoft Speech Recognition API i JavaScript | Microsoft Docs
description: Använd Microsoft Speech Recognition API i kognitiva Services för att utveckla program som kontinuerligt konverterar tal till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 12/21/2017
ms.author: zhouwang
ms.openlocfilehash: 56c41fd7f6a00d80bc6bccd61894654e057e926e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352188"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Kom igång med Speech Recognition API i JavaScript

Du kan utveckla program som konverterar tal till text med tal Recognition API. JavaScript-klientbiblioteket använder den [tal Service WebSocket-protokollet](../API-Reference-REST/websocketprotocol.md), där du kan kommunicera och få sätt text samtidigt. Den här artikeln hjälper dig att komma igång med Speech Recognition API i JavaScript.

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på Speech Recognition API och få en kostnadsfri utvärderingsprenumeration nyckel

Dikterings-API: N är en del av kognitiva tjänster. Du kan hämta en kostnadsfri utvärderingsprenumeration nycklar från den [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt tal-API, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar en primär och en sekundär nyckel. Båda nycklarna är knutna till samma kvoten, så du kan använda antingen nyckel.

> [!IMPORTANT]
> Hämta en nyckel för prenumerationen. Innan du kan använda tal klientbibliotek, måste du ha en [prenumeration nyckeln](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Kom igång

I det här avsnittet hjälper vi dig genom de nödvändiga stegen för att läsa in en exempel HTML-sida. Du hittar exemplet i vår [github-lagringsplatsen](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Du kan **öppna exemplet direkt** från databasen, eller **öppna provet från en lokal kopia** i databasen. 

> [!NOTE]
> Vissa webbläsare blockera mikrofon åtkomst på icke säkert ursprung. Därför rekommenderas att värd i exempel / din app på https för att den fungerar på alla webbläsare som stöds. 

### <a name="open-the-sample-directly"></a>Öppna exemplet direkt

Hämta en nyckel för prenumeration enligt beskrivningen ovan. Öppna den [länk till provet](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Detta kommer att läsa in sidan i standardwebbläsaren (återges med [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Öppna provet från en lokal kopia

Om du vill försöka exempel lokalt klona lagringsplatsen:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Kompilera källor för maskin- och paket/browserfy dem till en enda JavaScript-fil ([npm](https://www.npmjs.com/) måste vara installerad på datorn). Ändra till roten för den klonade databasen och kör kommandon:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Öppna `samples\browser\Sample.html` i webbläsaren.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du inkludera SDK i din egen webbsida finns [här](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Kommentarer

- API: et för Speech Recognition stöder tre [recognition lägen](../concepts.md#recognition-modes). Du kan växla läge genom att uppdatera den **Setup()** funktion hittades i filen Sample.html. Exemplet anger läget till `Interactive` som standard. Om du vill ändra läget uppdatera parametern `SR.RecognitionMode.Interactive` till ett annat läge. Till exempel ändra parametern för `SR.RecognitionMode.Conversation`.
- En fullständig lista över språk som stöds finns i [språk som stöds](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Relaterade ämnen

- [JavaScript Speech Recognition API exempel databasen](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Kom igång med REST API](GetStartedREST.md)
