---
title: Publicera din LUIS-app | Microsoft Docs
description: När du skapa och testa din app med hjälp av Språkförståelse (LUIS) kan du publicera den som en webbtjänst på Azure.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 1bd24af0498755b7cdcb170624fd8f9f3b39c85a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347458"
---
# <a name="publish-your-trained-app"></a>Publicera din tränade app
När du är klar att skapa och testa LUIS-appen kan du publicera den. När appen publiceras på publiceringssidan visar alla tillhörande HTTP [slutpunkter](luis-glossary.md#endpoint). De här slutpunkterna per [region](luis-reference-regions.md) och per [nyckel](luis-how-to-manage-keys.md), integreras sedan i alla klienten, chattrobot eller backend-program. 

Du kan alltid [testa](interactive-test.md) din app innan du publicerar den. 

## <a name="production-and-staging-slots"></a>Produktions- och mellanlagringsplatser
Du kan publicera din app till den **mellanlagringsplats** eller **produktionsplatsen**. Detta kan du ha två olika versioner med publicerade slutpunkter eller samma version på två olika slutpunkter med hjälp av två platser för publicering. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>Inställningar för konfigurationen kräver publishing modell
Publicera till slutpunkten efter att ändringarna av följande inställningar. 

## <a name="external-services-settings"></a>Inställningar för externa tjänster
Inkludera externa tjänstinställningar **[Attitydanalys](#enable-sentiment-analysis)** och  **[tal väcker](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Aktivera attitydanalys
I den **externa tjänster inställningar**, **aktivera Attitydanalys** kryssrutan kan LUIS för att integrera med [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/) att tillhandahålla känsla och nyckelfraser analys. Du behöver inte ange en nyckel för textanalys och det är kostnadsfritt faktureringen för den här tjänsten på Azure-kontot. När du markerar den här inställningen får är det beständiga. 

Åsiktsdata är ett värde mellan 1 och 0 som anger vilka positiva (närmare 1) eller ett negativt (närmare 0) känsla av data.

Läs mer om JSON-svar för slutpunkt med attitydanalys [attitydanalys](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Aktivera tal promotor 
I den **externa tjänster inställningar**, **aktivera tal väcker** kryssrutan låter dig ha en enda slutpunkt för att få en talat uttryck från en chattrobot eller LUIS-anropa program och ta emot en LUIS förutsägelse svar. Tal-promotor använder Cognitive service [Taligenkänning](../Speech-Service/rest-apis.md). 

![Bild av tal promotor bekräftelsedialogrutan](./media/luis-how-to-publish-app/speech-prime-modal.png)

Publicera din app när den här funktionen är aktiverad. När du publicerar din LUIS-app, skickas din appmodell till din egen tal-tjänst att Preparera Speech-tjänsten. Modellinformationen om är **inte** används utanför din egen tjänst. 

För att slutföra användningen av tal promotor, du behöver följande information för att använda i den [tal SDK](../speech-service/speech-sdk-reference.md):
* En LUIS-slutpunktsnyckeln.
* LUIS-app-ID.
* En slutpunkt-domän, kallas ”värdnamnet” i tal SDK, till exempel ”westus.api.cognitive.microsoft.com”, där den första underdomänen är den region där appen har publicerats.

Mer information finns i den [tal till avsikt](http://aka.ms/speechsdk) exemplet.

När LUIS-appen har tagits bort eller Speech-tjänsten tas bort, tas dina modelldata. 

## <a name="endpoint-url-settings"></a>Slutpunkten URL-inställningar
Slutpunktsinställningarna URL tjänster inkluderar **[tidszon](#set-timezone-offset)** Förskjutning,  **[alla förutse avsikt poäng](#include-all-predicted-intent-scores)**, och  **[ Bing-stavningskontroll](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Ange tidszonen förskjutning 
En del av valet fack är tidszonen valet. Den här inställningen för tidszon kan LUIS för att [alter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) helst fördefinierade datetimeV2 värden under förutsägelse så att den returnerade entitetsdata är giltigt enligt den valda tidszonen. 

### <a name="include-all-predicted-intent-scores"></a>Inkludera alla avsikt förutsägelsepoängen
Den **inkludera alla förutse avsikt poäng** kryssrutan tillåter frågesvaret slutpunkten att inkludera förutsägelse poängen för varje avsikt. 

Den här inställningen kan din chattrobot eller LUIS-anropa program kan besluta programmässiga baserat på poäng för returnerade avsikter. I allmänhet är de främsta två avsikterna mest intressanta. Om den översta poängen är avsiktlig, din chattrobot kan du ställa en fråga för uppföljning som gör en slutgiltig val mellan avsikt ingen och andra flest poäng avsikten ingen. 

Avsikter och deras resultat är också inkluderat endpoint-loggarna. Du kan [exportera](create-new-app.md#export-app) dessa loggar och analysera poängen. 

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

### <a name="enable-bing-spell-checker"></a>Aktivera Bing-stavningskontroll 
I den **slutpunkts-url-inställningar**, **Aktivera Bing-stavningskontroll** kryssrutan kan LUIS för att korrigera felstavade ord. innan förutsägelse. Detta måste du skapa en  **[stavningskontroll i Bing nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. När nyckeln har skapats läggs två parametrar för frågesträng till slutpunkts-URL på publiceringssidan. 

Om du konstruerar en egen URL: er för programmets LUIS anropas, se till att den **stavningskontroll = true** Frågeparametern och **bing-stavningskontroll-kontroll-subscription-key = {YOUR_BING_KEY_HERE}**. Ersätt den `{YOUR_BING_KEY_HERE}` med din nyckel för Bing stavningskontroll för installation.

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

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Publicera din tränade app till en HTTP-slutpunkt
Öppna din app genom att klicka på namnet på den **Mina appar** , och klicka sedan på **publicera** i den övre panelen. 

![Publicera sidan-](./media/luis-how-to-publish-app/publish-to-production.png)
 
När din app har publicerats visas ett meddelande om grönt överst i webbläsaren. 

* Välj om du vill publicera till **produktion** eller **mellanlagring** genom att välja från den nedrullningsbara menyn under **väljer fack**. 

## <a name="assign-key"></a>Tilldela nyckel

Om du vill använda en nyckel än den kostnadsfria Starter_Key visas, klickar du på den **Lägg till nyckel** knappen. Den här åtgärden öppnar en dialogruta där du kan välja en befintlig slutpunkt-nyckel för att tilldela till appen. Mer information om hur du skapar och lägger till slutpunkten nycklar till LUIS-appen finns i [hantera nycklarna](luis-how-to-manage-keys.md).

Använd alternativknapparna för att växla regioner om du vill visa slutpunkter och nycklar som är associerade med andra regioner. Varje rad i den **resurser och nycklar** tabell Azure-resurser som är associerade med ditt konto och slutpunkt-nycklar som är associerade med den här resursen.

## <a name="endpoint-url-construction"></a>Slutpunkten URL-konstruktion
Slutpunkts-URL som motsvarar den Azure-region som är associerade med slutpunktsnyckeln.

Den här tabellen visar bekvämt publishing konfigurationen i URL-slutpunkten med val av väg och frågesträngsvärden. Om du konstruerar din slutpunkt-URL: er för LUIS-anropa programmet, kontrollera att dessa samma vägar och frågesträngen värden har angetts för slutpunkten används – om du vill ange.

URL-flödet har konstruerats med regionen och app-ID. Om du publicerar i andra regioner eller med andra appar kan slutpunkts-URL skapas genom att ändra de region och app-ID-värdena. 

* Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera). När publiceringen har slutförts kan du använda visade slutpunkts-URL för att komma åt LUIS-appen. 

### <a name="optional-query-string-parameters"></a>Valfria parametrar för frågesträngen
Följande frågesträngparametrarna kan användas med slutpunkts-URL:

<!-- TBD: what about speech priming? -->

|Frågesträng|Typ|Exempelvärde|Syfte|
|--|--|--|--|
|utförlig|boolesk|true|Inkludera [samtliga avsikt värden](#include-all-predicted-intent-scores) för uttryck|
|timezoneOffset|Antal (enhet är minuter)|60|Ange [tidszon offset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) för [datetimeV2 fördefinierade entiteter](luis-reference-prebuilt-datetimev2.md)|
|Stavningskontroll|boolesk|true|[stavningskontroll](#enable-bing-spell-checker) av uttryck--används tillsammans med bing-stavningskontroll-kontroll-subscription-key frågesträngparametern|
|Bing-stavningskontroll-kontroll-subscription-key|prenumerations-ID||används tillsammans med parametern för frågesträngen stavningskontroll|
|Mellanlagring|boolesk|false|Välj mellanlagring eller produktion slutpunkt|
|log|boolesk|true|Lägg till frågor och resultat för att logga in|


## <a name="test-your-published-endpoint-in-a-browser"></a>Testa publicerade slutpunkten i en webbläsare
Testa publicerade slutpunkten genom att välja URL: en i den **Endpoint** kolumn. Standardwebbläsaren öppnas med genererade URL: en. Ange URL-parametern ”& q” att testa frågan. Lägg exempelvis till `&q=Book me a flight to Boston on May 4` till URL: en och tryck på RETUR. Webbläsaren visar JSON-svar för HTTP-slutpunkten. 

![JSON-svar från en publicerade HTTP-slutpunkt](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>Nästa steg

* Se [hantera nycklar](./luis-how-to-manage-keys.md) att lägga till nycklar LUIS-appen och lär dig mer om hur nycklar mappas till områden.
* Se [träna och testa din app](interactive-test.md) anvisningar om hur du testar din publicerade app i test-konsolen.
