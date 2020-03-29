---
title: Korrigera felstavade ord - LUIS
titleSuffix: Azure Cognitive Services
description: Korrigera felstavade ord i yttranden genom att lägga till Bing Stavningskontroll API V7 till LUIS slutpunktsfrågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 51b0d02443df872a7fae13116ea77b13d05055fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74225455"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Korrigera felstavade ord med Stavningskontroll i Bing

Du kan integrera din LUIS-app med [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) för att korrigera felstavade ord i yttranden innan LUIS förutsäger poängen och entiteterna för uttryck. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Skapa första nyckeln för Bing Spell Check V7

Din [första Bing Stavningskontroll API v7 nyckel](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) är gratis. 

![Skapa gratis nyckel](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Skapa slutpunktsnyckel
Om din kostnadsfria nyckel har gått ut skapar du en slutpunktsnyckel.

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **Skapa en resurs** i det övre vänstra hörnet.

3. Skriv `Bing Spell Check API V7` i sökrutan.

    ![Sök efter API för stavningskontroll av Bing V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Välj tjänsten. 

5. En informationspanel visas till höger som innehåller information, inklusive det juridiska meddelandet. Välj **Skapa** om du vill börja skapa prenumerationen. 

6. Ange dina tjänstinställningar på nästa panel. Vänta tills processen för att skapa tjänsten är klar.

    ![Ange tjänstinställningar](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Välj **Alla resurser** under rubriken **Favoriter** till vänster.

8. Välj den nya tjänsten. Dess typ är **Cognitive Services** och platsen är **global**. 

9. På huvudpanelen väljer du **Tangenter** för att se de nya tangenterna.

    ![Ta tag i nycklar](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopiera den första nyckeln. Du behöver bara en av de två nycklarna. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Lägga till nyckeln i slutpunkts-URL:en
Slutpunktsfrågan behöver nyckeln som skickas i frågesträngparametrarna för varje fråga som du vill använda stavningskorrigering. Du kan ha en chatbot som anropar LUIS eller så kan du anropa LUIS-slutpunkts-API:et direkt. Oavsett hur slutpunkten anropas måste varje anrop innehålla den information som krävs för att stavningskorrigeringar ska fungera korrekt.

Slutpunkts-URL:en har flera värden som måste skickas korrekt. Bing Stavningskontroll API v7 nyckel är bara en annan av dessa. Du måste ange parametern **spellCheck** till true och du måste ange värdet **för bing-spell-check-subscription-key** till nyckelvärdet:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Skicka felstavat uttryck till LUIS
1. I en webbläsare kopierar du föregående `region`sträng `appId` `luisKey`och `bingKey` ersätter , , och med dina egna värden. Se till att använda slutpunktsregionen om det skiljer sig från [publiceringsregionen](luis-reference-regions.md).

2. Lägg till ett felstavat uttryck som "Hur långt är berget?". På engelska `mountain`är `n`, med en , rätt stavning. 

3. Välj ange om du vill skicka frågan till LUIS.

4. LUIS svarar med ett JSON-resultat för `How far is the mountain?`. Om API v7 för stavningskontroll av Bing `query` identifierar en felstavning innehåller fältet i LUIS-appens JSON-svar den ursprungliga frågan och `alteredQuery` fältet innehåller den korrigerade frågan som skickas till LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorera stavfel

Om du inte vill använda tjänsten Bing Spell Check API v7 måste du lägga till rätt och felaktig stavning. 

Två lösningar är:

* Etikettexempel yttranden som har alla olika stavningar så att LUIS kan lära sig korrekt stavning och stavfel. Det här alternativet kräver mer märkningsansträngning än att använda en stavningskontroll.
* Skapa en fraslista med alla varianter av ordet. Med den här lösningen behöver du inte märka ordvariationerna i exempelyttrandena. 

## <a name="publishing-page"></a>Publiceringssida
[Publiceringssidan](luis-how-to-publish-app.md) har kryssrutan **Aktivera Bing-stavningskontroll.** Detta är en bekvämlighet för att skapa nyckeln och förstå hur slutpunkts-URL:en ändras. Du måste fortfarande använda rätt slutpunktsparametrar för att stavningen ska korrigeras för varje uttryck. 

> [!div class="nextstepaction"]
> [Läs mer om exempelyttranden](luis-how-to-add-example-utterances.md)
