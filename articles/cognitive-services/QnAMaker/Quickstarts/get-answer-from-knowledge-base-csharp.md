---
title: 'Snabbstart: Få svar från kunskapsbas – REST, C# – QnA Maker'
description: Denna C# REST-baserade snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas programmässigt.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 16093ec5e837b098da3c9b038fe2a57cd76c7151
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851810"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Snabbstart: Få svar på en fråga från en kunskapsbas med C #

Den här snabbstarten vägleder dig genom att programmatiskt hämta ett svar från en publicerad QnA Maker-kunskapsbas. Kunskapsbasen innehåller frågor och svar från [datakällor](../Concepts/knowledge-base.md) som vanliga frågor och svar. [Frågan](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) skickas till QnA Maker-tjänsten. [Svaret](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) innehåller det mest förutsedda svaret.

[Exempel](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [på referensdokumentation](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>Krav

* Senaste [**Visual Studio Community-versionen**](https://www.visualstudio.com/downloads/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på Azure-instrumentpanelen för din QnA Maker-resurs.
* **Publicera** sidinställningar. Om du inte har en publicerad kunskapsbas skapar du en tom kunskapsbas, importerar en kunskapsbas på sidan **Inställningar** och publicerar sedan. Du kan ladda ned och använda [den här grundläggande kunskapsbasen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    Inställningarna för att publicera sida omfattar värden för POST route (POST-väg), Host (Värd) samt EndpointKey (Slutpunktsnyckel).

    ![Publiceringinställningar](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>Skapa ett kunskapsbasprojekt

1. Öppna Visual Studio 2019 Community edition.
1. Skapa ett nytt Console App-projekt (.NET Core) och namnge projektet QnaMakerQuickstart. Godkänn standardinställningarna för de återstående inställningarna.

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Överst i Program.cs-filen ersätter du det enskilda using-uttrycket med följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter

Högst upp i klassen `Program`, i `Main`, lägger du sedan till de nödvändiga konstanterna för att få åtkomst till QnA Maker. Värdena finns på sidan **Publicera** när du har publicerat kunskapsbasen.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=9-41 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Lägg till en POST-begäran för att skicka fråga och få svar

Följande kod gör en HTTPS-begäran för API:et för QnA Maker för att skicka frågan till kunskapsbasen och tar emot svaret:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=43-76 "Add a POST request to send question to knowledge base")]

Värdet för `Authorization`-huvudet innehåller strängen `EndpointKey`.

Läs mer om [begäran](../how-to/metadata-generateanswer-usage.md#generateanswer-request) och [svar](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet från Visual Studio. Det skickar automatiskt begäran till API:et för QnA Maker, och sedan skrivs svaret ut till konsolfönstret.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)