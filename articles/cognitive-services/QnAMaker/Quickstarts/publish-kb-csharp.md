---
title: 'Snabb start: publicera kunskaps bas, REST, C#-QnA Maker'
description: Den här C# REST-baserade snabb starten publicerar din kunskaps bas och skapar en slut punkt som kan anropas i ditt program eller chatt-robot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: ccad31c5a67eaec53ee9366961187bb1ed235283
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934165"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Snabbstart: Publicera en kunskapsbas i QnA Maker med C#

Den här REST-baserade snabbstarten går igenom hur du programmatiskt publicerar din kunskapsbas (KB). Publiceringen skickar push-överför den senaste versionen av kunskaps basen till ett dedikerat Azure Kognitiv sökning-index och skapar en slut punkt som kan anropas i ditt program eller chatt-robot.

Den här snabbstarten anropar API:er för QnA Maker:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Publicera) – detta API kräver inte någon information i brödtexten för begäran.

## <a name="prerequisites"></a>Förutsättningar

* Senaste [**Visual Studio Community-versionen**](https://www.visualstudio.com/downloads/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta din nyckel och slut punkt (som innehåller resurs namnet) väljer du **snabb start** för resursen i Azure Portal.
* QnA Maker kunskaps bas-ID (KB) hittades i URL: en i frågesträngparametern `kbid` som visas nedan.

    ![QnA Maker-kunskapsbas-ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Om du inte har en kunskapsbas ännu kan du kan skapa en exempelkunskapsbas för den här snabbstarten: [Skapa en ny kunskapsbas](create-new-kb-csharp.md).

> [!NOTE]
> Fullständiga lösnings fil (er) är tillgängliga från databasen [ **Azure-samples/kognitiv-Services-qnamaker-csharp** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Skapa kunskapsbasprojekt

1. Öppna Visual Studio 2019 Community Edition.
1. Skapa ett nytt **konsol program (.net Core)-** projekt och ge projektet ett namn `QnaMakerQuickstart` . Godkänn standardinställningarna för de återstående inställningarna.

## <a name="add-required-dependencies"></a>Lägga till nödvändiga beroenden

Överst i Program.cs ersätter du det enskilda using-uttrycket med följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Lägg till nödvändiga konstanter

I **program** -klassen lägger du till de konstanter som krävs för att få åtkomst till QNA Maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Lägg till main-metoden för att publicera kunskaps basen

Efter konstanterna som krävs lägger du till följande kod, som gör en HTTPS-begäran för API:et för QnA Maker för att publicera en kunskapsbas och tar emot svaret:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

API-anropet returnerar 204-status för en lyckad publicering utan något innehåll i svarets brödtext.

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet. Det skickar automatiskt begäran till API för QnA Maker för att publicera kunskapsbasen, och sedan skrivs svaret ut till konsolfönstret.

När din kunskapsbas har publicerats kan köra frågor mot den från slutpunkten med ett klientprogram eller en chattrobot.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

När kunskapsbasen publiceras behöver du [slutpunktens webbadress för att generera ett svar](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)