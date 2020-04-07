---
title: Hantera kunskapsbaser - QnA Maker
description: Med QnA Maker kan du hantera dina kunskapsbaser genom att ge tillgång till kunskapsbasinställningarna och innehållet.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4eb07b30b10826c361f4f9b805b517e372fc5ea1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756814"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Skapa kunskapsbas och hantera inställningar

Med QnA Maker kan du hantera dina kunskapsbaser genom att ge tillgång till kunskapsbasinställningarna och datakällorna.

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
> * En [QnA Maker-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) som skapats i Azure-portalen. Kom ihåg ditt Azure Active Directory-ID, Prenumeration, QnA-resursnamn som du valde när du skapade resursen.

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

1. Logga in [QnAMaker.ai](https://QnAMaker.ai) på QnAMaker.ai-portalen med dina Azure-autentiseringsuppgifter.

1. I QnA Maker-portalen väljer du **Skapa en kunskapsbas**.

1. Hoppa över steg **1** på sidan **Skapa** om du redan har din QnA Maker-resurs.

    Om du inte har skapat resursen ännu väljer du **Skapa en QnA-tjänst**. Du dirigeras till [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) för att konfigurera en QnA Maker-tjänst i din prenumeration. Kom ihåg ditt Azure Active Directory-ID, Prenumeration, QnA-resursnamn som du valde när du skapade resursen.

    När du är klar med att skapa resursen i Azure-portalen går du tillbaka till QnA Maker-portalen, uppdaterar webbläsarsidan och fortsätter till **steg 2**.

1. I **steg 3**väljer du din Active Directory, prenumeration, tjänst (resurs) och språket för alla kunskapsbaser som skapats i tjänsten.

   ![Skärmbild av att välja en kunskapsbas för QnA Maker-tjänsten](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. I **steg 3**namnger `My Sample QnA KB`du kunskapsbasen .

1. Konfigurera inställningarna med följande tabell i **steg 4:**

    |Inställning|Värde|
    |--|--|
    |**Aktivera extrahering med flera svängar från webbadresser, PDF- eller .docx-filer.**|Markerad|
    |**Standardsvarstext**| `Quickstart - default answer not found.`|
    |**+ Lägg till URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chatt**|Välj **Professional**|

1. I **steg 5**väljer du **Skapa kb.**

    Extraheringsprocessen tar en stund att läsa dokumentet och identifiera frågor och svar.

    När QnA Maker har skapat kunskapsbasen öppnas sidan **Kunskapsbas.** Du kan redigera innehållet i kunskapsbasen på den här sidan.

## <a name="edit-knowledge-base"></a>Redigera kunskapsbas

1.  Välj **Mina kunskapsbaser** i det övre navigeringsfältet.

       Du kan se alla tjänster som du har skapat eller delat med dig sorterade i fallande ordning för det **senast ändrade** datumet.

       ![Mina kunskapsbaser](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Välj en viss kunskapsbas om du vill göra ändringar i den.

1.  Välj **Inställningar**. Följande lista innehåller fält som du kan ändra.

       |Mål|Åtgärd|
       |--|--|
       |Lägg till URL|Du kan lägga till nya webbadresser för att lägga till nytt innehåll med vanliga frågor i Kunskapsbasen genom att klicka på **Hantera kunskapsbas -> länken +Lägg till webbadress.**|
       |Ta bort URL|Du kan ta bort befintliga webbadresser genom att välja borttagningsikonen, papperskorgen.|
       |Uppdatera innehåll|Om du vill att kunskapsbasen ska genomsöka det senaste innehållet i befintliga webbadresser markerar du kryssrutan **Uppdatera.** Då uppdateras kunskapsbasen med det senaste URL-innehållet en gång. Detta är inte att ställa in ett regelbundet schema med uppdateringar.|
       |Lägg till fil|Du kan lägga till ett fildokument som stöds för att ingå i en kunskapsbas genom att välja **Hantera kunskapsbas**och sedan välja **+ Lägg till fil**|
    |Importera|Du kan också importera en befintlig kunskapsbas genom att välja knappen **Importera kunskapsbas.** |
    |Uppdatering|Uppdatering av kunskapsbasen beror på **hanteringsprisnivå som** används när du skapar QnA Maker-tjänst som är associerad med din kunskapsbas. Du kan också uppdatera hanteringsnivån från Azure-portalen om det behövs.

  1. När du är klar med att göra ändringar i kunskapsbasen väljer du **Spara och tränar** i det övre högra hörnet på sidan för att bevara ändringarna.

       ![Spara och träna](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Om du lämnar sidan innan du väljer **Spara och träna**kommer alla ändringar att gå förlorade.



## <a name="manage-large-knowledge-bases"></a>Hantera stora kunskapsbaser

* **Datakällgrupper**: QnA:erna grupperas efter den datakälla från vilken de extraherades. Du kan expandera eller komprimera datakällan.

    ![Använd QnA Maker-datakällfältet för att dölja och expandera frågor och svar om datakällan](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Sök kunskapsbas:** Du kan söka i kunskapsbasen genom att skriva in textrutan högst upp i tabellen Kunskapsbas. Klicka på Retur om du vill söka efter frågan, svaret eller metadatainnehållet. Klicka på X-ikonen för att ta bort sökfiltret.

    ![Använd sökrutan QnA Maker ovanför frågorna och svaren för att minska vyn till endast filtermatchande objekt](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Sidnumrering:** Snabbt flytta genom datakällor för att hantera stora kunskapsbaser

    ![Använd QnA Maker sidnumreringsfunktionerna ovanför frågorna och svaren för att gå igenom sidor med frågor och svar](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Ta bort kunskapsbaser

Att ta bort en kunskapsbas (KB) är en permanent åtgärd. Det kan inte göras ogjort. Innan du tar bort en kunskapsbas bör du exportera kunskapsbasen från sidan **Inställningar** i QnA Maker-portalen.

Om du delar kunskapsbasen med medarbetare](samarbeta-kunskaps-base.md) och sedan tar bort den förlorar alla åtkomst till KB.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [hanterar språket för](language-knowledge-base.md) alla kunskapsbaser i en resurs.

* Redigera QnA-par
* Hantera Azure-resurser som används av QnA Maker