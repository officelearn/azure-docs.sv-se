---
title: Anropa API:t för textanalys
titleSuffix: Azure Cognitive Services
description: Den här artikeln förklarar hur du kan anropa Azure-Cognitive Services Textanalys REST API och Postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 43ee7272066dbd89e7c0053d51ba039b83fb494f
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363824"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Så här anropar du Textanalys REST API

Anrop till **API för textanalys** är http post/GET-anrop, som du kan formulera på valfritt språk. I den här artikeln använder vi REST och [Postman](https://www.postman.com/downloads/) för att demonstrera viktiga begrepp.

Varje begäran måste innehålla din åtkomst nyckel och en HTTP-slutpunkt. Slut punkten anger den region som du valde under registreringen, tjänst-URL: en och en resurs som används på begäran: `sentiment` , `keyphrases` , `languages` och `entities` . 

Kom ihåg att Textanalys är tillstånds lös så att det inte finns några data till gångar att hantera. Texten laddas upp, analyseras vid inleverans och resultaten returneras omedelbart till det anropande programmet.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definition av JSON-schema

Indata måste vara JSON i rå ostrukturerad text. XML stöds inte. Schemat är enkelt, bestående av de element som beskrivs i följande lista. 

Du kan för närvarande skicka samma dokument för alla Textanalys åtgärder: sentiment, nyckel fras, språk identifiering och enhets identifiering. (Schemat kan troligt vis variera för varje analys i framtiden.)

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`id` |Data typen är sträng, men i dokument-ID: n är det vanligt vis heltal. | Obligatorisk | Systemet använder de ID: n som du anger för att strukturera utdata. Språk koder, nyckel fraser och sentiment resultat genereras för varje ID i begäran.|
|`text` | Ostrukturerad rå text, upp till 5 120 tecken. | Obligatorisk | För språk identifiering kan text uttryckas på valfritt språk. För sentiment analys, extrahering av nyckel fraser och enhets identifiering måste texten vara på ett [språk som stöds](../language-support.md). |
|`language` | 2 teckens [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -kod för ett [språk som stöds](../language-support.md) | Det varierar | Krävs för sentiment-analys, extrahering av nyckel fraser och länkning av entiteter. valfritt för språk identifiering. Det finns inget fel om du exkluderar det, men analysen har minskats utan den. Språk koden ska motsvara den `text` du anger. |

Mer information om gränser finns i [textanalys översikt > data begränsningar](../overview.md#data-limits). 


```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    },
    {
      "language": "en",
      "id": "3",
      "text": "Pike place market is my favorite Seattle attraction."
    }
  ]
}
```


## <a name="set-up-a-request-in-postman"></a>Konfigurera en begäran i Postman

Tjänsten accepterar en begäran på upp till 1 MB. Om du använder Postman (eller något annat verktyg för webb-API-testning), ställer du in slut punkten för att inkludera den resurs som du vill använda och anger åtkomst nyckeln i ett begär ande huvud. Varje åtgärd kräver att du lägger till en lämplig resurs till slut punkten. 

1. I Postman:

   + Välj **post** som typ av begäran.
   + Klistra in den slut punkt som du kopierade från Portal sidan.
   + Lägg till en resurs.

   Resurs slut punkter är följande (din region kan variera):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/entities/recognition/general`

2. Ange de tre begärandehuvuden:

   + `Ocp-Apim-Subscription-Key`: din åtkomst nyckel, hämtas från Azure Portal.
   + `Content-Type`: Application/JSON.
   + `Accept`: Application/JSON.

   Din begäran bör se ut ungefär som på följande skärm, förutsatt att en **/keyPhrases** -resurs används.

   ![Skärm bild för begäran med slut punkt och sidhuvud](../media/postman-request-keyphrase-1.png)

4. Klicka på **brödtext** och välj **RAW** som format.

   ![Skärm bild för begäran med inställningar för brödtext](../media/postman-request-body-raw.png)

5. Klistra in vissa JSON-dokument i ett format som är giltigt för den avsedda analysen. Mer information om en viss analys finns i avsnitten nedan:

  + [Språkidentifiering](text-analytics-how-to-language-detection.md)  
  + [Extrahering av nyckelfraser](text-analytics-how-to-keyword-extraction.md)  
  + [Sentimentanalys](text-analytics-how-to-sentiment-analysis.md)  
  + [Igenkänning av enhet](text-analytics-how-to-entity-linking.md)  


6. Klicka på **Skicka** för att skicka begäran. I avsnittet [data begränsningar](../overview.md#data-limits) i översikt finns information om antalet förfrågningar som du kan skicka per minut och sekund.

   I Postman visas svaret i nästa fönster, som ett enda JSON-dokument, med ett objekt för varje dokument-ID som anges i begäran.

## <a name="see-also"></a>Se även 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera språk](text-analytics-how-to-language-detection.md)