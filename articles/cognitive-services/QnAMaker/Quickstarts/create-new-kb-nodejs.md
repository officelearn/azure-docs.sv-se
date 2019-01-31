---
title: 'Snabbstart: Skapa kunskapsbas – REST, Node.js – QnA Maker'
description: I den här REST-baserade snabbstarten går vi igenom hur du skapar ett exempel på QnA Maker-kunskapsbas, programmatiskt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: diberry
ms.openlocfilehash: 470d0f80612bef31eca02856bd3bc6391c064035
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227411"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-nodejs"></a>Snabbstart: Skapa en kunskapsbas i QnA Maker med Node.js

Den här snabbstarten går igenom hur du programmatiskt skapar och publicerar ett exempel på QnA Maker-kunskapsbas. QnA Maker extraherar automatiskt frågor och svar för delvis strukturerat innehåll, som vanliga frågor från [datakällor](../Concepts/data-sources-supported.md). Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran. 

Den här snabbstarten anropar API:er för QnA Maker:
* [Skapa kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Hämta åtgärdsinformation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Node.js 6+](https://nodejs.org/en/download/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på instrumentpanelen. 

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Skapa en Node.js-fil för kunskapsbas

Skapa en fil som heter `create-new-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Högst upp i `create-new-knowledge-base.js` lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter
När du har lagt till nödvändiga beroenden lägger du till de konstanter som krävs för åtkomst till QnA Maker. Ersätt värdet av variabeln `subscriptionKey` med din egen QnA Maker-nyckel.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=10-19 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Lägga till KB-modelldefinitionen

Efter konstanterna lägger du till följande KB-modelldefinition. Modellen konverteras till en sträng efter definitionen.

[!code-nodejs[Add the KB model definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=21-51 "Add the KB model definition")]

## <a name="add-supporting-functions"></a>Lägga till stödfunktioner

Sedan lägger du till följande stödfunktioner.

1. Lägg till följande funktioner för att skriva ut JSON i ett lättläst format:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=53-56 "Add supporting functions, step 1")]

2. Lägg till följande funktioner för att hantera HTTP-svaret:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=58-80 "Add supporting functions, step 2")]

## <a name="add-functions-to-create-kb"></a>Lägga till funktioner för att skapa KB

Lägg till följande funktioner för att göra en HTTP POST-begäran för att skapa kunskapsbasen. `Ocp-Apim-Subscription-Key` är QnA Maker-tjänstenyckeln, som används för autentisering. 

[!code-nodejs[POST Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=82-109 "POST Request to API")]

Detta API-anrop anropar ett JSON-svar som innehåller åtgärds-ID:t. Använd åtgärds-ID:t för att fastställa om KB har skapats. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-functions-to-determine-creation-status"></a>Lägga till funktioner för att fastställa skapandestatus

Lägg till följande funktion för att göra en HTTP GET-begäran för att kontrollera åtgärdsstatusen. `Ocp-Apim-Subscription-Key` är QnA Maker-tjänstenyckeln, som används för autentisering. 

[!code-nodejs[Determine creation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=112-135 "Determine creation status")]

Upprepa anropet tills det lyckas eller misslyckas: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```


## <a name="add-create-kb-function"></a>Lägga till funktionen create-kb

Följande funktion är huvudfunktionen och skapar KB och upprepar kontroller av statusen. _Skapande_**åtgärds-ID:t** returneras i **platsen** för POST-svarets huvudfält och används sedan som en del av vägen i GET-begäran. Eftersom det kan ta lite tid att skapa KB måste du upprepa anrop för att kontrollera status tills statusen antingen lyckas eller misslyckas.

[!code-nodejs[Add create-kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=137-167 "Add create-kb function")]

## <a name="run-the-program"></a>Köra programmet

Ange följande kommando på en kommandorad för att köra programmet. Begäran skickas till API:et för QnA Maker för att skapa KB, och sedan söker den efter resultat var 30:e sekund. Varje svar skrivs ut i konsolfönstret.

```bash
node create-new-knowledge-base.js
```

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser). 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
