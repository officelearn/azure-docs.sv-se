---
title: 'Snabbstart: Skapa kunskapsbas – REST, C# – QnA Maker'
description: I den här C# REST-baserade snabbstarten går vi igenom hur du skapar ett exempel på QnA Maker-kunskapsbas, programmatiskt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: e31345c3b83e1ff5e01952d69dde9353b8234757
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338120"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Snabb start: skapa en kunskaps bas i QnA Maker använda C# med REST

Den här snabbstarten går igenom hur du programmatiskt skapar och publicerar ett exempel på QnA Maker-kunskapsbas. QnA Maker extraherar automatiskt frågor och svar för delvis strukturerat innehåll, som vanliga frågor från [datakällor](../index.yml). Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran.

Den här snabbstarten anropar API:er för QnA Maker:
* [Skapa kunskapsbas](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Få åtgärdsinformation](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referens dokumentation](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [C#-exempel](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* Du måste ha en [QNA Maker-resurs](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta din nyckel och slut punkt (som innehåller resurs namnet) väljer du **snabb start** för resursen i Azure Portal.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE.

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `qna-maker-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```dotnetcli
dotnet build
```

Build-utdata får inte innehålla varningar eller fel.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Överst i Program.cs ersätter du det enskilda using-uttrycket med följande rader för att lägga till nödvändiga beroenden i projektet:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter

Lägg till de konstanter som krävs för att få åtkomst till QnA Maker överst i program klassen.

Ange följande värden i miljövariabler:

* `QNA_MAKER_SUBSCRIPTION_KEY` – **Nyckeln** är en 32 tecken sträng och är tillgänglig i Azure Portal på den QNA Maker resursen på snabb starts sidan. Detta är inte samma som för förutsägelse slut punkts nyckel.
* `QNA_MAKER_ENDPOINT` - **Slut punkten** är URL: en för redigering, i formatet `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Detta är inte samma URL som används för att fråga efter förutsägelse slut punkten.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="constants":::

## <a name="add-the-kb-definition"></a>Lägga till KB-definition

Efter konstanterna lägger du till följande KB-definition:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="kb":::

## <a name="add-supporting-functions-and-structures"></a>Lägga till funktionerna och strukturer
Lägg till följande kodblock i klassen Program:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="support":::

## <a name="add-a-post-request-to-create-kb"></a>Lägga till en POST-begäran för att skapa KB

Följande kod gör en HTTPS-begäran för API för QnA Maker för att skapa en kunskapsbas och tar emot svaret:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post_create_kb":::

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

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get_status":::

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

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="create_kb":::

## <a name="add-the-createkb-method-to-main"></a>Lägga till metoden CreateKB till Main

Ändra Main-metoden för att anropa metoden CreateKB:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="main":::

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet. Begäran skickas automatiskt till API:et för QnA Maker för att skapa KB, och sedan söker den efter resultaten med 30 sekunders mellanrum. Varje svar skrivs ut till konsolfönstret.

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser).


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)