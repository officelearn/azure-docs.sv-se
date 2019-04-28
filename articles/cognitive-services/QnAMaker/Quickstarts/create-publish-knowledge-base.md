---
title: Skapa, träna och publicera kunskapsbas – QnA Maker
titleSuffix: Azure Cognitive Services
description: Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. QnA Maker kunskapsbasen i det här exemplet skapas från en enkel vanliga frågor och svar-webbsida att besvara frågor om återställning av BitLocker-nyckel.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: cf6b0cd99254d5b034687598c588ce5ef13dbd53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914544"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Skapa, träna och publicera en QnA Maker-kunskapsbas

Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. QnA Maker kunskapsbasen i det här exemplet skapas från en enkel vanliga frågor och svar-webbsida att besvara frågor om återställning av BitLocker-nyckel.

## <a name="prerequisite"></a>Krav

> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-qna-maker-knowledge-base"></a>Skapa en QnA Maker-kunskapsbas

1. Logga in på [QnAMaker.ai](https://QnAMaker.ai) med dina autentiseringsuppgifter för Azure.

1. På QnA Maker-portalen väljer **skapa en kunskapsbas**.

   ![Skapa ny kunskapsbas](../media/qna-maker-create-kb.png)

1. På sidan **skapa** i steg 1 väljer du **Create a QnA service** (skapa en tjänst för frågor och svar). Du dirigeras till [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) för att konfigurera en QnA Maker-tjänst i din prenumeration. Om tidsgränsen för Azure-portalen passeras väljer du **Försök igen** på webbplatsen. När du har anslutit visas instrumentpanelen i Azure.

1. När du har skapat en ny QnA Maker-tjänst i Azure kan du gå tillbaka till qnamaker.ai/create. Välj din frågor och svar-tjänst från listrutorna i steg 2. Om du har skapat en ny QnA-tjänst måste du uppdatera sidan.

   ![Välj en kunskapsbas för frågor och svar om tjänsten](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. I steg 3, namnge din kunskapsbas **min exempel QnA KB**.

1. Välj tre typer av datakällor om du vill lägga till innehåll i kunskapsbasen. Under **Populate your KB** (fyll i kunskapsbasen) i steg 4 lägger du till webbadressen för [BitLocker Recovery FAQ](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) i rutan **URL**.

   ![Välj en kunskapsbas för frågor och svar om tjänsten](../media/qnamaker-quickstart-kb/add-datasources.png)

1. Välj **Create your KB** (skapa kunskapsbasen) i steg 5.

1. Medan kunskapsbasen skapas, visas ett popup-fönster. Det tar några minuter för extraheringsprocessen att läsa HTML-sidan och identifiera frågor och svar.

1. När kunskapsbasen har skapats, den **kunskapsbas** öppnas. Du kan redigera innehållet i kunskapsbasen på den här sidan.

## <a name="edit-the-knowledge-base"></a>Redigera i knowledge base

1. QnA Maker-portalen på den **redigera** avsnittet **Lägg till frågor och svar om par** att lägga till en ny rad i kunskapsbasen. Skriv **Hi** under **Fråga.** Skriv **Hello** under **Svar. Fråga mig BitLocker-frågor.**

    ![Lägga till par med fråga och svar](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. Välj **Spara och öva** i det övre högra hörnet för att spara dina ändringar och öva med QnA Maker-modellen. Redigeringarna försvinner om de inte sparas.

## <a name="test-the-knowledge-base"></a>Testa i knowledge base

1. I QnA Maker-portalen i det övre högra hörnet väljer **testa** att testa att de ändringar du gjort trädde i kraft. Ange `hi there` i rutan och tryck på RETUR. Du bör nu se svaret du skapade som ett svar.

1. Välj **Granska** för att undersöka svaret i detalj. Testfönstret används för att testa ändringarna i kunskapsbasen innan de publiceras.

    ![Testpanel](../media/qnamaker-quickstart-kb/inspect-panel.png)

1. Välj **Test** igen för att dölja popup-fönstret **Test**.

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen

När du publicerar en kunskapsbas flyttar frågor och svar innehållet i kunskapsbasen från test-index till ett index för produktion i Azure search.

![Publicera prod test index](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. I QnA Maker-portalen på menyn bredvid **redigera**väljer **publicera**. Bekräfta sedan genom att välja **Publicera** på sidan.

1. QnA Maker-tjänsten har nu publicerats. Du kan använda slutpunkten i ditt program eller din robotkod.

    ![Publicera](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Skapa en robot

Skapa en robot som alltid är tillgänglig när du har publicerat från den **publicera** sidan. 

* Du kan skapa flera robotar snabbt, alla som pekar på samma kunskapsbasen för olika regioner eller prissättningsplaner för enskilda robotar. 
* Om du vill endast en robot för kunskapsbasen använder den **visa alla dina bots på Azure portal** länken för att visa en lista över dina aktuella bots. 
* När du gör ändringar i kunskapsbasen och publicerar om den, behöver du inte vidta några ytterligare åtgärder med roboten. Den har redan konfigurerats för att fungera med i knowledge base och fungerar med alla framtida ändringar i knowledge base. Alla robotar som är anslutna till den uppdateras automatiskt varje gång du publicerar en kunskapsbas-

1. QnA Maker-portalen på den **publicera** väljer **skapa robot**. Den här knappen visas endast när kunskapsbasen har publicerats.

    ![Gå till sidan Publicera i QnA Maker-portalen och publicera din kunskapsbas. Välj Skapa Bot.](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. En ny webbläsarflik öppnas för Azure-portalen med sidan för att skapa Azure Bot Service. Konfigurera Azure bot service. Mer information om dessa konfigurationsinställningar finns i [skapa QnA-roboten med Azure Bot Service v4](../tutorials/create-qna-bot.md) självstudien.
    
    * Ändra inte följande inställningar i Azure-portalen när du skapar roboten. De är i förväg för dina befintliga kunskapsbasen: 
        * Frågor och svar om Auth-nyckel
        * App Service-plan/plats
        * Azure Storage
    * Bot och QnA Maker kan dela appen webbtjänsten _plan_ men kan inte dela webbappen. Det innebär att den **appnamn** måste skilja sig från det namn du använde när du skapade QnA Maker-tjänsten. 


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](../How-To/create-knowledge-base.md)
