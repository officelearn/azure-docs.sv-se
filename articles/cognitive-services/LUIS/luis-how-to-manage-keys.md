---
title: Hantera nycklar
titleSuffix: Language Understanding - Azure Cognitive Services
description: När du har skapat en LUIS-slutpunktsnyckeln i Azure-portalen, tilldela nyckeln till LUIS-app och få rätt slutpunkts-URL. Använd den här slutpunkts-URL för att få LUIS förutsägelser.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a278ded7ce290347645f345e4eee0b15972787f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088110"
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
<a name="assign-resource"></a>


## <a name="assign-resource-in-luis-portal"></a>Tilldela resurs i LUIS-portalen

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

### <a name="unassign-resource"></a>Ta bort resurs
När du frigör slutpunktsnyckeln bort den inte från Azure. Det är bara avlänkas från LUIS. 

När en slutpunktsnyckeln otilldelade, eller inte har tilldelats appen, ett begärande till slutpunkten URL returnerar ett fel: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Inkludera alla avsikt förutsägelsepoängen
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

### <a name="enable-bing-spell-checker"></a>Aktivera Bing-stavningskontroll 
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


### <a name="publishing-regions"></a>Publicera regioner

Läs mer om hur du publicerar [regioner](luis-reference-regions.md) inklusive publicering i [Europa](luis-reference-regions.md#publishing-to-europe), och [Australien](luis-reference-regions.md#publishing-to-australia). Publicera regioner skiljer sig från redigering regioner. Skapa en app i regionen redigering som motsvarar regionen publicerar du vill använda för frågan slutpunkten.

## <a name="assign-resource-without-luis-portal"></a>Tilldela resurs utan LUIS-portalen

För automation, till exempel en CI/CD-pipeline, kanske du vill automatisera tilldelningen av en LUIS-resurs till en LUIS-app. I ordning som måste du utföra följande steg:

1. Skaffa en Azure Resource Manager-token från detta [webbplats](https://resources.azure.com/api/token?plaintext=true). Denna token upphör så Använd den omedelbart. Begäran returnerar en Azure Resource Manager-token.

    ![Azure Resource Manager-token för begäran och ta emot Azure Resource Manager-token](./media/luis-manage-keys/get-arm-token.png)

1. Använd token för att begära LUIS-resurser mellan prenumerationer, från den [hämta LUIS azure konton API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), ditt användarkonto har åtkomst till. 

    Det här INLÄGGET API kräver följande inställningar:

    |Huvud|Värde|
    |--|--|
    |`Authorization`|Värdet för `Authorization` är `Bearer {token}`. Observera att token-värde måste föregås av ordet `Bearer` eller ett blanksteg.| 
    |`Ocp-Apim-Subscription-Key`|Din [redigering nyckeln](luis-how-to-account-settings.md).|

    Den här API: T returnerar en matris av JSON-objekt LUIS prenumerationer, inklusive prenumerations-ID, resursgrupp och resursnamn, returneras som kontonamn. Hitta ett objekt i matrisen med LUIS resursen ska tilldelas LUIS-app. 

1. Tilldela token till LUIS-resursen med den [tilldelar en LUIS azure-konton till ett program](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API. 

    Det här INLÄGGET API kräver följande inställningar:

    |Typ|Inställning|Värde|
    |--|--|--|
    |Huvud|`Authorization`|Värdet för `Authorization` är `Bearer {token}`. Observera att token-värde måste föregås av ordet `Bearer` eller ett blanksteg.|
    |Huvud|`Ocp-Apim-Subscription-Key`|Din [redigering nyckeln](luis-how-to-account-settings.md).|
    |Huvud|`Content-type`|`application/json`|
    |QueryString|`appid`|LUIS-app-ID. 
    |Innehåll||{”AzureSubscriptionId”: ”ddda2925-af7f-4b05-9ba1-2155c5fe8a8e”<br>”ResourceGroup”: ”resourcegroup-2”,<br>”AccountName”: ”luis-uswest-S0-2”}|

    När detta API lyckas returnerar 201 - skapade status. 

## <a name="next-steps"></a>Nästa steg

Använd din nyckel för att publicera din app i den **publicera app** sidan. Mer information om publicering finns i [publicera app](luis-how-to-publish-app.md).

Se [nycklar i LUIS](luis-concept-keys.md) att förstå LUIS redigering och slutpunkt viktiga begrepp.
