---
title: Korrigera felstavade ord – LUIS
titleSuffix: Azure Cognitive Services
description: Korrigera felstavade ord i yttranden genom att lägga till API för stavningskontroll i Bing v7 till LUIS slut punkts frågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9c8babac8450bdfd170d3d18b338ba3d64383a67
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499026"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Korrigera felstavade ord med Stavningskontroll i Bing

Du kan integrera din LUIS-app med [API för stavningskontroll i Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) för att korrigera felstavade ord i yttranden innan Luis förutsäger poängen och entiteterna i uttryck. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Skapa första nyckeln för Stavningskontroll i Bing v7
Din [första API för stavningskontroll i Bing v7-nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) är kostnads fri. 

![Skapa en kostnads fri nyckel](./media/luis-tutorial-bing-spellcheck/free-key.png)

< namnet "Create-Subscription-Key" ></a>
## <a name="create-endpoint-key"></a>Skapa slut punkts nyckel
Om den kostnads fria nyckeln har upphört att gälla skapar du en slut punkts nyckel.

1. Logga in på [Azure-portalen](https://portal.azure.com). 

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

## <a name="using-the-key-in-luis-test-panel"></a>Använda nyckeln i LUIS test panel
Det finns två platser i LUIS för att använda nyckeln. Den första finns på [test panelen](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Nyckeln sparas inte i LUIS utan är i stället en sessionsvariabel. Du måste ange nyckeln varje gång du vill att test panelen ska tillämpa API för stavningskontroll i Bing v7-tjänsten på uttryck. Se [anvisningarna](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) på test panelen för att ställa in nyckeln.

## <a name="adding-the-key-to-the-endpoint-url"></a>Lägger till nyckeln i slut punkts-URL: en
Slut punkts frågan behöver nyckeln som skickas i Frågesträngens parametrar för varje fråga som du vill tillämpa stavnings korrigering. Du kan ha en chattrobot som anropar LUIS eller så kan du anropa LUIS slut punkts-API: n direkt. Oavsett hur slut punkten anropas måste varje anrop innehålla den information som krävs för att stavnings korrigeringar ska fungera korrekt.

Slut punkts-URL: en har flera värden som måste skickas på rätt sätt. Den API för stavningskontroll i Bing v7-nyckeln är bara en av dessa. Du måste ange parametern för **stavnings** kontroll till true och du måste ange värdet för **Bing-stavning-check-Subscription-Key** till nyckelvärdet:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Skicka felstavade uttryck till LUIS
1. Kopiera föregående sträng i en webbläsare och ersätt `region`, `appId`, `luisKey`och `bingKey` med dina egna värden. Se till att använda slut punkts regionen, om det skiljer sig från publicerings [regionen](luis-reference-regions.md).

2. Lägg till en felstavad uttryck, till exempel "hur långt är mountainn?". På engelska är `mountain`, med en `n`, rätt stavning. 

3. Välj RETUR för att skicka frågan till LUIS.

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

* Label-exempel yttranden som har alla olika stavningar så att LUIS kan lära sig korrekt stavning och skrivfel. Det här alternativet kräver mer märknings arbete än att använda en stavnings kontroll.
* Skapa en fras lista med alla varianter av ordet. Med den här lösningen behöver du inte märka ord variationerna i exemplet yttranden. 

## <a name="publishing-page"></a>Publicerings sida
Kryss rutan **Aktivera Bing-stavnings kontroll har Aktiver** ATS på sidan [publicering](luis-how-to-publish-app.md) . Detta är en bekvämlighet att skapa nyckeln och förstå hur slut punktens URL ändras. Du måste fortfarande använda rätt slut punkts parametrar för att stavningen ska kunna korrigeras för varje uttryck. 

> [!div class="nextstepaction"]
> [Läs mer om exempel yttranden](luis-how-to-add-example-utterances.md)
