---
title: 'Kom igång med API: et för Taligenkänning i Bing igenkänning i Java Script | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: 'Använd Taligenkänning i Bing igenkännings-API: n i Cognitive Services för att utveckla program som kontinuerligt konverterar talade ljud till text.'
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: eef1a4885b77ae94f11d3d5bda5ded9b70ed63a4
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965818"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Kom igång med tal igenkännings-API i Java Script

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Du kan utveckla program som konverterar talade ljud till text med hjälp av API: t för tal igenkänning. Java Script client-biblioteket använder sig av [Speech service WebSocket-protokollet](../API-Reference-REST/websocketprotocol.md), som gör att du kan prata och ta emot beskrivande text samtidigt. Den här artikeln hjälper dig att komma igång med API: t för tal igenkänning i Java Script.

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på API för tal igenkänning och få en prenumerations nyckel för kostnads fri utvärdering

Speech API är en del av Cognitive Services. Du kan få kostnads fria utvärderings prenumerations nycklar från sidan [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/) . När du har valt Speech API väljer du **Hämta API-nyckel** för att hämta nyckeln. Den returnerar en primär och sekundär nyckel. Båda nycklarna är knutna till samma kvot, så du kan använda båda nycklarna.

> [!IMPORTANT]
> Hämta en prenumerations nyckel. Innan du kan använda tal klient bibliotek måste du ha en [prenumerations nyckel](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Kom igång

I det här avsnittet kommer vi att vägleda dig genom de steg som krävs för att läsa in en HTML-sida med exempel. Exemplet finns i vår GitHub- [lagringsplats](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Du kan **Öppna exemplet direkt** från lagrings platsen eller **Öppna exemplet från en lokal kopia** av lagrings platsen.

> [!NOTE]
> Vissa webbläsare blockerar mikrofon åtkomst vid osäkert ursprung. Vi rekommenderar därför att du är värd för "Sample"/"din app" på https för att den ska fungera med alla webbläsare som stöds.

### <a name="open-the-sample-directly"></a>Öppna exemplet direkt

Hämta en prenumerations nyckel enligt beskrivningen ovan. Öppna sedan [länken till exemplet](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Då läses sidan in i din standard webbläsare (återges med [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Öppna exemplet från en lokal kopia

För att testa exemplet lokalt, klona den här databasen:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Kompilera TypeScript-källorna och paketera dem i en enda JavaScript-fil ([NPM](https://www.npmjs.com/) måste vara installerade på datorn). Ändra till roten för den klonade lagrings platsen och kör kommandona:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Öppna `samples\browser\Sample.html` i din favorit webbläsare.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du lägger till SDK i din egen webb sida finns [här](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Kommentarer

- Tal igenkännings-API: et stöder tre [igenkännings lägen](../concepts.md#recognition-modes). Du kan växla läge genom att uppdatera funktionen **Setup ()** som finns i Sample. html-filen. Exemplet ställer in läget `Interactive` som standard. Om du vill ändra läget uppdaterar du parametern `SR.RecognitionMode.Interactive` till ett annat läge. Ändra till exempel parametern till `SR.RecognitionMode.Conversation`.
- En fullständig lista över språk som stöds finns i [språk som stöds](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Relaterade ämnen

- [Exempel lagrings plats för API för tal igenkänning i Java Script](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Kom igång med REST API](GetStartedREST.md)
