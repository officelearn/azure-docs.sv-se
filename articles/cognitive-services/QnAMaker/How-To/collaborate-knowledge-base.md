---
title: Samarbeta med dina knowledge base - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Hur du samarbeta med frågor och svar om Maker kunskapsbasen
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e18d656236276595fc5186a6656349bf28974ead
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353952"
---
# <a name="collaborate-on-your-knowledge-base"></a>Samarbeta kring kunskapsbasen

Frågor och svar om Maker tillåter att flera personer att samarbeta på en knowledge base. Den här funktionen tillhandahålls i Azure [rollbaserad åtkomstkontroll](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure). 

Utför följande steg om du vill dela dina frågor och svar om Maker service med någon:

1. Logga in på Azure portal och gå till frågor och svar om Maker-resurs.

    ![Frågor och svar om Maker resurslistan](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Gå till den **Access Control (IAM)** fliken.

    ![Frågor och svar om Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Välj **Lägg till**.

    ![Frågor och svar om Maker IAM Lägg till](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Välj den **ägare** eller **deltagare** roll.

    ![Frågor och svar om Maker IAM lägga till rollen](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Ange e-postadress som du vill dela med och klicka på Spara.

    ![Frågor och svar om Maker IAM lägga till e-post](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Nu när personen som du delade frågor och svar om Maker tjänsten med, loggar in på den [frågor och svar om Maker portal](https://qnamaker.ai) de kan se knowledge grunderna i tjänsten.

Kom ihåg att du inte kan dela en viss kunskapsbas i frågor och svar om Maker-tjänsten. Överväg att distribuera knowledge-databaser på olika frågor och svar om Maker tjänster om du vill ha mer noggrann åtkomstkontroll.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa en kunskapsbas](./test-knowledge-base.md)
