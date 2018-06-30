---
title: Publicera appen THOMAS | Microsoft Docs
description: När du skapa och testa din app med hjälp av språk förstå (THOMAS) kan du publicera den som en webbtjänst på Azure.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ad5ef58536bb6cc7f2c754f2e5c4487a3ffe96f2
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112834"
---
# <a name="publish-your-trained-app"></a>Publicera appen utbildade
När du är klar bygger och testar appen THOMAS publicera den. När appen publiceras, publicera sidan visas alla tillhörande HTTP [slutpunkter](luis-glossary.md#endpoint). Dessa slutpunkter per [region](luis-reference-regions.md) och per [nyckeln](Manage-Keys.md), sedan integreras i alla klient, chatbot eller backend-program. 

Du kan alltid [testa](interactive-test.md) appen innan du publicerar den. 

## <a name="production-and-staging-slots"></a>Produktions- och mellanlagring fack
Du kan publicera en app till den **mellanlagring fack** eller **produktionsplatsen**. Detta kan du har två olika versioner med publicerade slutpunkter eller samma version på två olika slutpunkter med hjälp av två platser för publicering. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>Konfigurationen av prenumerationsinställningar kräver publishing modellen
Publicera till slutpunkten efter att ändringarna av följande inställningar. 

## <a name="external-services-settings"></a>Inställningar för externa tjänster
Externa tjänstinställningar inkluderar **[Sentiment Analysis](#enable-sentiment-analysis)** och  **[tal Priming](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Aktivera sentiment analys
I den **externa tjänster inställningar**, **aktivera Sentiment Analysis** checkbox tillåter THOMAS att integrera med [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/) att tillhandahålla sentiment och en fras som nyckel analys. Du behöver inte tillhandahålla en textanalys nyckel och är kostnadsfri fakturering för tjänsten till ditt Azure-konto. När du Markera inställningen är det beständiga. 

Sentiment data är ett värde mellan 1 och 0 som anger positivt (närmare 1) eller ett negativt (närmare 0) sentiment av data.

Läs mer om JSON-svar för slutpunkten med sentiment analysis [Sentiment analys](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Aktivera tal promotor 
I den **externa tjänster inställningar**, **aktivera tal Priming** kryssrutan kan du ha en enda slutpunkt för att hämta en talade utterance från en chatbot eller THOMAS anropar programmet och ta emot en THOMAS förutsägelse svar. Tal promotor använder tjänsten kognitiva [Speech API](../Speech-Service/rest-apis.md). 

![Bild av tal promotor bekräftelsedialogruta](./media/luis-how-to-publish-app/speech-prime-modal.png)

Publicera en app när den här funktionen har aktiverats. När du publicerar appen THOMAS skickas din appmodell till tal tjänsten till prime tjänsten tal. Modellinformationen om är **inte** används utanför din egen tjänst. 

För att slutföra användningen av tal promotor, behöver du följande information för användning i den [tal SDK](../speech-service/speech-sdk-reference.md):
* En THOMAS endpoint-nyckel.
* THOMAS app-ID.
* En slutpunkt-domän, kallas ”värdnamnet” i tal SDK, till exempel ”westus.api.cognitive.microsoft.com” där den första underdomänen är den region där appen har publicerats.

Mer information finns i [tal att avsikten](http://aka.ms/speechsdk) exempel.

När appen THOMAS tas bort eller tjänsten tal tas bort, tas din modelldata bort. 

## <a name="endpoint-url-settings"></a>URL-inställningar för slutpunkten
Slutpunkten URL-inställningar för tjänster inkluderar **[tidszonen](#set-timezone-offset)** Förskjutning,  **[alla förutsade avsiktshantering poäng](#include-all-predicted-intent-scores)**, och  **[ Bing stavningskontrollen](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Ange tidszonen förskjutning 
En del av fack valet är val av tidszon. Den här tidszonen inställningen THOMAS till [alter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) helst fördefinierade datetimeV2 som värden under förutsägelse så att den returnerade entitetsdata är korrekta för den valda tidszonen. 

### <a name="include-all-predicted-intent-scores"></a>Inkludera alla avsiktshantering detta
Den **inkludera alla förutsade avsiktshantering poäng** kryssrutan kan en slutpunkt frågesvar att inkludera förutsägelse poängen för varje avsikt. 

Den här inställningen kan din chatbot eller THOMAS anropar programmet kan besluta programmässiga utifrån resultat för de returnerade avsikter. I allmänhet är de översta två avsikter mest intressanta. Om den översta poängen är ingen avsiktshantering din chatbot kan du fråga uppföljning som gör en slutgiltig val mellan avsiktshantering ingen och andra flest poäng avsikten. 

Innehållet och deras resultat finns också inkluderat endpoint-loggarna. Du kan [exportera](create-new-app.md#export-app) dessa loggar och analysera resultaten. 

```
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Aktivera Bing stavningskontrollen 
I den **slutpunkts-url-inställningar**, **Aktivera Bing stavningskontrollen** checkbox tillåter THOMAS att korrigera stavfel innan förutsägelse. Detta måste du skapa en  **[Bing stavningskontroll nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. När nyckeln har skapats läggs två querystring-parametrar till slutpunkts-URL på sidan Publicera. 

Om du konstruerar egna URL: er för tillämpningsprogrammet THOMAS anropar, kontrollerar du att den **stavningskontroll = true** querystring-parametern och **bing-stavningskontroll-check-prenumeration-key = {YOUR_BING_KEY_HERE}**. Ersätt den `{YOUR_BING_KEY_HERE}` med din Bing stavningskontroll checker nyckel.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Publicera appen utbildade till en HTTP-slutpunkt
Öppna din app genom att klicka på namnet på den **Mina appar** , och klickar sedan på **publicera** i den övre panelen. 

![Publicera sidan-](./media/luis-how-to-publish-app/publish-to-production.png)
 
När appen har publicerats visas ett meddelande om grön lyckade överst i webbläsaren. 

* Välj om du vill publicera till **produktion** eller **mellanlagring** genom att välja den nedrullningsbara menyn under **väljer fack**. 

## <a name="assign-key"></a>Tilldela nyckel

Om du vill använda en nyckel än den kostnadsfria Starter_Key visas klickar du på den **Lägg till nyckel** knappen. Den här åtgärden öppnar en dialogruta som du kan välja en befintlig slutpunkt nyckel att tilldela till appen. Mer information om hur du skapar och lägger till slutpunkten nycklar i appen THOMAS finns [hantera dina nycklar](Manage-Keys.md).

Använd alternativknapparna för att växla regioner om du vill se slutpunkter och nycklar som är associerade med andra regioner. Varje rad i den **resurser och nycklar** tabell visas Azure-resurser som är kopplade till ditt konto och endpoint-nycklar som associeras med den här resursen.

## <a name="endpoint-url-construction"></a>Slutpunkten URL konstruktion
Slutpunkts-URL som motsvarar den Azure-regionen som är associerad med slutpunktsnyckel.

Den här tabellen visar ett enkelt sätt publishing konfigurationen i URL-slutpunkt med väg alternativ och fråga strängvärden. Om du konstruerar endpoint-URL för tillämpningsprogrammet THOMAS anropar, kontrollera att dessa samma vägar och frågesträng värden har angetts för slutpunkten används--om du vill ange.

URL-flödet har skapats med regionen och app-ID. Om du publicerar i andra regioner eller med andra appar kan slutpunkts-URL konstrueras genom att ändra värdena för region och app-ID: T. 

* Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera). När publicera lyckas, kan du använda visas slutpunkts-URL för att komma åt appen THOMAS. 

### <a name="optional-query-string-parameters"></a>Valfri sträng frågeparametrar
Följande fråga string-parametrar kan användas med slutpunkts-URL:

<!-- TBD: what about speech priming? -->

|Frågesträng|Typ|Exempelvärde|Syfte|
|--|--|--|--|
|utförlig|boolesk|true|Inkludera [alla avsiktshantering poäng](#include-all-predicted-intent-scores) för utterance|
|timezoneOffset|Antal (enheten är minuter)|60|Ange [förskjutningen av tidszonen](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) för [datetimeV2 färdiga entiteter](luis-reference-prebuilt-datetimev2.md)|
|Stavningskontroll|boolesk|true|[stavningskontroll](#enable-bing-spell-checker) av utterance--används tillsammans med bing-stavningskontroll-check-prenumeration-nyckeln frågesträngparametern|
|Bing-stavningskontroll-check-prenumeration-nyckel|Prenumerations-ID||används tillsammans med stavningskontroll frågesträngparametern|
|Mellanlagring|boolesk|false|Välj mellanlagring och produktion slutpunkt|
|logg|boolesk|true|lägga till frågan och resultat för att logga in|


## <a name="test-your-published-endpoint-in-a-browser"></a>Testa din publicerade slutpunkt i en webbläsare
Testa publicerade slutpunkten genom att välja en URL i den **Endpoint** kolumn. Standardwebbläsaren med genererade URL. Ange URL-parametern ”& q” test-frågan. Lägg exempelvis till `&q=Book me a flight to Boston on May 4` till URL och tryck på RETUR. Webbläsaren visar JSON-svar för HTTP-slutpunkten. 

![JSON-svar från en publicerad HTTP-slutpunkt](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>Nästa steg

* Se [hantera nycklar](./Manage-Keys.md) Lägg till nycklar i appen THOMAS och lär dig mer om hur nycklar mappas till regioner.
* Se [tåg och testa din app](interactive-test.md) anvisningar om hur du testar din publicerade app i test-konsolen.
