---
title: 'Snabbstart: Använda Postman för att få svar från kunskapsbas – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Denna snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas med Postman.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270900"
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

[![Publicera resultat](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Utför följande steg för att generera ett svar med Postman:

1. Öppna Postman. 
1. Välj byggblock för att skapa en grundläggande begäran.
1. Ange **frågenamn** som `Generate QnA Maker answer`och **samling** som `Generate QnA Maker answers`.
1. I arbetsytan, välj HTTP-metod för **POST**.
1. Sammanfoga HOST-värden och Post-värdet för att skapa den fullständiga URL:en. 

    [![I Postman, ändra metoden till Post och den fullständiga URL:en](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Välj fliken **rubriker** under URL:en och välj sedan **Massredigera**. 
1. Kopiera rubrikerna till textområdet.

    [![Ställ in sidhuvuden i Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Välj fliken **brödtext**.
1. Välj formatet **raw** och ange den JSON som representerar frågan.

    [![Konfigurera JSON-värdet för brödtexten i Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Välj knappen **Skicka**.
1. Svaret innehåller svar tillsammans med annan information som kan vara viktig för klientprogrammet. 

    [![Konfigurera JSON-värdet för brödtexten i Postman](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>Använda mellanlagringsslutpunkten med cURL

Om du vill få svar från mellanlagringsslutpunkten ska du använda den booleska frågeparametern `isTest` med värdet för `true`.

`isTest=true`

## <a name="next-steps"></a>Nästa steg

Publiceringssidan tillhandahåller även information för att [generera ett svar](get-answer-from-kb-using-curl.md) med cURL. 

> [!div class="nextstepaction"]
> [Använda metadata när du genererar ett svar](../How-to/metadata-generateanswer-usage.md)