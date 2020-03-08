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
ms.date: 11/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b5ee7f60eab0349378767473c9c80f035a65c9a5
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668541"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Redigera en kunskapsbas i QnA Maker

QnA Maker kan du hantera innehållet i kunskapsbasen genom att tillhandahålla en enkel att använda redigering upplevelse.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Redigera din kunskapsbas-innehåll

1.  Välj **Mina kunskaps banker** i det övre navigerings fältet. 

    Du kan se alla tjänster som du har skapat eller delat med sorterade i fallande ordning efter **senaste ändrings** datum.

    ![Min kunskapsbaser](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Välj en viss kunskapsbas kan göra ändringar i den.
 
1. Välj **Inställningar**. Här kan du redigera obligatoriskt fält tjänstnamn.
  
    |Mål|Åtgärd|
    |--|--|
    |Lägg till webbadress|Du kan lägga till nya URL: er för att lägga till nya FAQ-innehåll i kunskaps basen genom att klicka på **Hantera kunskaps bas-> ' + Lägg till URL '**|
    |Ta bort URL|Du kan ta bort befintliga webbadresser genom att välja ikonen Ta bort, kan Papperskorgen.|
    |Uppdatera innehåll|Om du vill att din kunskaps bas ska crawla det senaste innehållet i befintliga webb adresser markerar du kryss rutan **Uppdatera** . Detta uppdaterar kunskaps basen med senaste URL-innehåll en gång. Detta ställer inte in ett regelbundet uppdaterings schema.|
    |Lägg till fil|Du kan lägga till ett fil dokument som stöds som en del av en kunskaps bas genom att välja **Hantera kunskaps bas**och sedan välja **+ Lägg till fil**|
    |Importera|Du kan också importera en befintlig kunskaps bas genom att välja knappen **Importera kunskaps bas** . |
    |Uppdatera|Uppdatering av kunskaps basen beror på vilken **hanterings pris nivå** som används när du skapar QNA Maker tjänst som är kopplad till din kunskaps bas. Du kan också uppdatera hanteringsnivån från Azure-portalen om det behövs.

1. När du är klar med att göra ändringar i kunskaps basen väljer du **Spara och träna** i det övre högra hörnet på sidan för att spara ändringarna.    

    ![Spara och träna](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Om du lämnar sidan innan du väljer **Spara och träna**, kommer alla ändringar att gå förlorade.

## <a name="add-a-qna-pair"></a>Lägga till par med fråga och svar

På sidan **Redigera** väljer du **Lägg till QNA-par** för att lägga till en ny rad i kunskaps bas tabellen.

![Lägg till frågor och svar om par](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Ta bort ett par frågor och svar

Om du vill ta bort en QnA klickar du på ikonen **ta bort** längst till höger på raden QNA. Det här är en permanent åtgärd. Det går inte att ångra. Överväg att exportera dina KB från sidan **publicera** innan du tar bort par. 

![Ta bort frågor och svar om par](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Lägga till alternativa frågor

Lägg till alternativa frågor i ett befintligt nyckelpar med frågor och svar om att förbättra sannolikheten för en matchning för att en användarfråga.

![Lägga till alternativa frågor](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>lägga till metadata

Lägg till metadata-par genom att först välja **visnings alternativ**och sedan välja **Visa metadata**. Kolumnen metadata visas. Välj sedan **+** signera för att lägga till ett metadata-par. Det här paret består av en nyckel och ett värde.

![Lägga till Metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Se till att regelbundet spara och tränar kunskapsbasen när du har gjort ändringar för att undvika att förlora ändringar.

## <a name="manage-large-knowledge-bases"></a>Hantera stora kunskapsbaser

* **Data käll grupper**: kring grupperas efter den data källa som de extraherades från. Du kan visa eller dölja datakällan.

    ![Använd QnA Maker data källans fält för att komprimera och expandera frågor och svar för data källor](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Sök i kunskaps basen**: du kan söka i kunskaps basen genom att skriva i text rutan längst upp i tabellen kunskaps bas. Klicka på Ange om du vill söka i fråga, svar eller metadata för innehållet. Klicka på X-ikonen Ta bort sökrutan filtrera.

    ![Använd sökrutan QnA Maker ovanför frågorna och Svaren för att minska vyn till enbart filter matchnings objekt](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Sid brytning**: flytta snabbt genom data källor för att hantera stora kunskaps baser

    ![Använd QnA Maker sid brytnings funktioner ovanför frågorna och Svaren för att flytta genom sidor med frågor och svar](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Ta bort kunskapsbaser

Tar bort en knowledge base (KB) är en permanent åtgärd. Det går inte att ångra. Innan du tar bort en kunskaps bas bör du exportera kunskaps basen från sidan **Inställningar** i QNA Maker portalen. 

Om du delar dina KB med [medarbetare](collaborate-knowledge-base.md) tar du bort det och alla förlorar åtkomsten till KB. 

## <a name="delete-azure-resources"></a>Ta bort Azure-resurser 

Om du tar bort någon av de Azure-resurser används för kunskapsbaser din QnA Maker fungerar inte längre kunskapsbaser. Innan du tar bort några resurser bör du kontrol lera att du exporterar dina kunskaps baser från sidan **Inställningar** . 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samar beta i en kunskaps bas](./collaborate-knowledge-base.md)
