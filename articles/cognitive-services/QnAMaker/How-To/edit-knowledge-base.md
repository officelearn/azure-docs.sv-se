---
title: Redigera en kunskaps bas – QnA Maker
description: Med QnA Maker kan du hantera innehållet i kunskaps basen genom att tillhandahålla en användarvänlig redigerings upplevelse.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756737"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Redigera QnA-par i kunskaps basen

Med QnA Maker kan du hantera innehållet i kunskaps basen genom att tillhandahålla en användarvänlig redigerings upplevelse.

QnA-par läggs till från en data källa, till exempel en fil eller URL, eller läggs till som en redaktionell källa. En redaktionell källa visar att QnA-paret lades till i QnA-portalen manuellt. Alla QnA-par är tillgängliga för redigering.

## <a name="add-an-editorial-qna-pair"></a>Lägg till ett redigerings QnA-par
1. Logga in på [QNA-portalen](https://www.qnamaker.ai/)och välj sedan den kunskaps bas som QNA-paret ska läggas till i.
1. På sidan **Redigera** i kunskaps basen väljer du **Lägg till QNA-par** för att lägga till ett nytt QNA-par.

1. I den nya QnA pair-raden lägger du till obligatoriska **frågor** och **svars** fält. De andra fälten är valfria. Alla fält kan ändras när som helst.

1. Du kan också lägga till **alternativa formuleringen**. Alternativa formuleringen är en form av frågan som är mycket annorlunda från den ursprungliga frågan men som bör ge samma svar.

    När din kunskaps bas publiceras och du har aktiverat [aktiv inlärning](use-active-learning.md) , samlar QNA Maker in alternativa formuleringen-alternativ som du kan acceptera. Dessa val väljs för att öka förutsägelse noggrannheten.

1. Du kan också lägga till **metadata**. Om du vill visa metadata väljer du **visnings alternativ** på snabb menyn. Metadata innehåller filter för de svar som klient programmet, till exempel en chatt-robot, tillhandahåller.

1. Du kan också lägga till **uppföljnings**anvisningarna. Följ anvisningarna innehåller ytterligare konversations Sök vägar till klient programmet för att presentera för användaren.

1. Välj **Spara och träna** för att se förutsägelser, inklusive det nya QNA-paret.

## <a name="edit-a-qna-pair"></a>Redigera ett QnA-par

Alla fält i ett QnA-par kan redige ras, oavsett den ursprungliga data källan. Vissa fält kanske inte visas på grund av dina aktuella inställningar för **visnings alternativ** , som finns i kontext verktygsfältet.

## <a name="delete-a-qna-pair"></a>Ta bort ett QnA-par

Om du vill ta bort en QnA klickar du på ikonen **ta bort** längst till höger på raden QNA. Detta är en permanent åtgärd. Det går inte att återställa. Överväg att exportera dina KB från sidan **publicera** innan du tar bort uppsättningar.

![Ta bort QnA-par](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Hitta QnA par-ID

Om du behöver hitta QnA par-ID: t kan du hitta det på två platser:

* Hovra över borttagnings ikonen på QnA-värdeparet som du är intresse rad av. Hov rings texten innehåller QnA-parets ID.
* Exportera kunskaps basen. Varje QnA-par i kunskaps basen innehåller QnA-parets ID.

## <a name="add-alternate-questions"></a>Lägg till alternativa frågor

Lägg till alternativa frågor i ett befintligt QnA-par för att förbättra sannolikheten för en matchning till en användar fråga.

![Lägg till alternativa frågor](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Länkar QnA-par

Länkar av QnA-par finns med [uppföljnings instruktioner](multiturn-conversation.md). Det här är en logisk anslutning mellan QnA-par som hanteras på kunskaps bas nivån. Du kan redigera uppföljnings anvisningarna i QnA Maker portalen.

Du kan inte länka QnA-par i svarets metadata.

## <a name="add-metadata"></a>Lägg till metadata

Lägg till metadata uppsättningar genom att först välja **visnings alternativ**och sedan **Visa metadata**. Kolumnen metadata visas. Välj sedan **+** tecknet för att lägga till en uppsättning med metadata. Den här uppsättningen består av en nyckel och ett värde.

## <a name="save-changes-to-the-qna-pairs"></a>Spara ändringar i QnA-par

Välj regelbundet **Spara och träna** efter att du har redigerat ändringarna för att undvika att förlora ändringar.

![Lägg till metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samarbeta i en kunskapsbas](./collaborate-knowledge-base.md)

* [Hantera Azure-resurser som används av QnA Maker](set-up-qnamaker-service-azure.md)