---
title: Skapa, publicera och svara
titleSuffix: QnA Maker - Azure Cognitive Services
description: Den här REST-baserade självstudien vägleder dig genom att programmatiskt skapa och publicera en kunskapsbas och sedan besvara en fråga från kunskapsbasen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 348cb6a451f15ddec6b7ee013832ad6c563bb2da
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874964"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Självstudie: Använda C#, skapa kunskapsbas och sedan besvara fråga

Den här självstudien vägleder dig genom att programmatiskt skapa och publicera en kunskapsbas (KB) och sedan besvara en kundfråga med kunskapsbasen. 

> [!div class="checklist"]
* Skapa en kunskapsbas 
* Kontrollera skapandestatus
* Träna och publicera kunskapsbasen
* Hämta slutpunktsinformation
* Använda Curl för att köra frågor mot kunskapsbasen


Den här snabbstarten anropar API:er för QnA Maker:

* [Skapa kunskapsbas (KB)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Hämta åtgärdsinformation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [Hämta information om kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) 
* [Hämta slutpunkter för kunskapsbas](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys)
* [Publicera](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Senaste [**Visual Studio Community-versionen**](https://www.visualstudio.com/downloads/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på instrumentpanelen. 

> [!NOTE] 
> De fullständiga lösningsfilerna finns i [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub-lagringsplats](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Skapa ett kunskapsbasprojekt

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Överst i Program.cs ersätter du det enskilda _using_-uttrycket med följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Lägga till en KBDetails-klass
Lägg till den här KBDetails-klassen innanför Namespace-klamrarna (namnrymd). Den här klassen gör att NewtonSoft-biblioteket kan deserialisera JSON-svar till en C#-klass.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter

Längst upp i Program-klassen lägger du till följande konstanter för att komma åt QnA Maker:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Lägga till KB-definition

Efter konstanterna lägger du till följande KB-modelldefinition:

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Lägga till funktionerna och strukturer
Lägg till följande kodblock i klassen Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Lägga till en POST-begäran för att skapa KB

Följande kod gör en HTTPS-begäran för API för QnA Maker för att skapa en kunskapsbas och tar emot svaret:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Detta API-anrop anropar ett JSON-svar som innehåller åtgärds-ID. Senare använder programmet åtgärds-ID:t för att fastställa huruvida KB har skapats. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Lägga till GET-begäran för att fastställa skapandestatus

Kontrollera statusen för skapandeåtgärden.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Det här API-anropet returnerar ett JSON-svar som innehåller åtgärdsstatus: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Lägga till metoden CreateKB

Följande metod kapslar in anropen för att skapa KB och kontrollera statusen.  _Skapande_**åtgärds-ID:t** returneras i **platsen** för POST-svarets huvudfält och används sedan som en del av vägen i GET-begäran. Eftersom det kan ta lite tid att skapa KB måste du upprepa anrop för att kontrollera status tills statusen antingen lyckas eller misslyckas. När åtgärden lyckas returneras KB-ID i **resourceLocation**. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Lägga till publiceringsmetod

När kunskapsbasen har skapats publicerar du KB. Du förväntade dig kanske ett anrop till ett tränings-API. Det krävs inte med den här versionen. 

Följande kod gör en HTTPS-begäran för API för QnA Maker för att publicera en kunskapsbas och tar emot svaret:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

API-anropet returnerar 204-status för en lyckad publicering utan något innehåll i svarets brödtext. Snabbstartskoden lägger till text för 204-svaren så att du kan se resultatet.

För andra svar returneras svaret oförändrat.

## <a name="generating-an-answer"></a>Generera ett svar
För att komma åt KB för att skicka en fråga och få det bästa svaret behöver programmet _slutpunktsvärden_ från KB-informations-API:et och _nyckeln för den primära slutpunkten_ från slutpunkts-API:et. Dessa metoder finns i följande avsnitt liksom metoden för att generera ett svar. 

Följande tabell visar hur data används för att konstruera URI:

|Generera mall för svars-URI|
|--|
|https://**HOSTNAME**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

Den _primära slutpunkten_ skickas som huvud för att autentisera begäran att generera ett svar:

|Huvudnamn|Huvudvärde|
|--|--|
|Auktorisering|`Endpoint ` + **primär slutpunkt**<br>Exempel: `Endpoint xxxxxxx`<br>Observera avståndet mellan texten i `Endpoint` och värdet för den primära slutpunkten. 

Brödtexten i begäran måste skicka rätt JSON:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Hämta KB-information
Lägg till följande metod för att hämta KB-informationen. Den här informationen innehåller värdnamnet för KB. Värdnamnet är namnet på den QnA Maker Azure-webbtjänst som angav när du skapade QnA Maker-resursen. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Den här API-anropet returnerar ett JSON-svar: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Hämta KB-slutpunkter
Lägg till följande metod för att hämta de primära slutpunkterna för QnA Maker. De här slutpunkterna är inte kopplade till KB. De är giltiga för alla KB:er som är associerade med QnA Maker-resursnycklar från Azure-portalen.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Den här API-anropet returnerar ett JSON-svar: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Hämta ett svar
Lägg till följande metod för att hämta ett svar på användarens fråga. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Den här API-anropet returnerar ett JSON-svar: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Main-metoden
Main-metoden visar de synkrona anropen för att skapa, publicera och generera svaret. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet. 

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser). När du vet hur du använder API:et för att generera svar kan använda du API:et med valfritt språk eller ramverk för HTTP-begäran. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
