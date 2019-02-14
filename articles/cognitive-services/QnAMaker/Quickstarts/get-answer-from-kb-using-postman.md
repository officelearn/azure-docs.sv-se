---
title: 'Snabbstart: Använda Postman för att få svar från kunskapsbas – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Denna snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas med Postman.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/03/2019
ms.author: diberry
ms.openlocfilehash: a3d2d195614f0eab1b382e9a0967d921459ff553
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884111"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Snabbstart: Få svar från en kunskapsbas med Postman

Denna Postmanbaserade snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Senaste [**Postman**](https://www.getpostman.com/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md) och en [kunskapsbas med frågor och svar](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publicera för att få en slutpunkt

När du är redo att generera ett svar på en fråga från din kunskapsbas kan du [publicera](../How-to/publish-knowledge-base.md) kunskapsbasen.

## <a name="use-production-endpoint-with-postman"></a>Använd produktionsslutpunkten med Postman

När din kunskapsbas publiceras visar sidan **publicera** inställningar för HTTP-begäran för att generera ett svar. Standardvyn visar de inställningar som krävs för att generera ett svar från [Postman](https://www.getpostman.com).

De gula siffrorna på följande bild visar vilka namn/värdepar som ska användas i följande steg.

[![Publicera resultat](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Utför följande steg för att generera ett svar med Postman:

1. Öppna Postman. Om du uppmanas att välja ett byggblock, välj byggblocket **Grundläggande begäran**. Ange **frågenamn** som `Generate QnA Maker answer`och **samling** som `Generate QnA Maker answers`. Om du inte vill spara till en samling, väljer du knappen **Avbryt**.
1. I arbetsytan, välj HTTP-metod för **POST**.

    [![Ange POST-metoden i Postman](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Sammanfoga HOST-värden (nr 2 från bild) och Post-värdet (nr 1 från bild) för att skapa den fullständiga URL:en. Ett komplett exempel-URL ser ut så här: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![Ställ in den fullständiga URL:en i Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Välj fliken **rubriker** under URL:en och välj sedan **Massredigera**. 

1. Kopiera rubriker (nr 3 och 4 från bilden) till textområdet.

    [![Ställ in sidhuvuden i Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Välj fliken **brödtext**.
1. Välj formatet **raw** och ange den JSON (nr 5 från bilden) som representerar frågan.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![Konfigurera JSON-värdet för brödtexten i Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Välj knappen **Skicka**.
1. Svaret innehåller svar tillsammans med annan information som kan vara viktig för klientprogrammet. 

    [![Konfigurera JSON-värdet för brödtexten i Postman](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Använda mellanlagringsslutpunkt

Om du vill få svar från mellanlagringsslutpunkten ska du bifoga URL:en med den booleska frågeparametern `isTest` med värdet för `true`.

`?isTest=true`

## <a name="next-steps"></a>Nästa steg

Publiceringssidan tillhandahåller även information för att [generera ett svar](get-answer-from-kb-using-curl.md) med cURL. 

> [!div class="nextstepaction"]
> [Använda metadata när du genererar ett svar](../How-to/metadata-generateanswer-usage.md)
