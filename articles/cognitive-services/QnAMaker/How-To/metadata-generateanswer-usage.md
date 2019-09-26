---
title: Metadata med GenerateAnswer-API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Med QnA Maker kan du lägga till metadata, i form av nyckel/värde-par, till din fråga/svars uppsättning. Du kan filtrera resultat till användar frågor och lagra ytterligare information som kan användas i uppföljnings konversationer.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 2f9b624ffcc04963046ad817bb2bc9c025161506
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300255"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Få ett svar med GenerateAnswer-API och metadata

Använd GenerateAnswer-API: et för att få det förväntade svaret till en användares fråga. När du publicerar en kunskaps bas kan du se information om hur du använder det här API: et på sidan **publicera** . Du kan också konfigurera API: et för att filtrera svar baserat på metadata-Taggar och testa kunskaps basen från slut punkten med parametern testa frågesträngen.

Med QnA Maker kan du lägga till metadata, i form av nyckel-och värdepar, till dina frågor och svar. Du kan sedan använda den här informationen för att filtrera resultat till användar frågor och lagra ytterligare information som kan användas i uppföljnings konversationer. Mer information finns i [Knowledge Base](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Lagra frågor och svar med en QnA-entitet

Det är viktigt att förstå hur QnA Maker lagrar frågan och svars data. Följande bild visar en QnA-entitet:

![Illustration av en QnA-entitet](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Varje QnA-entitet har ett unikt och beständigt ID. Du kan använda ID: t för att göra uppdateringar till en viss QnA-entitet.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Få svars prognoser med GenerateAnswer-API: et

Du kan använda [GenerateAnswer-API: et](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) i din robot eller ditt program för att fråga din kunskaps bas med en användar fråga, för att få den bästa matchningen från frågan och svars uppsättningarna.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicera för att hämta GenerateAnswer-slutpunkt 

När du har publicerat din kunskaps bas, antingen från [QNA Maker-portalen](https://www.qnamaker.ai)eller med hjälp av API: [et](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), kan du hämta information om din GenerateAnswer-slutpunkt.

Så här hämtar du information om slut punkten:
1. Logga in på [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. I **Mina kunskaps baser**väljer du **Visa kod** för din kunskaps bas.
    ![Skärm bild av mina kunskaps baser](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Hämta information om din GenerateAnswer-slutpunkt.

    ![Skärm bild av slut punkts information](../media/qnamaker-how-to-metadata-usage/view-code.png)

Du kan också hämta information om slut punkt från fliken **Inställningar** i din kunskaps bas.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfiguration av GenerateAnswer-begäran

Du anropar GenerateAnswer med en HTTP POST-begäran. Exempel kod som visar hur du anropar GenerateAnswer finns i [snabb starterna](../quickstarts/create-publish-kb-csharp-sdk.md#generate-an-answer-from-the-knowledge-base). 

POST-begäran använder:

* Obligatoriska [URI-parametrar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Nödvändig [huvud egenskap](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, för säkerhet
* Egenskaper för nödvändig [brödtext](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

GenerateAnswer-URL: en har följande format: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Kom ihåg att ange egenskapen HTTP-huvud `Authorization` för med ett värde i strängen `EndpointKey` med ett avslutande blank steg och sedan den slut punkts nyckel som finns på sidan **Inställningar** .

Ett exempel på en JSON-text ser ut så här:

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

## <a name="generateanswer-response-properties"></a>Egenskaper för GenerateAnswer-svar

[Svaret](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) är ett JSON-objekt, inklusive all information som du behöver för att Visa svaret och nästa gång i konversationen, om det är tillgängligt.

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

## <a name="use-qna-maker-with-a-bot-in-c"></a>Använda QnA Maker med en robot iC#

Bot Framework ger till gång till QnA Maker egenskaper:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Support-roboten innehåller [ett exempel](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) med den här koden.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Använda QnA Maker med en robot i Node. js

Bot Framework ger till gång till QnA Maker egenskaper:

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

Support-roboten innehåller [ett exempel](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) med den här koden.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Använd metadata för att filtrera svar efter anpassade metadata-Taggar

Genom att lägga till metadata kan du filtrera Svaren efter dessa metadata-taggar. Lägg till kolumnen metadata från menyn **visnings alternativ** . Lägg till metadata i kunskaps basen genom att välja **+** metadata-ikonen för att lägga till ett metadata-par. Det här paret består av en nyckel och ett värde.

![Skärm bild av lägga till metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrera resultat med strictFilters för metadata-Taggar

Fundera över användar frågan "när den här hotellet stängs?", där avsikten är underförstådd med restaurang "Paradise".

Eftersom resultat endast krävs för restaurang "Paradise" kan du ange ett filter i GenerateAnswer-anropet för metadata "restaurang namn". I följande exempel visas följande:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Använd fråge-och svars resultat för att hålla konversations kontext

Svaret på GenerateAnswer innehåller motsvarande metadatainformation för den matchade frågan och svars uppsättningen. Du kan använda den här informationen i klient programmet för att lagra kontexten för den tidigare konversationen för användning i senare konversationer. 

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

## <a name="match-questions-only-by-text"></a>Matcha endast frågor, efter text

Som standard söker QnA Maker igenom frågor och svar. Om du bara vill söka igenom frågor, för att generera ett svar, använder `RankerType=QuestionOnly` du i inläggs texten i GenerateAnswer-begäran.

Du kan söka igenom den publicerade KB, använda `isTest=false`eller i test-KB med. `isTest=true`

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Vanliga HTTP-fel

|Kod|Förklaring|
|:--|--|
|2xx|Lyckades|
|400|begärans-parametrarna är felaktiga vilket innebär att de obligatoriska parametrarna är saknas, är för stor eller har en felaktig|
|400|brödtexten för begäran är felaktig JSON finns saknas, är för stor eller har en felaktig|
|401|Ogiltig nyckel|
|403|Tillåts inte - har du inte rätt behörighet|
|404|KB finns inte|
|410|Detta API är föråldrat och är inte längre tillgängligt|

## <a name="next-steps"></a>Nästa steg

På sidan **publicera** finns också information om hur du genererar ett svar med [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) och [sväng](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](./create-knowledge-base.md)
