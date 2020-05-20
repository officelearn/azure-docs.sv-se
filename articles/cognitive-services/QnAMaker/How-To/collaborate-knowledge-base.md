---
title: Samar beta på kunskaps bas – QnA Maker
description: QnA Maker gör det möjligt för flera personer att samar beta i en kunskaps bas. Den här funktionen tillhandahålls med Azure-rollbaserade Access Control.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650783"
---
# <a name="collaboration-with-authors-and-editors"></a>Samarbete med författare och redigerare

Samarbetet tillhandahålls på QnA Maker resurs nivå så att du kan begränsa åtkomsten till medarbetare baserat på samarbets rollens roll. Läs mer om [begrepp](../Concepts/role-based-access-control.md)för att QNA Maker Collaborative-autentisering.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Lägg till rollbaserad åtkomst (RBAC) till din QnA Maker-resurs

QnA Maker låter flera personer samar beta i alla kunskaps banker i samma QnA Maker-resurs. Den här funktionen tillhandahålls med Azure [-rollbaserade Access Control](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Åtkomst på QnA Maker resurs nivå

Du kan inte dela en viss kunskaps bas i en QnA Maker-tjänst. Om du vill ha mer detaljerad åtkomst kontroll bör du överväga att distribuera dina kunskaps baser över olika QnA Maker resurser och sedan lägga till roller till varje resurs.

## <a name="add-role-to-resource"></a>Lägg till roll till resurs

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Lägg till ett användar konto i QnA Maker resursen

Följande steg använder rollen medarbetare men någon av [rollerna](../reference-role-based-access-control.md) kan läggas till med hjälp av de här stegen

1. Logga in på [Azure](https://portal.azure.com/) Portal och gå till din QNA Maker-resurs.

    ![QnA Maker resurs lista](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Gå till fliken **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Välj **Lägg till**.

    ![QnA Maker IAM-tillägg](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Välj en roll i följande lista:

    |Roll|
    |--|
    |Ägare|
    |Deltagare|
    |QnA Maker läsare|
    |QnA Maker redigerare|
    |Cognitive Services användare|

    ![QnA Maker IAM Lägg till roll](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Ange användarens e-postadress och tryck på **Spara**.

    ![QnA Maker IAM Lägg till e-post](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

När personen som du delar din QnA Maker-tjänst med loggar i [QNA Maker-portalen](https://qnamaker.ai)kan de Se alla kunskaps banker i den tjänsten baserat på deras roll.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa en kunskapsbas](./test-knowledge-base.md)

Lär dig mer om samarbete:
* Rollbaserad åtkomst kontroll i [Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker rollbaserad åtkomst kontroll [koncept](../Concepts/role-based-access-control.md)
