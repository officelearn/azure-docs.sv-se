---
title: Så här redigerar du en knowledge base - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Så här redigerar du en kunskapsbas
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: eaa65bf3d257399fceadaa42f0d9ddbbf8afe234
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354093"
---
# <a name="edit-a-knowledge-base"></a>Redigera en kunskapsbas

Frågor och svar om Maker kan du hantera innehållet i kunskapsbasen genom att tillhandahålla en enkel att använda Redigera upplevelse.

## <a name="edit-your-knowledge-base-content"></a>Redigera knowledge base-innehåll

1.  Välj **min knowledge baser** i det övre navigeringsfältet. 

    Du kan se alla tjänster som du har skapat eller delas med dig sorterade i fallande ordning efter den **senast ändrad** datum.

    ![Min Knowledge baser](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Välj en viss kunskapsbas kan göra ändringar i den.

3. När du är klar med ändringarna i Knowledge base, klickar du på **spara och träna** i det övre högra hörnet på sidan för att spara ändringarna.    

    ![Spara och träna](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Om du lämnar sidan innan du klickar på Spara och tåget kommer inte att spara ändringarna.

## <a name="add-a-qna-pair"></a>Lägga till ett par med frågor och svar

Välj **Lägg till frågor och svar om par** att lägga till en ny rad i tabellen knowledge base.

![Lägg till par frågor och svar](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Ta bort ett par med frågor och svar

Ta bort en frågor och svar om, klicka på den **ta bort** ikonen längst till höger om raden frågor och svar.

![Ta bort frågor och svar om par](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Lägg till alternativa frågor

Lägg till alternativa frågor till en befintlig paret frågor och svar om att öka sannolikheten för en matchning till en användarfråga.

![Lägg till alternativa frågor](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Lägg till metadata


Lägg till metadata par genom att välja filterikonen

![Lägg till Metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Se till att spara regelbundet och träna kunskapsbasen när du har gjort ändringar för att undvika att ändringar går förlorade.

## <a name="manage-large-knowledge-bases"></a>Hantera stora knowledge baser

1. QnAs är **grupperade** av datakällan från vilken de extraherades. Du kan expandera eller komprimera data source.
2. Du kan **Sök** kunskapsbasen genom att skriva i textrutan längst upp i tabellen Knowledge Base. Klicka på Ange om du vill söka i fråga, svar eller metadata för innehållet. Klicka på ikonen X för att ta bort sökfilter.
3. **Sidbrytning** kan du hantera stora knowledge baser

    ![Söka, sidbryta grupp](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samarbeta med en kunskapsbas](./collaborate-knowledge-base.md)
