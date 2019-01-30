---
title: 'Snabbstart: Använda cURL för att få svar från knowledge base - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Den här snabbstarten visar hur du hämtar ett svar från din kunskapsbas med cURL.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 6eccf4014eb663d0a3275d70c4e997f9ed324762
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211992"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Snabbstart: Få svar från knowledge base med cURL

Snabbstarten cURL-baserade beskriver hur du hämtar ett svar från din kunskapsbas.

## <a name="prerequisites"></a>Förutsättningar

* Senaste [ **cURL**](https://curl.haxx.se/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md) och en [kunskapsbas med frågor och svar](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publicera för att få en slutpunkt

När du är redo att generera ett svar på en fråga från din kunskapsbas kan du [publicera](../How-to/publish-knowledge-base.md) kunskapsbasen.

## <a name="use-production-endpoint-with-curl"></a>Produktion slutpunkten med cURL

När din kunskapsbas publiceras visar sidan **publicera** inställningar för HTTP-begäran för att generera ett svar. Den **CURL** fliken visas de inställningar som krävs för att generera ett svar på kommandoraden [CURL](https://www.getpostman.com).

[![Publicera resultat](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Utför följande steg för att generera ett svar med CURL:

1. Kopiera texten på fliken CURL. 
1. Öppna en kommandorad eller terminal och klistra in texten.
1. Redigera din fråga och vara relevanta för din kunskapsbas. Tänk på att inte ta bort som innehåller JSON kring problemet.
1. Ange kommandot. 
1. Svaret innehåller relevant information om svaret. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Använda mellanlagringsslutpunkten med cURL

Om du vill få svar från mellanlagringsslutpunkten ska du använda den booleska frågeparametern `isTest` med värdet för `true`.

`isTest=true`

## <a name="next-steps"></a>Nästa steg

Publiceringssidan tillhandahåller även information till [generera ett svar](get-answer-from-kb-using-postman.md) med Postman. 

> [!div class="nextstepaction"]
> [Använda metadata när du genererar ett svar](../How-to/metadata-generateanswer-usage.md)
