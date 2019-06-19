---
title: Metadata med GenerateAnswer API – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker kan du lägga till metadata i form av nyckel/värde-par för din fråga/svar-uppsättningar. Du kan filtrera resultaten på användarförfrågningar och lagra ytterligare information som kan användas i Uppföljnings konversationer.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/17/2019
ms.author: tulasim
ms.openlocfilehash: d1e7a29e4ca94405e2d6b2000309ef6e2c3a777c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164616"
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

Du använder GenerateAnswer-API: er i din robot eller ditt program för att fråga din kunskapsbas med en användare-fråga, för att få bästa möjliga matchning från frågor och svar anger.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicera för att hämta GenerateAnswer slutpunkten 

När du har publicerat din kunskapsbas, antingen från den [QnA Maker portal](https://www.qnamaker.ai), eller genom att använda den [API](https://go.microsoft.com/fwlink/?linkid=2092179), du kan hämta information om din GenerateAnswer slutpunkt.

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

Den **URL för begäran** har följande format: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|Egenskapen för HTTP-begäran|Namn|Typ|Syfte|
|--|--|--|--|
|URL-parameter för väg|Kunskapsbas-ID|string|GUID för kunskapsbasen.|
|URL-parameter för väg|QnAMaker slutpunktsvärd|string|Värdnamnet för den slutpunkt som distribuerats i din Azure-prenumeration. Detta är tillgängligt på den **inställningar** sidan när du har publicerat i knowledge base. |
|Huvud|Content-Type|string|Medietyp i texten som skickas till API: et. Standardvärdet är: ''|
|Huvud|Auktorisering|string|Din slutpunktsnyckeln (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Publicera brödtext|JSON-objekt|JSON|Fråga med inställningar.|


JSON-texten har flera inställningar:

|Brödtext JSON-egenskap|Obligatoriskt|Typ|Syfte|
|--|--|--|--|
|`question`|Krävs|string|En användare-fråga som ska skickas till din kunskapsbas.|
|`top`|Valfritt|heltal|Antal översta resultat ska ingå i utdata. Standardvärdet är 1.|
|`userId`|Valfritt|string|Ett unikt ID för att identifiera användaren. Detta ID kommer att läggas till i chattloggarna.|
|`scoreThreshold`|Valfritt|heltal|Endast svar med förtroendepoäng ovanför denna tröskel returneras. Standardvärdet är 0.|
|`isTest`|Valfritt|Boolean|Om värdet är true, returnerar resultat från `testkb` Search-index i stället för publicerade index.|
|`strictFilters`|Valfritt|string|Om anges talar du om QnA Maker att returnera endast de svar som har angivna metadata. Använd `none` som visar svaret ska ha inga filter för filmetadata. |
|`RankerType`|Valfritt|string|Om tillhörigheten `QuestionOnly`, talar om för QnA Maker att söka efter frågor endast. Om inte anges söker QnA Maker frågor och svar.

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

Ett lyckat svar returneras statusen 200 och ett JSON-svar. 

|Svar-egenskap (sorterat efter bedömning)|Syfte|
|--|--|
|poäng|En rangordning mellan 0 och 100.|
|Id|Ett unikt ID för svaret.|
|Frågor|Frågor som anges av användaren.|
|Svar|Svaret på frågan.|
|source|Namnet på källan svaret har extraherats, eller sparats i knowledge base.|
|metadata|Metadata som associeras med svaret.|
|metadata.name|Namn på metadata. (string, maximal längd: 100 krävs)|
|metadata.value|Metadata-värde. (string, maximal längd: 100 krävs)|


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
