---
title: 'Snabbstart: Publicera kunskapsbas – REST, C# – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Den här REST-baserade snabbstarten vägleder dig genom publiceringen av din kunskapsbas, som överför den senaste versionen av den testade kunskapsbasen till ett dedikerat Azure Search-index som representerar den publicerade kunskapsbasen. Den skapar även en slutpunkt som kan anropas i ditt program eller en chattrobot.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/18/2018
ms.author: diberry
ms.openlocfilehash: e48f493c08ee96b75c1d418fdbef1d36672a48a3
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52163902"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Snabbstart: Publicera en kunskapsbas i QnA Maker med C#

Den här REST-baserade snabbstarten går igenom hur du programmatiskt publicerar din kunskapsbas (KB). Publicering skickar den senaste versionen av kunskapsbasen till ett dedikerat Azure Search-index och skapar en slutpunkt som kan anropas i ditt program eller en chattrobot.

Den här snabbstarten anropar API:er för QnA Maker:
* [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) (Publicera) – detta API kräver inte någon information i brödtexten för begäran.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Senaste [**Visual Studio Community-versionen**](https://www.visualstudio.com/downloads/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på instrumentpanelen. 
* ID för QnA Maker-kunskapsbas (KB) som finns i URL:en i kbid-frågesträngsparametern enligt nedan.

    ![QnA Maker-kunskapsbas-ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Om du inte har en kunskapsbas ännu kan du kan skapa en exempelkunskapsbas för den här snabbstarten: [Skapa en ny kunskapsbas](create-new-kb-csharp.md).

> [!NOTE] 
> Kompletta lösningsfiler är tillgängliga från [**Azure-Samples/cognitive-services-qnamaker-csharp** Github-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Skapa kunskapsbasprojekt

1. Öppna Visual Studio 2017 Community Edition.
1. Skapa ett nytt **konsolapprojekt (.Net Core)** och ge projektet namnet `QnaMakerQuickstart`. Godkänn standardinställningarna för de återstående inställningarna.

## <a name="add-required-dependencies"></a>Lägga till nödvändiga beroenden

Överst i Program.cs ersätter du det enskilda using-uttrycket med följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Lägg till nödvändiga konstanter

I **Main-metoden** lägger du sedan till de nödvändiga konstanterna för att få åtkomst till QnA Maker. Ersätt värdena med dina egna.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=11-14 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Lägga till POST-begäran för att publicera kunskapsbas

Efter de konstanter som krävs lägger du till följande kod, som gör en HTTPS-begäran för API för QnA Maker för att publicera en kunskapsbas och tar emot svaret:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=16-29&dedent=8 "Add HTTP Post request and response")]

API-anropet returnerar 204-status för en lyckad publicering utan något innehåll i svarets brödtext. 
 
## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet. Det skickar automatiskt begäran till API för QnA Maker för att publicera kunskapsbasen, och sedan skrivs svaret ut till konsolfönstret.

När din kunskapsbas har publicerats kan köra frågor mot den från slutpunkten med ett klientprogram eller en chattrobot. 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nästa steg

När kunskapsbasen publiceras behöver du [slutpunktens webbadress för att generera ett svar](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
