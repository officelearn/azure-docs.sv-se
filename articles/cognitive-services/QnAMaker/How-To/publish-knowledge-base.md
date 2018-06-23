---
title: Så här publicerar du en knowledge base - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Så här publicerar du en kunskapsbas
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353955"
---
# <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskapsbasen är det sista steget i att göra kunskapsbasen tillgänglig som en fråga genom att besvara slutpunkt. 

När du publicerar en knowledge base, frågor och svar om innehållet i kunskapsbasen flyttas från test-index till ett produktion index i Azure search.

![Publicera produktprenumeration test index](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

1. När du har gjort ändringar i din KB, Välj **publicera** i det övre navigeringsfältet. Du kan publicera upp till det angivna antalet knowledge baser för Azure Search. 

    ![Publicera kunskapsbas](../media/qnamaker-how-to-publish-kb/publish.png)

2. Välj **publicera** igen för att se endpoint-information som kan användas i programmet eller bot koden.

    ![Publicera kunskapsbas](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Få analyser på kunskapsbasen](./get-analytics-knowledge-base.md)
