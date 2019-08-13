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
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 9c5398ff7cb31698db3d4a798b6a082f9e74b99b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955142"
---
# <a name="collaborate-on-your-knowledge-base"></a>Samar beta med din kunskaps bas

QnA Maker gör det möjligt för flera personer att samar beta i en kunskaps bas. Den här funktionen tillhandahålls med Azure [-rollbaserade Access Control](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 

Utför följande steg för att dela din QnA Maker-tjänst med någon:

1. Logga in på Azure Portal och gå till din QnA Maker-resurs.

    ![QnA Maker resurslistan](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Gå till fliken **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Välj **Lägg till**.

    ![QnA Maker IAM-tillägg](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Välj **ägare** eller deltagar roll. Du kan inte bevilja skrivskyddad åtkomst via rollbaserade Access Control. Rollen ägare och deltagare har Läs-och Skriv behörighet för att QnA Maker tjänsten.

    ![QnA Maker IAM Lägg till roll](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Ange e-postadressen som du vill dela med och tryck på Spara.

    ![QnA Maker IAM Lägg till e-post](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Nu när personen som du delar din QnA Maker-tjänst med loggar in på [QNA Maker Portal](https://qnamaker.ai) kan de Se alla kunskaps baser i den tjänsten.

Kom ihåg att du inte kan dela en viss kunskaps bas i en QnA Maker-tjänst. Om du vill ha mer detaljerad åtkomst kontroll bör du överväga att distribuera dina kunskaps baser över olika QnA Maker tjänster.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa en kunskaps bas](./test-knowledge-base.md)
