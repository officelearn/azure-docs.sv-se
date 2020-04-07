---
title: Redigera en kunskapsbas - QnA Maker
description: Med QnA Maker kan du hantera innehållet i din kunskapsbas genom att tillhandahålla en lättanvänd redigeringsupplevelse.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756737"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Redigera QnA-par i din kunskapsbas

Med QnA Maker kan du hantera innehållet i din kunskapsbas genom att tillhandahålla en lättanvänd redigeringsupplevelse.

QnA-par läggs till från en datakälla, till exempel en fil eller URL, eller läggs till som en redaktionell källa. En redaktionell källa anger att QnA-paret lades till i QnA-portalen manuellt. Alla QnA-par är tillgängliga för redigering.

## <a name="add-an-editorial-qna-pair"></a>Lägga till ett redaktionellt QnA-par
1. Logga in på [QnA-portalen](https://www.qnamaker.ai/)och välj sedan kunskapsbasen att lägga till QnA-paret i.
1. På sidan **REDIGERA** i kunskapsbasen väljer du **Lägg till QnA-par** för att lägga till ett nytt QnA-par.

1. Lägg till de obligatoriska fråge- och svarsfälten **i** den nya QnA-parraden. **Question** De andra fälten är valfria. Alla fält kan ändras när som helst.

1. Du kan också lägga till **alternativ frasering**. Alternativ frasering är någon form av frågan som skiljer sig avsevärt från den ursprungliga frågan, men bör ge samma svar.

    När din kunskapsbas publiceras och du har [aktiverat aktiv inlärning](use-active-learning.md) samlar QnA Maker in alternativa fraseringsalternativ som du kan acceptera. Dessa alternativ väljs för att öka förutsägelsenoggrannheten.

1. Du kan också lägga till **metadata**. Om du vill visa metadata väljer du **Visa alternativ** på snabbmenyn. Metadata innehåller filter till de svar som klientprogrammet, till exempel en chattrobot, tillhandahåller.

1. Du kan också lägga till **uppföljningsan följande.** Uppföljningsansanningar ger ytterligare konversationssökvägar till klientprogrammet som ska visas för användaren.

1. Välj **Spara och träna** om du vill se förutsägelser, inklusive det nya QnA-paret.

## <a name="edit-a-qna-pair"></a>Redigera ett QnA-par

Alla fält i alla QnA-par kan redigeras, oavsett den ursprungliga datakällan. Vissa fält kanske inte visas på grund av de aktuella inställningarna för **visningsalternativ** som finns i kontextverktygsfältet.

## <a name="delete-a-qna-pair"></a>Ta bort ett QnA-par

Om du vill ta bort en QnA klickar du på **borttagningsikonen** längst till höger på QnA-raden. Det här är en permanent operation. Det kan inte göras ogjort. Överväg att exportera KB från sidan **Publicera** innan du tar bort uppsättningar.

![Ta bort QnA-par](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Hitta QnA-par-ID:t

Om du behöver hitta QnA-par-ID:t kan du hitta det på två ställen:

* Hovra på ikonen för borttagning på qnA-parraden som du är intresserad av. Hovringstexten innehåller QnA-par-ID: t.
* Exportera kunskapsbasen. Varje QnA-par i kunskapsbasen innehåller QnA-par-ID.

## <a name="add-alternate-questions"></a>Lägg till alternativa frågor

Lägg till alternativa frågor i ett befintligt QnA-par för att förbättra sannolikheten för en matchning till en användarfråga.

![Lägg till alternativa frågor](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Länka QnA-par

Länka QnA par är försedd med [uppföljningsansningar](multiturn-conversation.md). Detta är en logisk koppling mellan QnA-par, som hanteras på kunskapsbasnivå. Du kan redigera uppföljningsansanningar i QnA Maker-portalen.

Du kan inte länka QnA-par i svarets metadata.

## <a name="add-metadata"></a>Lägga till metadata

Lägg till metadatauppsättningar genom att först välja **Visa alternativ**och välj sedan **Visa metadata**. Då visas kolumnen metadata. Välj sedan **+** tecknet för att lägga till en metadatauppsättning. Den här uppsättningen består av en nyckel och ett värde.

## <a name="save-changes-to-the-qna-pairs"></a>Spara ändringar i QnA-paren

Välj regelbundet **Spara och träna** efter att du har gjort ändringar för att undvika att förlora ändringar.

![Lägga till metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samarbeta i en kunskapsbas](./collaborate-knowledge-base.md)

* [Hantera Azure-resurser som används av QnA Maker](set-up-qnamaker-service-azure.md)