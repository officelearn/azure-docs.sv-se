---
title: Anropa API:t för textanalys
titlesuffix: Azure Cognitive Services
description: Lär dig hur du anropar den REST API för textanalys.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: a70ef893019264ffc0eb3cb2982b05b15ebd0acf
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884376"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Hur du anropar den REST API för textanalys

Anrop till den **Textanalys** är HTTP POST/GET-anrop, vilket du kan formulera på valfritt språk. I den här artikeln använder vi REST och [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) att demonstrera nyckelbegrepp.

Varje begäran måste innehålla din åtkomstnyckel och en HTTP-slutpunkt. Slutpunkten som anger den region som du valde när du registrerade dig, tjänstens URL och en resurs som används på begäran: `sentiment`, `keyphrases`, `languages`, och `entities`. 

Kom ihåg att Text Analytics är tillståndslösa så att det finns inga datatillgångar som du hanterar. Texten har överförts kan analyseras vid mottagning, och resultaten returneras direkt till det anropande programmet.

> [!Tip]
> För oneoff-anrop och se hur API: et fungerar, kan du skicka POST-förfrågningar från inbyggt **API testkonsolen**, tillgängligt på valfri [API doc-sidan](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Det finns inga inställningar och de enda kraven är att klistra in en åtkomstnyckel och JSON-dokument i begäran. 

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API för textanalys**. 

Du måste ha den [slutpunkt och åtkomstnyckel](text-analytics-how-to-access-key.md) som genererades när du registrerar dig för Cognitive Services. 

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Schemadefinitionen för JSON

Indata måste vara JSON i raw ostrukturerad text. XML stöds inte. Schemat är enkel, som består av de delar som beskrivs i följande lista. 

För närvarande kan du skicka samma dokumenten för alla åtgärder för textanalys: sentiment, diskussionsämne, språkidentifiering och enhetens identifiering. (Schemat är kan variera för varje analys i framtiden.)

| Element | Giltiga värden | Krävs? | Användning |
|---------|--------------|-----------|-------|
|`id` |Datatypen är sträng, men i praktiken dokument-ID: N tenderar att vara heltal. | Krävs | Systemet använder ID: N som du anger för att strukturera utdata. Språkkoder, nyckelfraser och sentimentpoäng genereras för varje-ID i begäran.|
|`text` | Ostrukturerade rå text, upp till 5 000 tecken. | Krävs | För språkidentifiering, kan text uttryckas i valfritt språk. För attitydanalys, extrahering av diskussionsämne och enhetens identifiering texten måste vara i en [språk som stöds](../text-analytics-supported-languages.md). |
|`language` | 2 tecken [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) Platskod för en [språk som stöds](../text-analytics-supported-languages.md) | Varierar | Krävs för attitydanalys, extrahering av diskussionsämne och entitetslänkning; valfritt för språkidentifiering. Det finns inget fel om du utesluter den, men analysen lägre utan den. Språkkoden ska motsvara den `text` du anger. |

Mer information om begränsningar finns i [Text Analytics översikt > databegränsningar](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Ställa in en begäran i Postman

Tjänsten tar emot begäran upp till 1 MB i storlek. Om du använder Postman (eller något annat verktyg för test av webb-API) Konfigurera slutpunkten för att inkludera den resurs som du vill använda och ange åtkomstnyckel i en rubrik för begäran. Varje åtgärd som kräver att du lägger till lämplig resursen till slutpunkten. 

1. I Postman:

   + Välj **Post** som typ av begäran.
   + Klistra in den slutpunkt som du kopierade i portalsidan.
   + Lägg till en resurs.

  Resursen slutpunkter finns på följande sätt (din region kan variera):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

2. Ställ in tre begärandehuvuden:

   + `Ocp-Apim-Subscription-Key`: din åtkomstnyckel hämtade Azure portal.
   + `Content-Type`: application/json.
   + `Accept`: application/json.

  Din begäran bör likna följande skärmbild, förutsatt att en **/keyPhrases** resurs.

   ![Skärmbild med slutpunkt och sidhuvuden för begäran](../media/postman-request-keyphrase-1.png)

4. Klicka på **brödtext** och välj **raw** för formatet.

   ![Begäran skärmbild med brödtext inställningar](../media/postman-request-body-raw.png)

5. Klistra in några JSON-dokument i ett format som är giltig för den avsedda analysen. Mer information om en viss analys finns i följande avsnitt:

  + [Språkidentifiering](text-analytics-how-to-language-detection.md)  
  + [Extrahering av diskussionsämne](text-analytics-how-to-keyword-extraction.md)  
  + [Attitydanalys](text-analytics-how-to-sentiment-analysis.md)  
  + [Igenkänning av entiteter (förhandsversion)](text-analytics-how-to-entity-linking.md)  


6. Klicka på **skicka** att skicka begäran. Du kan skicka upp till 100 begäranden per minut. 

  I Postman visas svaret i fönstret nästa ned, som ett enda JSON-dokument med ett objekt för varje dokument-ID som tillhandahölls i begäran.

## <a name="see-also"></a>Se också 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera språk](text-analytics-how-to-language-detection.md)
