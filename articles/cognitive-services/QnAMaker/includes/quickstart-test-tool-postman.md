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
ms.openlocfilehash: 0b442c8cccf8ee9ed5194a7d3dfbfb4d7aa055a3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424421"
---
Denna Postmanbaserade snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas.

## <a name="prerequisites"></a>Förutsättningar

* Senaste [**Postman**](https://www.getpostman.com/).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md) och en [kunskapsbas med frågor och svar](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publicera för att få en slutpunkt

När du är redo att generera ett svar på en fråga från din kunskapsbas kan du [publicera](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) kunskapsbasen.

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

Om du vill få ett svar från mellanlagrings slut punkten lägger du till URL: en med egenskapen `isTest` Body.
