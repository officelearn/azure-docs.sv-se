---
title: Korrigera felstavade ord – LUIS
titleSuffix: Azure Cognitive Services
description: Korrigera felstavade ord i yttranden genom att lägga till API för stavningskontroll i Bing v7 till LUIS slut punkts frågor.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/19/2019
ms.openlocfilehash: 9d2a1702ea131e9b1b4bf5e586f4290db3aff7ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018777"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Korrigera felstavade ord med Stavningskontroll i Bing

Du kan integrera din LUIS-app med [API för stavningskontroll i Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) för att korrigera felstavade ord i yttranden innan Luis förutsäger poängen och entiteterna i uttryck.

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

## <a name="create-endpoint-key"></a>Skapa slut punkts nyckel

Följ dessa instruktioner om du vill skapa en Stavningskontroll i Bing resurs i Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **skapa en resurs** i det övre vänstra hörnet.

3. Skriv `Bing Spell Check API V7` i sökrutan.

    ![Sök efter API för stavningskontroll i Bing v7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Välj tjänsten.

5. En informations panel visas till höger som innehåller information, inklusive juridiskt meddelande. Välj **skapa** för att starta processen för att skapa prenumerationer.

6. I nästa panel anger du dina tjänst inställningar. Vänta tills processen har skapats.

    ![Ange tjänst inställningar](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Välj **alla resurser** under rubriken **Favoriter** i navigerings sidan till vänster.

8. Välj den nya tjänsten. Dess typ är **Cognitive Services** och platsen är **Global**.

9. I huvud panelen väljer du **nycklar** för att se dina nya nycklar.

    ![Ta nycklar](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopiera den första nyckeln. Du behöver bara en av de två nycklarna.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Lägger till nyckeln i slut punkts-URL: en
Slut punkts frågan behöver nyckeln som skickas i Frågesträngens parametrar för varje fråga som du vill tillämpa stavnings korrigering. Du kan ha en chattrobot som anropar LUIS eller så kan du anropa LUIS slut punkts-API: n direkt. Oavsett hur slut punkten anropas måste varje anrop innehålla den information som krävs för att stavnings korrigeringar ska fungera korrekt.

Slut punkts-URL: en har flera värden som måste skickas på rätt sätt. Den API för stavningskontroll i Bing v7-nyckeln är bara en av dessa. Du måste ange parametern för **stavnings** kontroll till true och du måste ange värdet för **Bing-stavning-check-Subscription-Key** till nyckelvärdet:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=true&bing-spell-check-subscription-key={bingKey}&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Skicka felstavade uttryck till LUIS
1. I en webbläsare kopierar du föregående sträng och ersätter `region` ,, `appId` `luisKey` , och `bingKey` med dina egna värden. Se till att använda slut punkts regionen, om det skiljer sig från publicerings [regionen](luis-reference-regions.md).

2. Lägg till en felstavad uttryck, till exempel "hur långt är mountainn?". I engelska `mountain` är det med en `n` korrekt stavning.

3. Välj RETUR för att skicka frågan till LUIS.

4. LUIS svarar med ett JSON-resultat för `How far is the mountain?` . Om API för stavningskontroll i Bing v7 identifierar ett stavfel `query` innehåller fältet i Luis-appens JSON-svar den ursprungliga frågan och `alteredQuery` fältet innehåller den korrigerade frågan som skickats till Luis.

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

* Label-exempel yttranden som har alla olika stavningar så att LUIS kan lära sig korrekt stavning och skrivfel. Det här alternativet kräver mer märknings arbete än att använda en stavnings kontroll.
* Skapa en fras lista med alla varianter av ordet. Med den här lösningen behöver du inte märka ord variationerna i exemplet yttranden.

## <a name="publishing-page"></a>Publicerings sida
Kryss rutan **Aktivera Bing-stavnings kontroll har Aktiver** ATS på sidan [publicering](luis-how-to-publish-app.md) . Detta är en bekvämlighet att skapa nyckeln och förstå hur slut punktens URL ändras. Du måste fortfarande använda rätt slut punkts parametrar för att stavningen ska kunna korrigeras för varje uttryck.

> [!div class="nextstepaction"]
> [Läs mer om exempel yttranden](./luis-how-to-add-entities.md)