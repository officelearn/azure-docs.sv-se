---
title: 'Snabbstart: Skapa kunskapsbas – REST, Go – QnA Maker'
titlesuffix: Azure Cognitive Services
description: I den här Go REST-baserade snabbstarten går vi igenom hur du skapar ett exempel på QnA Maker-kunskapsbas, programmatiskt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: cfb8cb60a828220ea681ad06e86588d3453b8d1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313247"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Snabbstart: Skapa en kunskapsbas i QnA Maker med hjälp av Go

Den här snabbstarten går igenom hur du programmatiskt skapar ett exempel på QnA Maker-kunskapsbas. QnA Maker extraherar automatiskt frågor och svar för delvis strukturerat innehåll, som vanliga frågor från [datakällor](../Concepts/data-sources-supported.md). Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran. 

Den här snabbstarten anropar API:er för QnA Maker:
* [Skapa kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Hämta åtgärdsinformation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Go 1.10.1](https://golang.org/dl/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på instrumentpanelen. 

## <a name="create-a-knowledge-base-go-file"></a>Skapa en Go-fil för kunskapsbas

Skapa en fil som heter `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Högst upp i `create-new-knowledge-base.go` lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter
När du har lagt till nödvändiga beroenden lägger du till de konstanter som krävs för åtkomst till QnA Maker. Ersätt värdet av variabeln `subscriptionKey` med din egen QnA Maker-nyckel.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Lägga till KB-modelldefinitionen
Efter konstanterna lägger du till följande KB-modelldefinition. Modellen konverteras till en sträng efter definitionen.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Lägga till strukturer och funktioner

Sedan lägger du till följande stödfunktioner.

1. Lägg till strukturen för en HTTP-förfrågan:

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Lägg till följande metod för att hantera en POST för QnA Maker-API: er. I den här snabbstarten används POST för att skicka KB-definition till QnA Maker.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Lägg till följande metod för att hantera en GET för QnA Maker-API: er. I den här snabbstarten används GET för att kontrollera statusen för skapandeåtgärden. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Lägga till funktioner för att skapa KB

Lägg till följande funktioner för att göra en HTTP POST-begäran för att skapa kunskapsbasen. _Skapande_**åtgärds-ID:t** returneras i **platsen** för POST-svarets huvudfält och används sedan som en del av vägen i GET-begäran. `Ocp-Apim-Subscription-Key` är QnA Maker-tjänstenyckeln, som används för autentisering. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

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

## <a name="add-function-to-get-status"></a>Lägga till funktion för att hämta status

Lägg till följande funktion för att göra en HTTP GET-begäran för att kontrollera åtgärdsstatusen. `Ocp-Apim-Subscription-Key` är QnA Maker-tjänstenyckeln, som används för autentisering. 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>Lägga till Main-funktion

Följande funktion är huvudfunktionen och skapar KB och upprepar kontroller av statusen. Eftersom det kan ta lite tid att skapa KB måste du upprepa anrop för att kontrollera status tills statusen antingen lyckas eller misslyckas.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>Kompilera programmet
Ange följande kommando för att kompilera filen. Kommandotolken returnerar inte någon information för ett lyckat bygge.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Köra programmet

Ange följande kommando på en kommandorad för att köra programmet. Begäran skickas till API:et för QnA Maker för att skapa KB, och sedan söker den efter resultat var 30:e sekund. Varje svar skrivs ut i konsolfönstret.

```bash
go run create-new-knowledge-base
```

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser). 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
