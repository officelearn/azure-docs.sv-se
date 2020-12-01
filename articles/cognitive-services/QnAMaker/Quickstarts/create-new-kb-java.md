---
title: 'Snabbstart: Skapa kunskapsbas – REST, Java – QnA Maker'
description: Den här Java REST-baserade snabb starten vägleder dig genom att skapa ett exempel QnA Maker kunskaps bas, program mässigt, som visas i Azure-instrumentpanelen för ditt Cognitive Services API-konto.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 50433ee1e5a575ab671d562bfc3fe549b26fe00c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352311"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Snabbstart: Skapa en kunskapsbas i QnA Maker med hjälp av Java

Den här snabbstarten går igenom hur du programmatiskt skapar ett exempel på QnA Maker-kunskapsbas. QnA Maker extraherar automatiskt frågor och svar för delvis strukturerat innehåll, som vanliga frågor från [datakällor](../index.yml). Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran.

Den här snabbstarten anropar API:er för QnA Maker:
* [Skapa kunskapsbas](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Få åtgärdsinformation](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referens dokumentation](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Java-exempel](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Go 1.10.1](https://golang.org/dl/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta din nyckel och slut punkt (som innehåller resurs namnet) väljer du **snabb start** för resursen i Azure Portal.

[Exempel koden](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) finns på GitHub-lagrings platsen för QNA Maker med Java.

## <a name="create-a-knowledge-base-file"></a>Skapa en kunskapsbasfil

Skapa en fil som heter `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Längst upp i `CreateKB.java` lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter
När du har lagt till nödvändiga beroenden lägger du till de konstanter som krävs till klassen `CreateKB` för åtkomst till QnA Maker.

Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln och resurs namnet genom att välja **snabb start** i Azure Portal för din QNA Maker resurs.

Ställ in följande värden:

* `<your-qna-maker-subscription-key>` – **Nyckeln** är en 32 tecken sträng och är tillgänglig i Azure Portal på den QNA Maker resursen på snabb starts sidan. Den här nyckeln är inte samma som för förutsägelse slut punkts nyckeln.
* `<your-resource-name>` – Ditt **resurs namn** används för att skapa slut punkts-URL: en för redigering, i formatet `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Resurs namnet är inte samma som det som används för att fråga efter förutsägelse slut punkten.

Du behöver inte lägga till den sista klammerparentesen för att avsluta klassen. Den finns i det sista kodfragmentet i slutet av den här snabbstarten.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>Lägga till KB-modelldefinitionsklasser
Efter konstanterna lägger du till följande klasser och funktioner i klassen `CreateKB` för att serialisera modelldefinitionsobjektet till JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>Lägga till stödfunktioner

Sedan lägger du till följande stödfunktioner i klassen `CreateKB`.

1. Lägg till följande funktioner för att skriva ut JSON i ett lättläst format:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. Lägg till följande klass för att hantera HTTP-svaret:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. Lägg till följande metod för att göra en POST-begäran för QnA Maker-API: er. `Ocp-Apim-Subscription-Key` är QnA Maker-tjänstenyckeln, som används för autentisering.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. Lägg till följande metod för att göra en GET-begäran för QnA Maker-API: er.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>Lägg till en metod för att skapa KB
Lägg till följande metod för att skapa kunskapsbasen genom att anropa Post-metoden.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

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

## <a name="add-a-method-to-get-status"></a>Lägg till en metod för att hämta status
Lägg till följande metod för att kontrollera statusen för skapandet.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

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

## <a name="add-a-main-method"></a>Lägga till en main-metod
Main-metoden skapar kunskapsbasen och söker sedan efter status. Åtgärds-ID: t returneras i fältet POSTens svars huvud **plats** och används som en del av vägen i get-begäran. `while`Loopen försöker igen om den inte är slutförd.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>Skapa och köra programmet

1. Kontrollera att gson-biblioteket finns i katalogen `./libs`. På kommando raden kompilerar du filen `CreateKB.java` :

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Ange följande kommando på en kommando rad för att köra programmet. Begäran skickas till API:et för QnA Maker för att skapa KB, och sedan söker den efter resultat var 30:e sekund. Varje svar skrivs ut till konsolfönstret.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)