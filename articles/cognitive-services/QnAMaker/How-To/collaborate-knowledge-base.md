---
title: Samar beta på kunskaps bas – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker gör det möjligt för flera personer att samar beta i en kunskaps bas. Den här funktionen tillhandahålls med Azure-rollbaserade Access Control.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: d9c91d54fb357807682cd57f46b04454e4e2cfec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876652"
---
# <a name="collaborate-on-your-knowledge-base"></a>Samar beta med din kunskaps bas

QnA Maker gör det möjligt för flera personer att samar beta i en kunskaps bas. Den här funktionen tillhandahålls med Azure [-rollbaserade Access Control](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 

Utför följande steg för att dela din QnA Maker-tjänst med någon:

1. Logga in på Azure Portal och gå till din QnA Maker-resurs.

    ![QnA Maker resurslistan](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Gå till fliken **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Välj **Lägg till**.

    ![QnA Maker IAM-tillägg](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Välj **ägare** eller deltagar roll. Du kan inte bevilja skrivskyddad åtkomst via rollbaserade Access Control. Ägare och deltagar roller har Läs-och Skriv behörighet till tjänsten QnA Maker.

    ![QnA Maker IAM Lägg till roll](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Ange användarens e-postadress och tryck på **Spara**.

    ![QnA Maker IAM Lägg till e-post](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

När du har delat din QnA Maker-tjänst med loggar du in på [QNA Maker Portal](https://qnamaker.ai) kan de Se alla kunskaps baser i den tjänsten.

Kom ihåg att du inte kan dela en viss kunskaps bas i en QnA Maker-tjänst. Om du vill ha mer detaljerad åtkomst kontroll bör du överväga att distribuera dina kunskaps baser över olika QnA Maker tjänster.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa en kunskaps bas](./test-knowledge-base.md)
