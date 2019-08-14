---
title: Redigera en kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker kan du hantera innehållet i kunskapsbasen genom att tillhandahålla en enkel att använda redigering upplevelse.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1342616a0d32d8ac68965bc04f1fd3ca60146369
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966772"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Redigera en kunskapsbas i QnA Maker

QnA Maker kan du hantera innehållet i kunskapsbasen genom att tillhandahålla en enkel att använda redigering upplevelse.

<a name="add-datasource"></a>

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

På sidan **Inställningar** väljer du **Lägg till QNA-par** för att lägga till en ny rad i kunskaps bas tabellen.

![Lägg till frågor och svar om par](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Ta bort ett par frågor och svar

Ta bort en frågor och svar genom att klicka på den **ta bort** ikonen längst till höger för frågor och svar om raden. Det här är en permanent åtgärd. Det går inte att ångra. Överväga att exportera din Kunskapsbas från den **publicera** sidan innan du tar bort par. 

![Ta bort frågor och svar om par](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Lägga till alternativa frågor

Lägg till alternativa frågor i ett befintligt nyckelpar med frågor och svar om att förbättra sannolikheten för en matchning för att en användarfråga.

![Lägga till alternativa frågor](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>lägga till metadata

Lägg till metadata-par genom att först välja **visnings alternativ**och sedan välja **Visa metadata**. Kolumnen metadata visas. Sedan väljer du **+** tecknet för att lägga till ett metadata-par. Det här paret består av en nyckel och ett värde.

![Lägga till Metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Se till att regelbundet spara och tränar kunskapsbasen när du har gjort ändringar för att undvika att förlora ändringar.

## <a name="manage-large-knowledge-bases"></a>Hantera stora kunskapsbaser

* **Data käll grupper**: Kring grupperas efter den data källa som de extraherades från. Du kan visa eller dölja datakällan.

    ![Använd QnA Maker data källans fält för att komprimera och expandera frågor och svar för data källor](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Sök i kunskaps bas**: Du kan söka i kunskaps basen genom att skriva i text rutan längst upp i tabellen kunskaps bas. Klicka på Ange om du vill söka i fråga, svar eller metadata för innehållet. Klicka på X-ikonen Ta bort sökrutan filtrera.

    ![Använd sökrutan QnA Maker ovanför frågorna och Svaren för att minska vyn till enbart filter matchnings objekt](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Sid brytning**: Flytta snabbt genom data källor för att hantera stora kunskaps baser

    ![Använd QnA Maker sid brytnings funktioner ovanför frågorna och Svaren för att flytta genom sidor med frågor och svar](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Ta bort kunskapsbaser

Tar bort en knowledge base (KB) är en permanent åtgärd. Det går inte att ångra. Innan du tar bort en kunskapsbas exporterar kunskapsbas från den **inställningar** i QnA Maker-portalen. 

Om du delar din Kunskapsbas med [medarbetare](collaborate-knowledge-base.md) tar bort den, alla förlorar åtkomst till KB. 

## <a name="delete-azure-resources"></a>Ta bort Azure-resurser 

Om du tar bort någon av de Azure-resurser används för kunskapsbaser din QnA Maker fungerar inte längre kunskapsbaser. Innan du tar bort alla resurser, kontrollera att du exporterar dina kunskapsbaser från den **inställningar** sidan. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samarbeta med en kunskapsbas](./collaborate-knowledge-base.md)
