---
title: Lägg till Bing stavningskontroll kontrollera API v7 i THOMAS frågor | Microsoft Docs
titleSuffix: Azure
description: Rätta felstavade ord i utterances genom att lägga till Bing stavningskontroll kontrollera API V7 THOMAS endpoint frågor.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/27/2018
ms.author: v-geberr
ms.openlocfilehash: 96b23146e726b7fee86b7e449c81d7efc0073e8d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127677"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Rätta felstavade ord med Bing stavningskontroll

Du kan integrera THOMAS appen med [Bing stavningskontroll kontrollera API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) att korrigera stavningen i utterances innan THOMAS beräknar poäng och entiteter av utterance. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Skapa första nyckeln för Bing stavningskontroll kontrollera V7
Din [första Bing stavningskontroll kontrollera API v7 nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) är ledig. 

![Skapa ledigt nyckel](./media/luis-tutorial-bing-spellcheck/free-key.png)

< en ”skapa-prenumeration-namnnyckel” ></a>
## <a name="create-endpoint-key"></a>Skapa slutpunktsnyckel
Om din kostnadsfria nyckel har upphört att gälla, kan du skapa en slutpunktsnyckel.

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **skapar du en resurs** i det övre vänstra hörnet.

3. Skriv `Bing Spell Check API V7` i sökrutan.

    ![Sök efter Bing stavningskontroll kontrollera API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Välj tjänsten. 

5. En panelen visas till höger som innehåller information, inklusive juridiskt meddelande. Välj **skapa** att starta processen för prenumerationen. 

6. Ange inställningarna på panelen nästa. Vänta medan tjänstprocessen ska slutföras.

    ![Ange inställningar för tjänsten](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Välj **alla resurser** under den **Favoriter** rubrik i navigeringen till vänster.

8. Välj den nya tjänsten. Dess typ är **kognitiva Services** och platsen är **globala**. 

9. Markera den huvudsakliga panelen **nycklar** att se din nya nycklar.

    ![Hämta nycklar](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopiera den första nyckeln. Du behöver bara en av två nycklar. 

## <a name="using-the-key-in-luis-test-panel"></a>Med hjälp av nyckeln THOMAS test Kontrollpanelen
Det finns två platser i THOMAS angavs. Först är i den [test panelen](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Nyckeln sparas inte till THOMAS men är i stället en sessionsvariabeln. Du måste ange nyckeln varje gång du vill panelen test för att gälla utterance Bing stavningskontroll kontrollera API v7-tjänsten. Se [instruktioner](interactive-test.md#view-bing-spell-check-corrections-in-test-panel) på panelen test för att ställa in nyckeln.

## <a name="adding-the-key-to-the-endpoint-url"></a>Att lägga till nyckeln slutpunkts-URL
Frågan slutpunkt måste nyckeln som har skickats för varje fråga som du vill använda stavningskontrollen sträng Frågeparametrar. Du kan ha en chatbot som anropar THOMAS eller du kan anropa THOMAS slutpunkten API direkt. Oavsett hur slutpunkten anropas måste varje anrop innehålla information som behövs för stavningskorrigeringar ska fungera korrekt.

Slutpunkten URL innehåller flera värden som ska skickas på rätt sätt. Bing stavningskontroll kontrollera API v7-nyckeln är en av dessa. Måste du ange den **stavningskontroll** parameter till true och du måste ange värdet för **bing-stavningskontroll-check-prenumeration-nyckeln** till värdet för nyckeln:

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appID}?Subscription-Key={luisKey}&spellCheck=**SANT**& bing-stavningskontroll-check-prenumeration-nyckel =**{bingKey}**& utförlig = true & timezoneOffset = 0 & q = {utterance}

## <a name="send-misspelled-utterance-to-luis"></a>Skicka felstavade utterance till THOMAS
1. I en webbläsare, Kopiera föregående sträng och Ersätt den `region`, `appId`, `luisKey`, och `bingKey` med egna värden. Se till att använda endpoint-region om det skiljer sig från din publicering [region](luis-reference-regions.md).

2. Lägg till en felstavade utterance som ”hur långt är mountainn”?. Engelska, `mountain`, med en `n`, är rätt stavning. 

3. Välj ange för att skicka frågan till THOMAS.

4. THOMAS svarar med ett JSON-resultat för `How far is the mountain?`. Om Bing stavningskontroll kontrollera API v7 identifierar felstavat, den `query` fält i appen THOMAS JSON-svar som innehåller den ursprungliga frågan och `alteredQuery` fältet innehåller korrigerade fråga skickas till THOMAS.

```
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
Om du inte vill använda tjänsten Bing stavningskontroll kontrollera API v7 etikett du utterances som har stavfel så att THOMAS kan lära sig korrekt stavning samt skrivfel. Det här alternativet kräver mer märkning arbete än att använda en stavningskontroll.

## <a name="publishing-page"></a>Publicera sidan
Den [publishing](publishapp.md) sidan har ett **Aktivera Bing stavningskontrollen** kryssrutan. Det här är en underlätta för dig att skapa nyckeln och förstå hur slutpunkts-URL ändras. Du måste använda parametrarna korrekt slutpunkt för att få stavning korrigeras för varje utterance. 

> [!div class="nextstepaction"]
> [Mer information om exempel utterances](luis-how-to-add-example-utterances.md)
