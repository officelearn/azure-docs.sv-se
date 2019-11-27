---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0677a361e853f778894b6a62a054636e3276b364
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424414"
---
Den här Spiralbaserade snabb starten vägleder dig genom att få svar från din kunskaps bas.

## <a name="prerequisites"></a>Förutsättningar

* Senaste [**vändning**](https://curl.haxx.se/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md) och en [kunskapsbas med frågor och svar](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publicera för att få en slutpunkt

När du är redo att generera ett svar på en fråga från din kunskapsbas kan du [publicera](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) kunskapsbasen.

## <a name="use-production-endpoint-with-curl"></a>Använd produktions slut punkt med sväng

När din kunskapsbas publiceras visar sidan **publicera** inställningar för HTTP-begäran för att generera ett svar. På fliken **vändning** visas de inställningar som krävs för att generera ett svar från kommando rads verktyget, [sväng](https://www.getpostman.com).

[![Publicera resultat](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Utför följande steg för att generera ett svar med en sväng:

1. Kopiera texten på fliken vändning. 
1. Öppna en kommando rad eller Terminal och klistra in texten.
1. Redigera frågan så att den är relevant för din kunskaps bas. Var noga med att inte ta bort den som innehåller JSON som omger frågan.
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
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
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

Om du vill få ett svar från mellanlagringsplatsen använder du egenskapen `isTest` text.

```json
isTest:true
```


