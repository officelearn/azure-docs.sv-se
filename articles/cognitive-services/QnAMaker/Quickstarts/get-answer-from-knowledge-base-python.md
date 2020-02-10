---
title: 'Snabbstart: Få svar från kunskapsbas – REST, Python – QnA Maker'
description: Denna Python REST-baserade snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas programmässigt.
ms.topic: quickstart
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCHANGE-20200128
ms.openlocfilehash: 7fe1ed140c53f93f2bd00847c5652906de998147
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109701"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Snabb start: få svar på en fråga från en kunskaps bas med python

Den här snabbstarten vägleder dig genom att programmatiskt hämta ett svar från en publicerad QnA Maker-kunskapsbas. Kunskaps basen innehåller frågor och svar från [data källor](../Concepts/knowledge-base.md) som vanliga frågor och svar. [Frågan](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) skickas till QNA Maker tjänsten. [Svaret](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) innehåller det mest förväntade svaret.

[Referens dokumentation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [exempel](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>Förutsättningar

* [Python 3.6 eller senare](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på Azure-instrumentpanelen för din QnA Maker-resurs.
* **Publicera** sidinställningar. Om du inte har en publicerad kunskapsbas skapar du en tom kunskapsbas, importerar en kunskapsbas på sidan **Inställningar** och publicerar sedan. Du kan ladda ned och använda [den här grundläggande kunskapsbasen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    Inställningarna för att publicera sida omfattar värden för POST route (POST-väg), Host (Värd) samt EndpointKey (Slutpunktsnyckel).

    ![Publiceringinställningar](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Skapa en Python-fil

Öppna VSCode och skapa en ny fil med namnet `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Längst upp i filen `get-answer-3x.py` lägger du till nödvändiga beroenden i projektet:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

<!--TBD - reword this following paragraph -->

Värden och vägen skiljer sig från hur de visas på sidan **Publicera**. Det beror på att Python-bibliotek inte tillåter någon routning i värden. Routningen som visas på sidan **Publicera** som en del av värden har flyttats till flödet.

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter

Lägg till de nödvändiga konstanterna för att få åtkomst till QnA Maker. De här värdena finns på sidan **Publicera** när du har publicerat kunskapsbasen.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Lägg till en POST-begäran för att skicka fråga och få ett svar

Följande kod gör en HTTPS-begäran till API för QnA Maker för att skicka frågan till kunskapsbasen och tar emot svaret:

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

Värdet för `Authorization`-huvudet innehåller strängen `EndpointKey`.

## <a name="run-the-program"></a>Köra programmet

Kör programmet från kommandoraden. Det skickar automatiskt begäran till API:et för QnA Maker, och sedan skrivs svaret ut till konsolfönstret.

Kör filen:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Läs mer om [begäran](../how-to/metadata-generateanswer-usage.md#generateanswer-request) och [svar](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
