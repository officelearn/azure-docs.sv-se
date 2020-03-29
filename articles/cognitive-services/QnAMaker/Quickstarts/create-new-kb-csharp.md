---
title: 'Snabbstart: Skapa kunskapsbas – REST, C# – QnA Maker'
description: I den här C# REST-baserade snabbstarten går vi igenom hur du skapar ett exempel på QnA Maker-kunskapsbas, programmatiskt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 78608d3acdfea07f21ccc42e8d530ff502fbb0b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851931"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Snabbstart: Skapa en kunskapsbas i QnA Maker med C# med REST

Den här snabbstarten går igenom hur du programmatiskt skapar och publicerar ett exempel på QnA Maker-kunskapsbas. QnA Maker extraherar automatiskt frågor och svar för delvis strukturerat innehåll, som vanliga frågor från [datakällor](../Concepts/knowledge-base.md). Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran.

Den här snabbstarten anropar API:er för QnA Maker:
* [Skapa kunskapsbas](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Få åtgärdsinformation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referensdokumentation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [C# Exempel](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Krav

* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Du måste ha en [QnA Maker-resurs](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta nyckeln och slutpunkten (som innehåller resursnamnet) väljer du **Snabbstart** för din resurs i Azure-portalen.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskad redigerare eller IDE.

I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du `qna-maker-quickstart`kommandot för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

```dotnetcli
dotnet build
```

Byggutdata bör inte innehålla några varningar eller fel.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Överst i Program.cs ersätter du det enskilda using-uttrycket med följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter

Lägg till de konstanter som krävs för att komma åt QnA Maker högst upp i klassen Program.

Ange följande värden i miljövariabler:

* `QNA_MAKER_SUBSCRIPTION_KEY`- **Nyckeln** är en 32 teckensträng och är tillgänglig i Azure-portalen, på QnA Maker-resursen, på snabbstartssidan. Detta är inte samma sak som förutsägelseslutpunktsnyckeln.
* `QNA_MAKER_ENDPOINT`- **Slutpunkten** är url:en för redigering, `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`i formatet . Det här är inte samma URL som används för att fråga ut förutsägelseslutpunkten.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-26 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Lägga till KB-definition

Efter konstanterna lägger du till följande KB-definition:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=28-58 "Add the knowledge base definition")]

## <a name="add-supporting-functions-and-structures"></a>Lägga till funktionerna och strukturer
Lägg till följande kodblock i klassen Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=60-99 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Lägga till en POST-begäran för att skapa KB

Följande kod gör en HTTPS-begäran för API för QnA Maker för att skapa en kunskapsbas och tar emot svaret:

[!code-csharp[Add PostCreateKB to request via POST](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=145-165 "Add PostCreateKB to request via POST")]

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=101-122 "Add a POST request to create KB")]

Detta API-anrop anropar ett JSON-svar som innehåller åtgärds-ID. Använd åtgärds-ID:t för att fastställa om KB har skapats.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Lägga till GET-begäran för att fastställa skapandestatus

Kontrollera status för åtgärden.

[!code-csharp[Add GetStatus to request via GET](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=167-187 "Add GetStatus to request via GET")]

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=124-143 "Add GET request to determine creation status")]

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

## <a name="add-createkb-method"></a>Lägga till metoden CreateKB

Följande metod skapar KB och upprepar kontroller av statusen.  _Skapande_ **åtgärds-ID:t** returneras i **platsen** för POST-svarets huvudfält och används sedan som en del av vägen i GET-begäran. Eftersom KB-skapandet kan ta lite tid måste du upprepa anropen för att kontrollera statusen tills statusen lyckas eller misslyckas. När åtgärden lyckas returneras KB-ID i **resourceLocation**.

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=189-254 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>Lägga till metoden CreateKB till Main

Ändra Main-metoden för att anropa metoden CreateKB:

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=256-265 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet. Begäran skickas automatiskt till API:et för QnA Maker för att skapa KB, och sedan söker den efter resultaten med 30 sekunders mellanrum. Varje svar skrivs ut till konsolfönstret.

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser).


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)