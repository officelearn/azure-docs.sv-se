---
title: 'Snabbstart: Skapa en KB - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Du kan skapa en QnA Maker knowledge base (KB) från ditt eget innehåll, till exempel vanliga frågor och svar eller produkthandböcker. QnA Maker KB i det här exemplet skapas från en enkel vanliga frågor och svar-webbsida att besvara frågor om återställning av BitLocker-nyckel.
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: f7af86687a8a61fb7aed028d2868752faaa8045a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715113"
---
# <a name="create-train-and-publish-your-knowledge-base"></a>Skapa, träna och publicera din kunskapsbas

Du kan skapa en QnA Maker knowledge base (KB) från ditt eget innehåll, till exempel vanliga frågor och svar eller produkthandböcker. QnA Maker KB i det här exemplet skapas från en enkel vanliga frågor och svar-webbsida att besvara frågor om återställning av BitLocker-nyckel.

## <a name="prerequisite"></a>Krav

> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-qna-maker-knowledge-base"></a>Skapa en kunskapsbas med QnA Maker

1. Logga in på QnAMaker.ai med dina autentiseringsuppgifter för Azure.

2. På webbplatsen QnA Maker väljer **skapa en kunskapsbas**.

   ![Skapa ny KB](../media/qna-maker-create-kb.png)

3. På den **skapa** sida i steg 1, väljer **skapar du en tjänst för frågor och svar om**. Du dirigeras till den [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) att ställa in QnA Maker-tjänsten i din prenumeration. Om Azure-portalen tidsgränsen, Välj **igen** på webbplatsen. När du har anslutit, visas instrumentpanelen i Azure.

4. När du har skapat en ny QnA Maker-tjänsten i Azure kan du gå tillbaka till qnamaker.ai/create. Välj frågor och svar om tjänsten listrutorna i steg 2. Om du har skapat en ny QnA-tjänst måste du uppdatera sidan.

   ![Välj en frågor och svar om tjänsten KB](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. I steg 3, namnge din Kunskapsbas **min exempel QnA KB**.

6. Välj tre typer av datakällor om du vill lägga till innehåll i din Kunskapsbas. I steg 4, under **fylla i din Kunskapsbas**, lägga till den [BitLocker Recovery vanliga frågor och svar](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL: en i den **URL** box.

   ![Välj en frågor och svar om tjänsten KB](../media/qnamaker-quickstart-kb/add-datasources.png)

7. I steg 5 väljer **skapa din Kunskapsbas**.

8. Medan KB skapas, visas ett popup-fönster. Extraheringsprocessen tar några minuter att läsa HTML-sidan och identifiera frågor och svar.

9. När KB har skapats, den **kunskapsbas** öppnas. Du kan redigera innehållet i KB på den här sidan.

10. I det övre högra hörnet väljer **Lägg till frågor och svar om par** att lägga till en ny rad i den **språkliga** avsnittet i KB. Under **fråga**, ange **Hej.** Under **svar**, ange **Hello. Fråga mig bitlocker-frågor.**

   ![Lägga till ett par frågor och svar](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. I det övre högra hörnet väljer **spara och träna** att spara dina ändringar och öva med QnA Maker-modellen. Redigeringar kvar inte om du har sparat.

   ![Spara och träna](../media/qnamaker-quickstart-kb/add-qna-pair2.png)

12. I det övre högra hörnet väljer **testa** att testa att de ändringar du gjort trädde i kraft. Ange **Hej det** i rutan och välj RETUR. Du bör se svaret som du skapade som ett svar.

13. Välj **granska** att undersöka svaret i detalj. Testfönstret används för att testa dina ändringar till KB innan de publiceras.

   ![Test-panelen](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Välj **Test** igen för att stänga den **Test** popup.

15. På menyn bredvid **redigera**väljer **publicera**. Välj sedan för att bekräfta, **publicera** på sidan.

16. QnA Maker-tjänsten är nu publicerats. Du kan använda slutpunkten i ditt program eller bot.

   ![Publicera](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](../How-To/create-knowledge-base.md)
