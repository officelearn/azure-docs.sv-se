---
title: Publicera en kunskapsbas
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Publicera din kunskapsbas med QnA Maker API-tjänsten är det sista steget att tillgängliggöra din kunskapsbas som besvarar frågan-slutpunkt. När du publicerar en kunskapsbas flyttar frågor och svar om innehållet i kunskapsbasen från test-index till ett index för produktion i Azure search.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091961"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Publicera en kunskapsbas med QnA Maker API service-portalen

Publicera din kunskapsbas med QnA Maker API-tjänsten är det sista steget att tillgängliggöra din kunskapsbas som besvarar frågan-slutpunkt. 

När du publicerar en kunskapsbas flyttar frågor och svar innehållet i kunskapsbasen från test-index till ett index för produktion i Azure search.

![Publicera prod test index](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

1. När du har gjort ändringar i din KB, Välj **publicera** i det övre navigeringsfältet. Du kan publicera upp till det angivna antalet kunskapsbaser för Azure Search. 

    ![Publicera kunskapsbas](../media/qnamaker-how-to-publish-kb/publish.png)

2. Välj **publicera** igen för att se vilka slutpunkt som kan användas i program / robotar koden.

    ![Publicerades kunskapsbas](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med kunskapsbasen kan du ta bort den i QnA Maker-portalen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Få analysfunktioner på din kunskapsbas](./get-analytics-knowledge-base.md)
