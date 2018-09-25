---
title: Redigera en kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker kan du hantera innehållet i kunskapsbasen genom att tillhandahålla en enkel att använda redigering upplevelse.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: ca35e13c502efadad07ef78077271923951ae4b1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038229"
---
# <a name="edit-a-knowledge-base"></a>Redigera en kunskapsbas

QnA Maker kan du hantera innehållet i kunskapsbasen genom att tillhandahålla en enkel att använda redigering upplevelse.

## <a name="edit-your-knowledge-base-content"></a>Redigera din kunskapsbas-innehåll

1.  Välj **min kunskapsbaser** i det övre navigeringsfältet. 

    Du kan se alla tjänster som du har skapat eller delat med dig sorterade i fallande ordning efter den **senast ändrad** datum.

    ![Min kunskapsbaser](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Välj en viss kunskapsbas kan göra ändringar i den.

3. När du är klar med ändringarna i kunskapsbasen, klickar du på **spara och träna** i det övre högra hörnet på sidan för att spara ändringarna.    

    ![Spara och träna](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Lämna sidan innan du klickar på Spara och träna behålls inte ändringarna.

## <a name="add-a-qna-pair"></a>Lägga till ett par frågor och svar

Välj **Lägg till frågor och svar om par** att lägga till en ny rad i tabellen knowledge base.

![Lägg till frågor och svar om par](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Ta bort ett par frågor och svar

Ta bort en frågor och svar genom att klicka på den **ta bort** ikonen längst till höger för frågor och svar om raden.

![Ta bort frågor och svar om par](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Lägga till alternativa frågor

Lägg till alternativa frågor i ett befintligt nyckelpar med frågor och svar om att förbättra sannolikheten för en matchning för att en användarfråga.

![Lägga till alternativa frågor](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>lägga till metadata


Lägg till metadata par genom att välja filterikonen

![Lägga till Metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Se till att regelbundet spara och tränar kunskapsbasen när du har gjort ändringar för att undvika att förlora ändringar.

## <a name="manage-large-knowledge-bases"></a>Hantera stora kunskapsbaser

1. Kunskapsbaser är **grupperade** av datakällan från vilken de extraherades. Du kan visa eller dölja datakällan.
2. Du kan **search** kunskapsbas genom att skriva i textrutan längst ned i Knowledge Base-tabell. Klicka på Ange om du vill söka i fråga, svar eller metadata för innehållet. Klicka på X-ikonen Ta bort sökrutan filtrera.
3. **Sidbrytning** kan du hantera stora kunskapsbaser

    ![Sök, sidbryta grupp](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samarbeta med en kunskapsbas](./collaborate-knowledge-base.md)
