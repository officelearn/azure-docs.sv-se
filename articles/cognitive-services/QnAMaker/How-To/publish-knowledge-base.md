---
title: Publicera en kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: Publicera din kunskapsbas är det sista steget i att göra din kunskapsbas tillgängliga som en slutpunkt för besvara fråga. När du publicerar en kunskapsbas flyttar frågor och svar om innehållet i kunskapsbasen från test-index till ett index för produktion i Azure search.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9c3d1009742f9ca0df6dc2f2d1a2d1904397275a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087856"
---
# <a name="publish-a-knowledge-base-in-qna-maker"></a>Publicera en kunskapsbas i QnA Maker

Publicera din kunskapsbas är det sista steget i att göra din kunskapsbas tillgängliga som en slutpunkt för besvara fråga. 

När du publicerar en kunskapsbas flyttar frågor och svar om innehållet i kunskapsbasen från test-index till ett index för produktion i Azure search.

![Publicera prod test index](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

1. När du har gjort ändringar i din KB, Välj **publicera** i det övre navigeringsfältet. Du kan publicera upp till det angivna antalet kunskapsbaser för Azure Search. 

    ![Publicera kunskapsbas](../media/qnamaker-how-to-publish-kb/publish.png)

2. Välj **publicera** igen för att se vilka slutpunkt som kan användas i program / robotar koden.

    ![Publicerades kunskapsbas](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Få analysfunktioner på din kunskapsbas](./get-analytics-knowledge-base.md)
