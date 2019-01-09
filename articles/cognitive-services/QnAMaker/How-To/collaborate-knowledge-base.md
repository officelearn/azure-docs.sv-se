---
title: Samarbeta med knowledge base - Qna Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker kan flera personer att samarbeta med en kunskapsbas. Den här funktionen tillhandahålls med rollbaserad åtkomstkontroll.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/08/2018
ms.author: tulasim
ms.openlocfilehash: ec26e7ab535084fa5d8f52c11c671212eeab0f13
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119847"
---
# <a name="collaborate-on-your-knowledge-base"></a>Samarbeta med din kunskapsbas

QnA Maker kan flera personer att samarbeta med en kunskapsbas. Den här funktionen tillhandahålls med Azure [Role-Based Access Control](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 

Utför följande steg om du vill dela din QnA Maker-tjänsten med någon:

1. Logga in på Azure Portal och gå till QnA Maker-resurs.

    ![QnA Maker resurslistan](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Gå till den **åtkomstkontroll (IAM)** fliken.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Välj **Lägg till**.

    ![Lägg till QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Välj den **ägare** eller **deltagare** roll. Du kan inte bevilja skrivskyddad åtkomst via rollbaserad åtkomstkontroll. Ägare och deltagare rollen har skrivskyddad åtkomst till QnA Maker-tjänsten.

    ![QnA Maker IAM Lägg till roll](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Ange e-postadress som du vill dela med och trycker på Spara.

    ![QnA Maker IAM lägga till e-post](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Nu när personen som du delat QnA Maker-tjänsten med, loggar in på den [QnA Maker portal](https://qnamaker.ai) de kan se alla kunskapsbaser i tjänsten.

Kom ihåg att du inte kan dela en viss kunskapsbas i QnA Maker-tjänsten. Om du vill mer noggrann åtkomstkontroll, bör överväga att dela din kunskapsbaser över olika QnA Maker-tjänster.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa en kunskapsbas](./test-knowledge-base.md)
