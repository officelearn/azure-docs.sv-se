---
title: Publicera en kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: Publicera din kunskapsbas är det sista steget i att göra din kunskapsbas tillgängliga som en slutpunkt för besvara fråga. När du publicerar en kunskapsbas flyttar frågor och svar om innehållet i kunskapsbasen från test-index till ett index för produktion i Azure search.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 0a43c54db7af61b72a8e8e6188fd13ad1159271e
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45540947"
---
# <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera din kunskapsbas är det sista steget i att göra din kunskapsbas tillgängliga som en slutpunkt för besvara fråga. 

När du publicerar en kunskapsbas flyttar frågor och svar om innehållet i kunskapsbasen från test-index till ett index för produktion i Azure search.

![Publicera prod test index](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

1. När du har gjort ändringar i din KB, Välj **publicera** i det övre navigeringsfältet. Du kan publicera upp till det angivna antalet kunskapsbaser för Azure Search. 

    ![Publicera kunskapsbas](../media/qnamaker-how-to-publish-kb/publish.png)

2. Välj **publicera** igen för att se vilka slutpunkt som kan användas i program / robotar koden.

    ![Publicera kunskapsbas](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Få analysfunktioner på din kunskapsbas](./get-analytics-knowledge-base.md)
