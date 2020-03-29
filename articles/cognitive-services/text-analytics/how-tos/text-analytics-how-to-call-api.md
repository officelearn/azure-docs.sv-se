---
title: Anropa API:t för textanalys
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du kan anropa AZURE Cognitive Services Text Analytics REST API och Postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219306"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Så här anropar du REST-API:et för textanalys

Anrop till **API:et** för textanalys är HTTP POST/GET-anrop som du kan formulera på vilket språk som helst. I den här artikeln använder vi REST och [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) för att demonstrera nyckelbegrepp.

Varje begäran måste innehålla din åtkomstnyckel och en HTTP-slutpunkt. Slutpunkten anger vilken region du valde under registreringen, tjänstens URL och `sentiment` `keyphrases`en `languages`resurs `entities`som används på begäran: , , och . 

Kom ihåg att Text Analytics är tillståndslöst så det finns inga datatillgångar att hantera. Texten laddas upp, analyseras vid mottagandet och resultaten returneras omedelbart till det anropande programmet.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>JSON-schemadefinition

Indata måste vara JSON i rå ostrukturerad text. XML stöds inte. Schemat är enkelt och består av de element som beskrivs i följande lista. 

Du kan för närvarande skicka samma dokument för alla Text Analytics-åtgärder: sentiment, nyckelfras, språkidentifiering och entitetsidentifiering. (Schemat kommer sannolikt att variera för varje analys i framtiden.)

| Element | Giltiga värden | Krävs? | Användning |
|---------|--------------|-----------|-------|
|`id` |Datatypen är sträng, men i praktiken tenderar dokument-ID:n att vara heltal. | Krävs | Systemet använder de ID:er som du anger för att strukturera utdata. Språkkoder, nyckelfraser och sentimentpoäng genereras för varje ID i begäran.|
|`text` | Ostrukturerad rå text, upp till 5 120 tecken. | Krävs | För språkidentifiering kan text uttryckas på vilket språk som helst. För sentimentanalys, extrahering av nyckelfraser och enhetsidentifiering måste texten vara på ett [språk som stöds](../text-analytics-supported-languages.md). |
|`language` | 2-teckens [ISO 639-1-kod](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) för ett [språk som stöds](../text-analytics-supported-languages.md) | Varierar | Krävs för sentimentanalys, nyckelfrasutextraktion och entitetslänkning. valfritt för språkidentifiering. Det finns inget fel om du utesluter det, men analysen försvagas utan den. Språkkoden bör motsvara `text` den du anger. |

Mer information om begränsningar finns i [Översikt över textanalys > databegränsningar](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Konfigurera en begäran i Postman

Tjänsten accepterar begäran upp till 1 MB i storlek. Om du använder Postman (eller ett annat webb-API-testverktyg) ställer du in slutpunkten så att den innehåller den resurs du vill använda och anger åtkomstnyckeln i ett begärandehuvud. Varje åtgärd kräver att du lägger till rätt resurs till slutpunkten. 

1. I Brevbäraren:

   + Välj **Bokför** som förfråsättningstyp.
   + Klistra in den slutpunkt som du kopierade från portalsidan.
   + Lägg till en resurs.

   Resursslutpunkter är följande (din region kan variera):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Ange de tre förfråmsrubrikerna:

   + `Ocp-Apim-Subscription-Key`: din åtkomstnyckel som hämtas från Azure-portalen.
   + `Content-Type`: ansökan/json.
   + `Accept`: ansökan/json.

   Din begäran bör se ut ungefär som följande skärmdump, förutsatt att en **/ keyPhrases** resurs.

   ![Begär skärmdump med slutpunkt och rubriker](../media/postman-request-keyphrase-1.png)

4. Klicka på **Brödtext** och välj **rå** för formatet.

   ![Begär skärmdump med kroppsinställningar](../media/postman-request-body-raw.png)

5. Klistra in i vissa JSON-dokument i ett format som är giltigt för den avsedda analysen. Mer information om en viss analys finns i avsnitten nedan:

  + [Språkidentifiering](text-analytics-how-to-language-detection.md)  
  + [Extraktion av nyckelfraser](text-analytics-how-to-keyword-extraction.md)  
  + [Sentimentanalys](text-analytics-how-to-sentiment-analysis.md)  
  + [Erkännande av entitet](text-analytics-how-to-entity-linking.md)  


6. Klicka på **Skicka** om du vill skicka begäran. Se avsnittet [datagränser](../overview.md#data-limits) i översikten för information om antalet begäranden du kan skicka per minut och sekund.

   I Postman visas svaret i nästa fönster nedåt, som ett enda JSON-dokument, med ett objekt för varje dokument-ID som anges i begäran.

## <a name="see-also"></a>Se även 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (Vanliga frågor)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera språk](text-analytics-how-to-language-detection.md)
