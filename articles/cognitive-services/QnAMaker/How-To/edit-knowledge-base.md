---
title: Redigera en kunskapsbas - QnA Maker
description: Med QnA Maker kan du hantera innehållet i din kunskapsbas genom att tillhandahålla en lättanvänd redigeringsupplevelse.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131645"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>Redigera QnA-uppsättningar i din kunskapsbas

Med QnA Maker kan du hantera innehållet i din kunskapsbas genom att tillhandahålla en lättanvänd redigeringsupplevelse.

QnA-uppsättningar läggs till från en datakälla, till exempel en fil eller URL, eller läggs till som en redaktionell källa. En redaktionell källa anger att QnA-uppsättningen har lagts till i QnA-portalen manuellt. Alla QnA-uppsättningar är tillgängliga för redigering.

## <a name="add-an-editorial-qna-set"></a>Lägga till en redaktionell QnA-uppsättning
1. Logga in på [QnA-portalen](https://www.qnamaker.ai/)och välj sedan kunskapsbasen som du vill lägga till QnA-uppsättningen i.
1. På sidan **REDIGERA** i kunskapsbasen väljer du **Lägg till QnA-uppsättning** för att lägga till en ny QnA-uppsättning.

1. Lägg till de **obligatoriska fråge-** och svarsfälten **i** den nya QnA-uppsättningenraden. De andra fälten är valfria. Alla fält kan ändras när som helst.

1. Du kan också lägga till **alternativ frasering**. Alternativ frasering är någon form av frågan som skiljer sig avsevärt från den ursprungliga frågan, men bör ge samma svar.

    När din kunskapsbas publiceras och du har aktiverat aktiv inlärning samlar QnA Maker in alternativa fraseringsalternativ som du kan acceptera. Dessa alternativ väljs för att öka förutsägelsenoggrannheten.

1. Du kan också lägga till **metadata**. Om du vill visa metadata väljer du **Visa alternativ** på snabbmenyn. Metadata innehåller filter till de svar som klientprogrammet, till exempel en chattrobot, tillhandahåller.

1. Du kan också lägga till **uppföljningsan följande.** Uppföljningsansanningar ger ytterligare konversationssökvägar till klientprogrammet som ska visas för användaren.

1. Välj **Spara och träna** om du vill se förutsägelser, inklusive den nya QnA-uppsättningen.

## <a name="edit-a-qna-set"></a>Redigera en QnA-uppsättning

Alla fält i alla QnA-set kan redigeras, oavsett den ursprungliga datakällan. Vissa fält kanske inte visas på grund av de aktuella inställningarna för **visningsalternativ** som finns i kontextverktygsfältet.

## <a name="delete-a-qna-set"></a>Ta bort en QnA-uppsättning

Om du vill ta bort en QnA klickar du på **borttagningsikonen** längst till höger på QnA-raden. Det här är en permanent operation. Det kan inte göras ogjort. Överväg att exportera KB från sidan **Publicera** innan du tar bort uppsättningar.

![Ta bort QnA-uppsättning](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>Hitta QnA-set-ID:t

Om du behöver hitta QnA-set-ID:t kan du hitta det på två ställen:

* Hovra på borttagningsikonen på den QnA-uppsättningsrad du är intresserad av. Hovringstexten innehåller QnA-set-ID: t.
* Exportera kunskapsbasen. Varje QnA-uppsättning i kunskapsbasen innehåller QnA-set-ID.

## <a name="add-alternate-questions"></a>Lägg till alternativa frågor

Lägg till alternativa frågor i en befintlig QnA-uppsättning för att förbättra sannolikheten för en matchning till en användarfråga.

![Lägg till alternativa frågor](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>Länka QnA-uppsättningar

Länka QnA uppsättningar är försedd med [uppföljningsan följande](multiturn-conversation.md). Detta är en logisk koppling mellan QnA-uppsättningar, som hanteras på kunskapsbasnivå. Du kan redigera uppföljningsansanningar i QnA Maker-portalen.

Du kan inte länka QnA-uppsättningar i svarets metadata.

## <a name="add-metadata"></a>Lägga till metadata

Lägg till metadatauppsättningar genom att först välja **Visa alternativ**och välj sedan **Visa metadata**. Då visas kolumnen metadata. Välj sedan **+** tecknet för att lägga till en metadatauppsättning. Den här uppsättningen består av en nyckel och ett värde.

## <a name="save-changes-to-the-qna-sets"></a>Spara ändringar i QnA-uppsättningarna

Välj regelbundet **Spara och träna** efter att du har gjort ändringar för att undvika att förlora ändringar.

![Lägga till metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samarbeta i en kunskapsbas](./collaborate-knowledge-base.md)

* [Hantera Azure-resurser som används av QnA Maker](set-up-qnamaker-service-azure.md)