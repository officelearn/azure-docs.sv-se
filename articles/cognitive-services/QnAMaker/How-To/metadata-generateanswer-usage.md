---
title: Metadata med GenerateAnswer API – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker kan du lägga till metadata i form av nyckel/värde-par för din fråga/svar-uppsättningar. Du kan filtrera resultaten på användarförfrågningar och lagra ytterligare information som kan användas i Uppföljnings konversationer.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: b691d447f51165ea3cb56410da9cd2d4d00ce913
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490191"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Få ett svar med GenerateAnswer API och metadata

Använd GenerateAnswer API för att få förväntade svaret på en användares fråga. När du publicerar en kunskapsbas visas information om hur du använder detta API på den **publicera** sidan. Du kan också konfigurera API för att filtrera svar baserat på metadatataggar och testa kunskapsbas från slutpunkten med test-frågesträngparametern.

QnA Maker kan du lägga till metadata i form av nyckel / värdepar för din uppsättningar av frågor och svar. Du kan sedan använda den här informationen för att filtrera resultat på användarförfrågningar och för att lagra ytterligare information som kan användas i Uppföljnings konversationer. Mer information finns i [kunskapsbas](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Store frågor och svar med en entitet för frågor och svar

Det är viktigt att förstå hur QnA Maker lagrar frågor och svar. Följande bild visar en frågor och svar om entitet:

![Bild av en QnA-entitet](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Varje entitet för frågor och svar har ett unikt och beständiga-ID. Du kan använda det ID: T för att göra uppdateringar för en viss affärsenhet i frågor och svar.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Få svar förutsägelser med GenerateAnswer-API

Du använder den [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) i din robot eller program för att fråga din kunskapsbas med en fråga för användare att få bästa möjliga matchning från frågor och svar anger.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicera för att hämta GenerateAnswer slutpunkten 

När du har publicerat din kunskapsbas, antingen från den [QnA Maker portal](https://www.qnamaker.ai), eller genom att använda den [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), du kan hämta information om din GenerateAnswer slutpunkt.

Hämta information om din slutpunkt:
1. Logga in på [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. I **min kunskapsbaser**väljer **Visa kod** för kunskapsbasen.
    ![Skärmbild av Mina kunskapsbaser](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Får tillgång till dina GenerateAnswer slutpunkt.

    ![Skärmbild av information om slutpunkten](../media/qnamaker-how-to-metadata-usage/view-code.png)

Du kan också få din slutpunktsinformation från den **inställningar** fliken i kunskapsbasen.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer begäran-konfiguration

Du kan anropa GenerateAnswer med en HTTP POST-begäran. Exempel som visar hur du anropar GenerateAnswer, finns det [snabbstarter](../quickstarts/csharp.md). 

POST-begäran använder:

* Krävs [URI-parametrar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Krävs [rubrik egenskapen](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, för säkerhet
* Krävs [body egenskaper](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

GenerateAnswer-URL har följande format: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Kom ihåg att ange egenskapen HTTP-huvud för `Authorization` med ett värde av strängen `EndpointKey` med ett avslutande blanksteg sedan slutpunktsnyckeln hittades på den **inställningar** sidan.

Det ser ut som ett exempel på JSON-texten:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer responsegenskaperna

Den [svar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful_query) är en JSON-objekt, inklusive all information du behöver för att visa svaret och nästa aktiverar i en konversation, om det är tillgängligt.

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

## <a name="use-qna-maker-with-a-bot-in-c"></a>Använd QnA Maker med en bot iC#

I bot framework ger åtkomst till QnA Maker egenskaper:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Har stöd för bot [ett exempel](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) med den här koden.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Använd QnA Maker med en bot i Node.js

I bot framework ger åtkomst till QnA Maker egenskaper:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Har stöd för bot [ett exempel](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) med den här koden.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Använda metadata för att filtrera svar efter anpassade metadatataggar

Att lägga till metadata kan du filtrera svar efter dessa metadatataggar. Lägga till metadatakolumnen från den **Visningsalternativ** menyn. Lägga till metadata i kunskapsbasen genom att välja metadata **+** ikon för att lägga till ett metadata-par. Den här par består av en nyckel och ett värde.

![Skärmbild för att lägga till metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrera resultaten med strictFilters för metadatataggarna

Överväg att användaren frågan ”när gör den här hotell Stäng”?, där avsikten är angiven för restaurang ”Paradise”.

Eftersom resultat krävs endast för restaurang ”Paradise” kan definiera du ett filter i GenerateAnswer anropet om metadata som ”restaurang-Name”. I följande exempel visar detta:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Använda frågor och svar resultat för att hålla konversationen kontext

Svaret på GenerateAnswer innehåller motsvarande metadatainformation för uppsättningen matchade frågor och svar. Du kan använda den här informationen i klientprogrammet för att lagra kontexten för den tidigare konversationen för användning i senare konversationer. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Matcha frågor endast genom text

Som standard söker QnA Maker genom frågor och svar. Om du vill söka igenom frågor endast kan använda för att generera ett svar på `RankerType=QuestionOnly` i själva POST GenerateAnswer begäran.

Du kan söka igenom de publicerade kb med `isTest=false`, eller i kb test med `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Nästa steg

Den **publicera** sidan innehåller även information om du vill generera ett svar med [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) och [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](./create-knowledge-base.md)
