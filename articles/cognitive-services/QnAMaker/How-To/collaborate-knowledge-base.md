---
title: Samar beta på kunskaps bas – QnA Maker
description: QnA Maker gör det möjligt för flera personer att samar beta i en kunskaps bas. Den här funktionen ingår i rollbaserad åtkomst kontroll i Azure (Azure RBAC).
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 8adc02d4b397c2b0bdaf5d3998cfbe229c896d41
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776858"
---
# <a name="collaboration-with-authors-and-editors"></a>Samarbete med författare och redigerare

Samarbetet tillhandahålls på QnA Maker resurs nivå så att du kan begränsa åtkomsten till medarbetare baserat på samarbets rollens roll. Läs mer om [begrepp](../Concepts/role-based-access-control.md)för att QNA Maker Collaborative-autentisering.

## <a name="add-azure-role-based-access-control-azure-rbac-to-your-qna-maker-resource"></a>Lägg till Azure-rollbaserad åtkomst kontroll (Azure RBAC) till din QnA Maker-resurs

QnA Maker låter flera personer samar beta i alla kunskaps banker i samma QnA Maker-resurs. Den här funktionen ingår i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Åtkomst på QnA Maker resurs nivå

Du kan inte dela en viss kunskaps bas i en QnA Maker-tjänst. Om du vill ha mer detaljerad åtkomst kontroll bör du överväga att distribuera dina kunskaps baser över olika QnA Maker resurser och sedan lägga till roller till varje resurs.

## <a name="add-role-to-resource"></a>Lägg till roll till resurs

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Lägg till ett användar konto i QnA Maker resursen

Följande steg använder rollen medarbetare men någon av [rollerna](../reference-role-based-access-control.md) kan läggas till med hjälp av de här stegen

1. Logga in på [Azure](https://portal.azure.com/) Portal och gå till din QNA Maker-resurs.

    ![QnA Maker resurs lista](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Gå till fliken **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Välj **Lägg till**.

    ![QnA Maker IAM-tillägg](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Välj en roll i följande lista:

    |Roll|
    |--|
    |Ägare|
    |Deltagare|
    |Cognitive Services QnA Maker läsare|
    |Cognitive Services QnA Maker redigeraren|
    |Cognitive Services användare|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM-Lägg till roll.":::

1. Ange användarens e-postadress och tryck på **Spara**.

    ![QnA Maker IAM Lägg till e-post](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Visa QnA Maker kunskaps baser

När personen som du delar din QnA Maker-tjänst med loggar i [QNA Maker-portalen](https://qnamaker.ai)kan de Se alla kunskaps banker i den tjänsten baserat på deras roll.

När de väljer en kunskaps bas visas den aktuella rollen på den QnA Maker resursen bredvid kunskaps bas namnet.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="QnA Maker IAM-Lägg till roll.":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa en kunskapsbas](./test-knowledge-base.md)

Lär dig mer om samarbete:
* Rollbaserad åtkomst kontroll i [Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker rollbaserad åtkomst kontroll [koncept](../Concepts/role-based-access-control.md)
