---
title: Hantera QnA Maker app-QnA Maker
description: QnA Maker gör det möjligt för flera personer att samar beta i en kunskaps bas. QnA Maker erbjuder en möjlighet att förbättra din kunskaps Bass kvalitet med aktiv inlärning. En kan granska, godkänna eller avvisa och lägga till utan att ta bort eller ändra befintliga frågor.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 77290d271709db36f9c62e165b0b4070783b3ec6
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128501"
---
# <a name="manage-qna-maker-app"></a>Hantera QnA Maker app

Med QnA Maker kan du samar beta med olika författare och innehålls redigerare genom att erbjuda en möjlighet att begränsa åtkomsten till medarbetare baserat på samarbets rollen.
Läs mer om [begrepp för att QNA Maker Collaborative-autentisering](../Concepts/role-based-access-control.md).

Du kan också förbättra din kunskaps bas kvalitet genom att föreslå alternativa frågor via [aktiv inlärning](../Concepts/active-learning-suggestions.md). Användar-och insändningar beaktas och visas som förslag i listan över alternativa frågor. Du har möjlighet att antingen lägga till dessa förslag som alternativa frågor eller avvisa dem.

Kunskaps basen ändras inte automatiskt. För att alla ändringar ska börja gälla måste du godkänna förslagen. Dessa förslag lägger till frågor, men ändrar inte eller tar inte bort befintliga frågor.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Lägg till Azure-rollbaserad åtkomst kontroll (Azure RBAC)

QnA Maker låter flera personer samar beta i alla kunskaps banker i samma QnA Maker-resurs. Den här funktionen tillhandahålls med [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Åtkomst på QnA Maker resurs nivå

Du kan inte dela en viss kunskaps bas i en QnA Maker-tjänst. Om du vill ha mer detaljerad åtkomst kontroll bör du överväga att distribuera dina kunskaps baser över olika QnA Maker resurser och sedan lägga till roller till varje resurs.

## <a name="add-a-role-to-a-resource"></a>Lägga till en roll i en resurs

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

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Uppgradera runtime-versionen för att använda aktiv inlärning

Active Learning stöds i runtime-version 4.4.0 och senare. Om din kunskaps bas har skapats på en tidigare version kan du [Uppgradera körningen](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) för att använda den här funktionen.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Aktivera aktiv inlärning för alternativa frågor

Aktiv inlärning är inaktiverat som standard. Aktivera det om du vill se föreslagna frågor. När du har aktiverat aktiv inlärning måste du skicka information från klient appen till QnA Maker. Mer information finns i [arkitektoniskt flöde för att använda GenerateAnswer och träna API: er från en bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Välj **publicera** för att publicera kunskaps basen. Aktiva inlärnings frågor samlas endast in från GenerateAnswer API förutsägelse-slut punkten. Frågorna i test fönstret i QnA Makers portalen påverkar inte aktiv inlärning.

1. Om du vill aktivera aktiv inlärning i QnA Maker portal går du till det övre högra hörnet och väljer ditt **namn**, gå till [**tjänst inställningar**](https://www.qnamaker.ai/UserSettings).

    ![Aktivera alternativ för den föreslagna frågan i Active Learning på sidan tjänst inställningar. Välj ditt användar namn på menyn längst upp till höger och välj sedan tjänst inställningar.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Hitta QnA Maker tjänsten och växla sedan **aktiv inlärning**.

    > [!div class="mx-imgBorder"]
    > [![På sidan tjänst inställningar växlar du till funktionen aktiv inlärning. Om du inte kan växla funktionen kan du behöva uppgradera tjänsten.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > Den exakta versionen på föregående bild visas som ett exempel. Din version kan vara annorlunda.
    När **aktiv inlärning** har Aktiver ATS föreslår kunskaps basen nya frågor med jämna mellanrum baserat på frågor som skickats av användaren. Du kan inaktivera **aktiv inlärning** genom att växla inställningen igen.

## <a name="review-suggested-alternate-questions"></a>Granska föreslagna alternativa frågor

[Granska alternativa föreslagna frågor](improve-knowledge-base.md) på sidan **Redigera** i varje kunskaps bas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskaps bas](./manage-knowledge-bases.md)