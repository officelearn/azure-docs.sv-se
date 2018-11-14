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
ms.date: 11/08/2018
ms.author: tulasim
ms.openlocfilehash: fa5b5e1998d54c7b22d01e7cc70e84059d482cfb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623554"
---
# <a name="edit-a-knowledge-base"></a>Redigera en kunskapsbas

QnA Maker kan du hantera innehållet i kunskapsbasen genom att tillhandahålla en enkel att använda redigering upplevelse.

## <a name="edit-your-knowledge-base-content"></a>Redigera din kunskapsbas-innehåll

1.  Välj **min kunskapsbaser** i det övre navigeringsfältet. 

    Du kan se alla tjänster som du har skapat eller delat med dig sorterade i fallande ordning efter den **senast ändrad** datum.

    ![Min kunskapsbaser](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Välj en viss kunskapsbas kan göra ändringar i den.
 
1. Välj **inställningar**. Här kan du redigera obligatoriskt fält tjänstnamn.
  
    |Mål|Åtgärd|
    |--|--|
    |Lägg till webbadress|Du kan lägga till nya URL: er för att lägga till nytt innehåll för vanliga frågor och svar till kunskapsbas genom att klicka på **hantera knowledge base -> ”+ Lägg till URL: en”** länk.|
    |Ta bort URL|Du kan ta bort befintliga webbadresser genom att välja ikonen Ta bort, kan Papperskorgen.|
    |Uppdatera URL: en innehåll|Om du vill att din kunskapsbas att uppdatera befintliga webbadresser senaste innehåll, väljer den **uppdatera** kryssrutan. Kunskapsbasen uppdateras med senaste URL-innehåll.|
    |Lägg till fil|Du kan lägga till ett dokument för stöds filen som en del av en kunskapsbas genom att välja **hantera kunskapsbas**, sedan välja **+ Lägg till fil**|
    |Importera|Du kan också importera alla befintliga kunskapsbas genom att välja **Ímport kunskapsbas** knappen. |
    |Uppdatering|Uppdatering av kunskapsbas beror på **management prisnivån** används för att skapa QnA Maker-tjänsten som är associerade med din kunskapsbas. Du kan också uppdatera hanteringsnivån från Azure-portalen om det behövs.

1. När du är klar att göra ändringar i kunskapsbasen, väljer **spara och träna** i det övre högra hörnet på sidan för att spara ändringarna.    

    ![Spara och träna](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Om du lämnar sidan innan du väljer **spara och träna**, alla ändringar går förlorade.

## <a name="add-a-qna-pair"></a>Lägga till par med fråga och svar

Välj **Lägg till frågor och svar om par** att lägga till en ny rad i tabellen knowledge base.

![Lägg till frågor och svar om par](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Ta bort ett par frågor och svar

Ta bort en frågor och svar genom att klicka på den **ta bort** ikonen längst till höger för frågor och svar om raden. Det här är en permanent åtgärd. Det går inte att ångra. Överväga att exportera din Kunskapsbas från den **publicera** sidan innan du tar bort par. 

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

## <a name="delete-knowledge-bases"></a>Ta bort kunskapsbaser

Tar bort en knowledge base (KB) är en permanent åtgärd. Det går inte att ångra. Innan du tar bort en kunskapsbas exporterar kunskapsbas från den **inställningar** i QnA Maker-portalen. 

Om du delar din Kunskapsbas med [medarbetare](collaborate-knowledge-base.md) tar bort den, alla förlorar åtkomst till KB. 

## <a name="delete-azure-resources"></a>Ta bort Azure-resurser 

Om du tar bort någon av de Azure-resurser används för kunskapsbaser din QnA Maker fungerar inte längre kunskapsbaser. Innan du tar bort alla resurser, kontrollera att du exporterar dina kunskapsbaser från den **inställningar** sidan. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samarbeta med en kunskapsbas](./collaborate-knowledge-base.md)
