---
title: 'Snabbstart: Få svar från kunskapsbas – REST, Node.js – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Denna Node.js REST-baserade snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas programmässigt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: ce8d0d669af93d4886b4bca25f67e9261e5af20a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308127"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Snabbstart: Få svar på en fråga med hjälp av en kunskapsbas med Node.js

Den här snabbstarten vägleder dig genom att programmatiskt hämta ett svar från en publicerad QnA Maker-kunskapsbas. Kunskaps basen innehåller frågor och svar från [data källor](../Concepts/data-sources-supported.md) som vanliga frågor och svar. [Frågan](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) skickas till QNA Maker tjänsten. [Svaret](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) innehåller det mest förväntade svaret. 

## <a name="prerequisites"></a>Förutsättningar

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på Azure-instrumentpanelen för din QnA Maker-resurs. 
* **Publicera** sidinställningar. Om du inte har en publicerad kunskapsbas skapar du en tom kunskapsbas, importerar en kunskapsbas på sidan **Inställningar** och publicerar sedan. Du kan ladda ned och använda [den här grundläggande kunskapsbasen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    Inställningarna för att publicera sida omfattar värden för POST route (POST-väg), Host (Värd) samt EndpointKey (Slutpunktsnyckel). 

    ![Publiceringinställningar](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Koden för den här snabbstarten finns i lagringsplatsen [https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-nodejs-file"></a>Skapa en Node.js-fil

Öppna VSCode och skapa en ny fil med namnet `get-answer.js`. 

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Längst upp i filen `get-answer.js` lägger du till nödvändiga beroenden i projektet:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter

Lägg sedan till de nödvändiga konstanterna för att få åtkomst till QnA Maker. Värdena finns på sidan **Publicera** när du har publicerat kunskapsbasen. 

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Lägg till en POST-begäran för att skicka fråga och få ett svar

Följande kod gör en HTTPS-begäran för API:et för QnA Maker för att skicka frågan till kunskapsbasen och tar emot svaret:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

Värdet för `Authorization`-huvudet innehåller strängen `EndpointKey`. 

## <a name="install-the-dependencies"></a>Installera beroendena

Installera beroenden från kommandoraden:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Köra programmet

Kör programmet från kommandoraden. Det skickar automatiskt begäran till API:et för QnA Maker, och sedan skrivs svaret ut till konsolfönstret.

Kör filen:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Läs mer om [begäran](../how-to/metadata-generateanswer-usage.md#generateanswer-request) och [svar](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
