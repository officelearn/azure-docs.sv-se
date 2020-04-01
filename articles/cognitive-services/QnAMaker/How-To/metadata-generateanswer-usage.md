---
title: Metadata med GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: Med QnA Maker kan du lägga till metadata i form av nyckel-/värdepar i dina fråge-/svarsuppsättningar. Du kan filtrera resultat till användarfrågor och lagra ytterligare information som kan användas i uppföljningskonversationer.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: diberry
ms.openlocfilehash: 9beb6dbbba1c5855b8bfa97fc02f50aa59225d78
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474850"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Få svar med GenerateAnswer API och metadata

Om du vill få det förväntade svaret på en användares fråga använder du GenerateAnswer-API:et. När du publicerar en kunskapsbas kan du se information om hur du använder det här API:et på sidan **Publicera.** Du kan också konfigurera API:et för att filtrera svar baserat på metadatataggar och testa kunskapsbasen från slutpunkten med parametern testfrågesträng.

Med QnA Maker kan du lägga till metadata, i form av nyckel- och värdepar, i dina uppsättningar med frågor och svar. Du kan sedan använda den här informationen för att filtrera resultat till användarfrågor och för att lagra ytterligare information som kan användas i uppföljningskonversationer. Mer information finns i [Kunskapsbas](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Lagra frågor och svar med en QnA-enhet

Det är viktigt att förstå hur QnA Maker lagrar frågan och svarsdata. Följande bild visar en QnA-enhet:

![Illustration av en QnA-enhet](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Varje QnA-entitet har ett unikt och beständigt ID. Du kan använda ID:t för att göra uppdateringar till en viss QnA-entitet.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Få svarsprognoser med GenerateAnswer API

Du använder [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) i din bot eller program för att fråga din kunskapsbas med en användarfråga, för att få den bästa matchningen från fråga och svarsuppsättningar.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicera för att hämta GenerateAnswer-slutpunkt

När du har publicerat kunskapsbasen, antingen från [QnA Maker-portalen](https://www.qnamaker.ai)eller med hjälp av [API:et,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)kan du få information om endpointen GenerateAnswer.

Så här hämtar du information om slutpunkten:
1. Logga in [https://www.qnamaker.ai](https://www.qnamaker.ai)på .
1. I **Mina kunskapsbaser**väljer du **Visa kod** för din kunskapsbas.
    ![Skärmbild av Mina kunskapsbaser](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Hämta dina GenerateAnswer-slutpunktsinformation.

    ![Skärmbild av slutpunktsinformation](../media/qnamaker-how-to-metadata-usage/view-code.png)

Du kan också hämta dina slutpunktsinformationer från fliken **Inställningar** i din kunskapsbas.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfiguration av GenerateAnswer-begäran

Du anropar GenerateAnswer med en HTTP POST-begäran. Exempel på kod som visar hur du anropar GenerateAnswer finns i [snabbstarterna](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

POST-begäran använder:

* Obligatoriska [URI-parametrar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Obligatorisk huvudegenskap, `Authorization`för säkerhet
* Obligatoriska [kroppsegenskaper](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

GenerateAnswer-URL:en har följande format:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Kom ihåg att ställa `Authorization` in HTTP-rubrikgenskapen med ett värde i strängen `EndpointKey` med ett avslutande blanksteg och sedan slutpunktsnyckeln som finns på sidan **Inställningar.**

Ett exempel JSON kropp ser ut:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Läs mer om [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Den tidigare JSON begärde endast svar som ligger på 30% eller över tröskelvärdet poäng.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Generera Svarsegenskaper för GenereraAnswer

[Svaret](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) är ett JSON-objekt med all information du behöver för att visa svaret och nästa sväng i konversationen, om tillgängligt.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
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

Den tidigare JSON svarade med ett svar med poängen 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>Använd QnA Maker med en bot i C #

Bot-ramverket ger tillgång till QnA Maker egenskaper med [getAnswer API:](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)

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

Den tidigare JSON begärde endast svar som ligger på 30% eller över tröskelvärdet poäng.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Använd QnA Maker med en bot i Node.js

Bot-ramverket ger tillgång till QnA Maker egenskaper med [getAnswer API:](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Den tidigare JSON begärde endast svar som ligger på 30% eller över tröskelvärdet poäng.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Använda metadata för att filtrera svar efter anpassade metadatataggar

Genom att lägga till metadata kan du filtrera svaren efter dessa metadatataggar. Lägg till metadatakolumnen på **menyn Visa alternativ.** Lägg till metadata i kunskapsbasen genom att välja metadataikonen **+** för att lägga till ett metadatapar. Det här paret består av en nyckel och ett värde.

![Skärmbild av hur du lägger till metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrera resultat med striktFilter för metadatataggar

Tänk på användarfrågan "När stänger detta hotell?", där avsikten är underförstådd för restaurangen "Paradise".

Eftersom resultat endast krävs för restaurangen "Paradise" kan du ställa in ett filter i GenerateAnswer-anropet på metadata "Restaurangnamn". Följande exempel visar detta:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Använd fråge- och svarsresultat för att hålla konversationskontexten

Svaret på GenerateAnswer innehåller motsvarande metadatainformation för den matchade fråge- och svarsuppsättningen. Du kan använda den här informationen i klientprogrammet för att lagra kontexten för föregående konversation för användning i senare konversationer.

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

Som standard söker QnA Maker igenom frågor och svar. Om du bara vill söka igenom frågor, för `RankerType=QuestionOnly` att generera ett svar, använd i post-brödtexten för GenerateAnswer-begäran.

Du kan söka igenom den `isTest=false`publicerade kb, använda `isTest=true`eller i testet kb med .

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
|2xx (2xx)|Lyckades|
|400|Parametrarna för begäran är felaktiga, vilket innebär att de parametrar som krävs saknas, är felaktiga eller för stora|
|400|Begärans kropp är felaktig vilket innebär att JSON saknas, missbildas eller är för stor|
|401|Ogiltig nyckel|
|403|Förbjudet - du har inte rätt behörighet|
|404|KB finns inte|
|410|Detta API är inaktuellt och är inte längre tillgängligt|

## <a name="next-steps"></a>Nästa steg

På sidan **Publicera** finns också information för att [generera ett svar](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) med Postman eller cURL.

> [!div class="nextstepaction"]
> [Skapa en kunskapsbasro](../tutorials/integrate-qnamaker-luis.md)
