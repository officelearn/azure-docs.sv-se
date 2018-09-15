---
title: Hantera nycklar för redigering och CDN-slutpunkt i LUIS
titleSuffix: Azure Cognitive Services
description: När du har skapat en LUIS-slutpunktsnyckeln i Azure-portalen, tilldela nyckeln till LUIS-app och få rätt slutpunkts-URL. Använd den här slutpunkts-URL för att få LUIS förutsägelser.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 6d3f487fd64744fa390291d7e23d95cd9632cd23
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634940"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Lägg till en Azure-LUIS-resurs i appen

När du har skapat en LUIS-resurs i Azure portal tilldelar resursen LUIS-app och få rätt slutpunkts-URL. Använd den här slutpunkts-URL för att få LUIS förutsägelser.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>

## <a name="assign-resource"></a>Tilldela resurs

1. Skapa en LUIS-nyckel på den [Azure-portalen](https://portal.azure.com). Ytterligare instruktioner finns i [skapar en slutpunktsnyckel med hjälp av Azure](luis-how-to-azure-subscription.md).
 
2. Välj **hantera** i den övre högra menyn och markera **nycklar och slutpunkter**.

    [ ![Sidan nycklar och slutpunkter](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. För att lägga till LUIS, välja **Tilldela resurs +**.

    ![Tilldela en resurs till din app](./media/luis-manage-keys/assign-key.png)

4. Välj en klient i dialogrutan som är associerade med den e-postadress som du loggar in med LUIS-webbplatsen.  

5. Välj den **prenumerationsnamn** som är associerade med Azure-resursen du vill lägga till.

6. Välj den **LUIS resursnamn**. 

7. Välj **Tilldela resurs**. 

8. Hitta den nya raden i tabellen och kopiera slutpunkts-URL. Det är korrekt konstruerat för att göra en HTTP GET-begäran till LUIS-slutpunkten för en förutsägelse. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="unassign-resource"></a>Ta bort resurs
När du frigör slutpunktsnyckeln bort den inte från Azure. Det är bara avlänkas från LUIS. 

När en slutpunktsnyckeln otilldelade, eller inte har tilldelats appen, ett begärande till slutpunkten URL returnerar ett fel: `401 This application cannot be accessed with the current subscription`. 

## <a name="include-all-predicted-intent-scores"></a>Inkludera alla avsikt förutsägelsepoängen
Den **inkludera alla förutse avsikt poäng** kryssrutan tillåter frågesvaret slutpunkten att inkludera förutsägelse poängen för varje avsikt. 

Den här inställningen kan din chattrobot eller LUIS-anropa program kan besluta programmässiga baserat på poäng för returnerade avsikter. I allmänhet är de främsta två avsikterna mest intressanta. Om den översta poängen är avsiktlig, din chattrobot kan du ställa en fråga för uppföljning som gör en slutgiltig val mellan avsikt ingen och andra flest poäng avsikten ingen. 

Avsikter och deras resultat är också inkluderat endpoint-loggarna. Du kan [exportera](luis-how-to-start-new-app.md#export-app) dessa loggar och analysera poängen. 

```JSON
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

## <a name="enable-bing-spell-checker"></a>Aktivera Bing-stavningskontroll 
I den **slutpunkts-url-inställningar**, **Bing-stavningskontroll** växlingen möjliggör LUIS för att korrigera felstavade ord. innan förutsägelse. Skapa en  **[stavningskontroll i Bing nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Lägg till den **stavningskontroll = true** Frågeparametern och **bing-stavningskontroll-kontroll-subscription-key = {YOUR_BING_KEY_HERE}** . Ersätt den `{YOUR_BING_KEY_HERE}` med din nyckel för Bing stavningskontroll för installation.

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


## <a name="publishing-regions"></a>Publicera regioner

Läs mer om hur du publicerar [regioner](luis-reference-regions.md) inklusive publicering i [Europa](luis-reference-regions.md#publishing-to-europe), och [Australien](luis-reference-regions.md#publishing-to-australia). Publicera regioner skiljer sig från redigering regioner. Skapa en app i regionen redigering som motsvarar regionen publicerar du vill använda för frågan slutpunkten.

## <a name="next-steps"></a>Nästa steg

Använd din nyckel för att publicera din app i den **publicera app** sidan. Mer information om publicering finns i [publicera app](luis-how-to-publish-app.md).

Se [nycklar i LUIS](luis-concept-keys.md) att förstå LUIS redigering och slutpunkt viktiga begrepp.