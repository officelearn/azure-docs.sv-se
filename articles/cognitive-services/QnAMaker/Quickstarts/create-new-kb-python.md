---
title: 'Snabbstart: Skapa kunskapsbas – REST, Python – QnA Maker'
titlesuffix: Azure Cognitive Services
description: I den här Python REST-baserade snabbstarten går vi igenom hur du skapar ett exempel på QnA Maker-kunskapsbas, programmatiskt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 766fe32736ae0d8ea23805ea017d93b32119636a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60920018"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Snabbstart: Skapa en kunskapsbas i QnA Maker med Python

Den här snabbstarten går igenom hur du programmatiskt skapar och publicerar ett exempel på QnA Maker-kunskapsbas. QnA Maker extraherar automatiskt frågor och svar för delvis strukturerat innehåll, som vanliga frågor från [datakällor](../Concepts/data-sources-supported.md). Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran. 

Den här snabbstarten anropar API:er för QnA Maker:
* [Skapa kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Hämta åtgärdsinformation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 3.7](https://www.python.org/downloads/)
* Du måste ha en QnA Maker-tjänst. Hämta nyckeln genom att välja Nycklar under Resurshantering på instrumentpanelen.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="create-a-knowledge-base-python-file"></a>Skapa en Python-fil för kunskapsbas

Skapa en fil som heter `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Högst upp i `create-new-knowledge-base-3x.py` lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter
När du har lagt till nödvändiga beroenden lägger du till de konstanter som krävs för åtkomst till QnA Maker. Ersätt värdet av variabeln `subscriptionKey` med din egen QnA Maker-nyckel.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Lägga till KB-modelldefinitionen

Efter konstanterna lägger du till följande KB-modelldefinition. Modellen konverteras till en sträng efter definitionen.

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>Lägga till stödfunktion

Lägg till följande funktioner för att skriva ut JSON i ett lättläst format:

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>Lägga till funktioner för att skapa KB

Lägg till följande funktioner för att göra en HTTP POST-begäran för att skapa kunskapsbasen. Detta API-anrop anropar ett JSON-svar som innehåller åtgärds-ID i rubrikfältet **Plats**. Använd åtgärds-ID:t för att fastställa om KB har skapats. `Ocp-Apim-Subscription-Key` är QnA Maker-tjänstenyckeln, som används för autentisering. 

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

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

## <a name="add-function-to-check-creation-status"></a>Lägga till funktion för att kontrollera skapandestatus

Följande funktion kontrollerar skapandestatusen som skickar åtgärds-ID i slutet av URL-vägen. Anropet till `check_status` är inuti _huvudwhileloopen_.

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

Det här API-anropet returnerar ett JSON-svar som innehåller åtgärdsstatus: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

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

## <a name="add-main-code-block"></a>Lägg till huvudsakligt kodblock
Följande loop söker efter statusen för skapandeåtgärden regelbundet tills åtgärden har slutförts. 

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Ange följande kommando på en kommandorad för att köra programmet. Begäran skickas till API:et för QnA Maker för att skapa KB, och sedan söker den efter resultat var 30:e sekund. Varje svar skrivs ut i konsolfönstret.

```bash
python create-new-knowledge-base-3x.py
```

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser). Välj namnet på kunskapsbasen, till exempel QnA Maker Vanliga frågor och svar, för att visa.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
