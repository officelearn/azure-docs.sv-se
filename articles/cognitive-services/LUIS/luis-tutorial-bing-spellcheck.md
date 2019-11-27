---
title: Korrigera felstavade ord – LUIS
titleSuffix: Azure Cognitive Services
description: Rätta felstavade ord i yttranden genom att lägga till Bing stavningskontroll kontrollera API V7 LUIS endpoint frågor.
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225455"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Rätta felstavade ord med stavningskontroll i Bing

Du kan integrera din LUIS-app med [API för stavningskontroll i Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) för att korrigera felstavade ord i yttranden innan Luis förutsäger poängen och entiteterna i uttryck. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Skapa första nyckeln för Bing stavningskontroll kontrollera V7

Din [första API för stavningskontroll i Bing v7-nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) är kostnads fri. 

![Skapa kostnadsfria nyckel](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Skapa slutpunktsnyckel
Om din kostnadsfria nyckel har gått ut kan du skapa en slutpunktsnyckel.

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **skapa en resurs** i det övre vänstra hörnet.

3. Skriv `Bing Spell Check API V7` i sökrutan.

    ![Sök efter för stavningskontroll i Bing Kontrollera API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Välj tjänsten. 

5. En panelen visas till höger som innehåller information inklusive juridiskt meddelande. Välj **skapa** för att starta processen för att skapa prenumerationer. 

6. Ange inställningar i nästa panel. Vänta tills tjänsten skapandeprocessen ska slutföras.

    ![Ange inställningar för tjänsten](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Välj **alla resurser** under rubriken **Favoriter** i navigerings sidan till vänster.

8. Välj den nya tjänsten. Dess typ är **Cognitive Services** och platsen är **Global**. 

9. I huvud panelen väljer du **nycklar** för att se dina nya nycklar.

    ![Hämta nycklar](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopiera den första nyckeln. Du behöver bara en av två nycklar. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Att lägga till nyckeln till slutpunkts-URL
Slutpunkt-frågan måste nyckeln som har skickats frågesträngparametrarna för varje fråga som du vill använda stavning korrigering. Du kan ha en chattrobot som anropar LUIS eller du kan anropa LUIS slutpunkten API direkt. Oavsett hur slutpunkten som anropas, måste varje anrop innehålla information som behövs för stavningskorrigeringar ska fungera korrekt.

Slutpunkten URL: en har flera värden som ska skickas på rätt sätt. Stavningskontroll i Bing v7 nyckeln är bara en av dessa. Du måste ange parametern för **stavnings** kontroll till true och du måste ange värdet för **Bing-stavning-check-Subscription-Key** till nyckelvärdet:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Skicka felstavade uttryck till LUIS
1. Kopiera föregående sträng i en webbläsare och ersätt `region`, `appId`, `luisKey`och `bingKey` med dina egna värden. Se till att använda slut punkts regionen, om det skiljer sig från publicerings [regionen](luis-reference-regions.md).

2. Lägg till en felstavade uttryck som ”hur långt är mountainn”?. På engelska är `mountain`, med en `n`, rätt stavning. 

3. Välj ange för att skicka frågan till LUIS.

4. LUIS svarar med ett JSON-resultat för `How far is the mountain?`. Om API för stavningskontroll i Bing v7 identifierar ett stavfel, innehåller fältet `query` i LUIS-appens JSON-svar den ursprungliga frågan och fältet `alteredQuery` innehåller den korrigerade frågan som skickats till LUIS.

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

Om du inte vill använda tjänsten API för stavningskontroll i Bing v7 måste du lägga till korrekt och felaktig stavning. 

Två lösningar:

* Label-exempel yttranden som har alla olika stavningar så att LUIS kan lära sig korrekt stavning och skrivfel. Det här alternativet kräver mer etikettering av dig än att använda en stavningskontroll.
* Skapa en fras lista med alla varianter av ordet. Med den här lösningen behöver du inte märka ord variationerna i exemplet yttranden. 

## <a name="publishing-page"></a>Publiceringssida
Kryss rutan **Aktivera Bing-stavnings kontroll har Aktiver** ATS på sidan [publicering](luis-how-to-publish-app.md) . Det här är att underlätta för att skapa nyckeln och förstå hur slutpunkts-URL ändras. Du måste använda rätt slutpunkt-parametrar för att få stavning korrigerad för varje uttryck. 

> [!div class="nextstepaction"]
> [Läs mer om exempel yttranden](luis-how-to-add-example-utterances.md)
