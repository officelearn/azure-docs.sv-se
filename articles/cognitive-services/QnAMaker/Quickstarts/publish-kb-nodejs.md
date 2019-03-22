---
title: Publicera kunskapsbas, REST, Node.js
titleSuffix: QnA Maker - Azure Cognitive Services
description: Den här Node.js-snabbstarten går igenom hur du programmatiskt publicerar din kunskapsbas (KB). Publicering skickar den senaste versionen av kunskapsbasen till ett dedikerat Azure Search-index och skapar en slutpunkt som kan anropas i ditt program eller en chattrobot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5538253459643400faf01b5999cfaf8780c05d29
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217962"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>Snabbstart: Publicera en kunskapsbas i QnA Maker med Node.js

Den här REST-baserade snabbstarten går igenom hur du programmatiskt publicerar din kunskapsbas (KB). Publicering skickar den senaste versionen av kunskapsbasen till ett dedikerat Azure Search-index och skapar en slutpunkt som kan anropas i ditt program eller en chattrobot.

Den här snabbstarten anropar API:er för QnA Maker:
* [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) (Publicera) – detta API kräver inte någon information i brödtexten för begäran.

## <a name="prerequisites"></a>Förutsättningar

* [Node.js 6+](https://nodejs.org/en/download/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på instrumentpanelen. 
* ID för QnA Maker-kunskapsbas (KB) som finns i URL:en i kbid-frågesträngsparametern enligt nedan.

    ![QnA Maker-kunskapsbas-ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Om du inte har någon kunskapsbas ännu kan du skapa en exempelkunskapsbas för den här snabbstarten: [Skapa en ny kunskapsbas](create-new-kb-nodejs.md).


> [!NOTE] 
> Kompletta lösningsfiler är tillgängliga från [**Azure-Samples/cognitive-services-qnamaker-nodejs** GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/publish-knowledge-base-short).

## <a name="create-a-knowledge-base-nodejs-file"></a>Skapa en Node.js-fil för kunskapsbas

Skapa en fil som heter `publish-knowledge-base.js`.

## <a name="add-required-dependencies"></a>Lägga till nödvändiga beroenden

Längst upp i `publish-knowledge-base.js` lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=1-3 "Add the dependencies")]

## <a name="add-required-constants"></a>Lägg till nödvändiga konstanter

När du har lagt till nödvändiga beroenden lägger du till de konstanter som krävs för åtkomst till QnA Maker. Ersätt värdena med dina egna.

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=11-14 "Add required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Lägga till POST-begäran för att publicera kunskapsbas

Efter konstanterna som krävs lägger du till följande kod, som gör en HTTPS-begäran för API:et för QnA Maker för att publicera en kunskapsbas och tar emot svaret:

[!code-nodejs[Add a POST request to publish knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=16-47 "Add a POST request to publish knowledge base")]

API-anropet returnerar 204-status för en lyckad publicering utan något innehåll i svarets brödtext. Koden lägger till innehåll för 204-svar.

För andra svar returneras svaret oförändrat.

## <a name="run-the-program"></a>Köra programmet

Skapa och kör programmet. Det skickar automatiskt begäran till API för QnA Maker för att publicera kunskapsbasen, och sedan skrivs svaret ut till konsolfönstret.

När din kunskapsbas har publicerats kan köra frågor mot den från slutpunkten med ett klientprogram eller en chattrobot. 

```bash
node publish-knowledge-base.js
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nästa steg

När kunskapsbasen publiceras behöver du [slutpunktens webbadress för att generera ett svar](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
