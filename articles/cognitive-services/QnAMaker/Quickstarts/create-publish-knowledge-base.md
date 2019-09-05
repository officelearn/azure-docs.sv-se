---
title: 'Snabbstart: Skapa, träna och publicera kunskaps bas – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. QnA Maker kunskaps basen i det här exemplet skapas från en enkel webb sida med vanliga frågor och svar för att besvara frågor om återställning av BitLocker-nyckel.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 229956b41f7f26637e6a6aa62e341c4b06b8a429
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376367"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Snabbstart: Skapa, träna och publicera en QnA Maker-kunskapsbas

Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. Den här artikeln innehåller ett exempel på hur du skapar en QnA Maker kunskaps bas från en enkel webb sida med vanliga frågor och svar om återställning av BitLocker-nyckel.

## <a name="prerequisite"></a>Krav

> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-qna-maker-knowledge-base"></a>Skapa en QnA Maker-kunskapsbas

1. Logga in på [QnAMaker.AI](https://QnAMaker.ai) -portalen med dina Azure-autentiseringsuppgifter.

1. På QnA Maker-portalen väljer du **skapa en kunskaps bas**.

   ![Skärm bild av QnA Maker Portal](../media/qna-maker-create-kb.png)

1. På sidan **skapa** i steg 1 väljer du **Create a QnA service** (skapa en tjänst för frågor och svar). Du dirigeras till [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) för att konfigurera en QnA Maker-tjänst i din prenumeration. Om tidsgränsen för Azure-portalen passeras väljer du **Försök igen** på webbplatsen. När du har anslutit visas instrumentpanelen i Azure.

1. När du har skapat en ny QnA Maker-tjänst i Azure kan du gå tillbaka till qnamaker.ai/create. Välj din QnA Maker-tjänst i list rutorna i steg 2. Om du har skapat en ny QnA Maker tjänst måste du uppdatera sidan.

   ![Skärm bild av att välja en QnA Maker tjänst kunskaps bas](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. I steg 3 namnger du kunskaps basen **mitt exempel på QNA KB**.

1. Om du vill lägga till innehåll i kunskaps basen väljer du tre typer av data källor. Under **Populate your KB** (fyll i kunskapsbasen) i steg 4 lägger du till webbadressen för [BitLocker Recovery FAQ](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) i rutan **URL**.

   ![Skärm bild av lägga till data källor](../media/qnamaker-quickstart-kb/add-datasources.png)

1. Välj **Create your KB** (skapa kunskapsbasen) i steg 5.

1. När QnA Maker skapar kunskaps basen visas ett popup-fönster. Det tar några minuter för extraheringsprocessen att läsa HTML-sidan och identifiera frågor och svar.

1. När QnA Maker har skapat kunskaps basen öppnas sidan **kunskaps bas** . Du kan redigera innehållet i kunskaps basen på den här sidan.

## <a name="edit-the-knowledge-base"></a>Redigera kunskaps basen

1. I QnA Maker portal går du till avsnittet **Redigera** och väljer **Lägg till QNA-par** för att lägga till en ny rad i kunskaps basen. Skriv **Hi** under **Fråga.** Skriv **Hello** under **Svar. Fråga mig om BitLocker-frågor.**

    ![Skärm bild av QnA Maker Portal](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. Välj **Spara och öva** i det övre högra hörnet för att spara dina ändringar och öva med QnA Maker-modellen. Redigeringarna försvinner om de inte sparas.

## <a name="test-the-knowledge-base"></a>Testa kunskaps basen

1. I den QnA Maker portalen, i det övre högra hörnet, väljer du **test** för att testa att ändringarna du gjort har genomförts. Ange `hi there` i rutan och tryck på RETUR. Du bör nu se svaret du skapade som ett svar.

1. Välj **Granska** för att undersöka svaret i detalj. Test fönstret används för att testa dina ändringar i kunskaps basen innan de publiceras.

    ![Skärm bild av test panelen](../media/qnamaker-quickstart-kb/inspect.png)

1. Välj **Test** igen för att dölja popup-fönstret **Test**.

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen

När du publicerar en kunskaps bas, flyttas frågan och svars innehållet i kunskaps basen från test indexet till ett produktions index i Azure Search.

![Skärm bild av hur du flyttar innehållet i kunskaps basen](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. I QnA Maker portal går du till menyn bredvid **Redigera**och väljer **publicera**. Bekräfta sedan genom att välja **Publicera** på sidan.

1. QnA Maker-tjänsten har nu publicerats. Du kan använda slutpunkten i ditt program eller din robotkod.

    ![Skärm bild av lyckad publicering](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Skapa en robot

När du har publicerat kan du skapa en robot från **publicerings** sidan: 

* Du kan skapa flera robotar snabbt, allt som pekar på samma kunskaps bas för olika regioner eller prissättnings planer för enskilda robotar. 
* Om du bara vill ha en robot för kunskaps basen använder du länken **Visa alla robotar på Azure Portal** för att visa en lista över din aktuella robotar. 

När du gör ändringar i kunskaps basen och återpublicerar behöver du inte vidta några åtgärder i bot-roboten. Den har redan kon figurer ATS för att fungera med kunskaps basen och fungerar med alla framtida ändringar i kunskaps basen. Varje gång du publicerar en kunskaps bas uppdateras alla robotar som är anslutna till den automatiskt.

1. På sidan **publicera** i QNA Maker-portalen väljer du **skapa bot**. Den här knappen visas bara när du har publicerat kunskaps basen.

    ![Skärm bild som visar hur du skapar en robot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. En ny flik i webbläsaren öppnas för Azure Portal med sidan för att skapa Azure Bot Service. Konfigurera Azure bot service. Mer information om dessa konfigurations inställningar finns i [skapa en QNA-robot med Azure bot service v4](../tutorials/create-qna-bot.md).
    
    * Ändra inte följande inställningar i Azure Portal när du skapar bot-roboten. De är i förväg ifyllda för din befintliga kunskaps bas: 
        * QnA auth-nyckel
        * App Service-plan och plats
        * Azure Storage
    * Robot-och QnA Maker kan dela Web App Service-planen, men kan inte dela webbappen. Det innebär att **appens namn** måste vara ett annat än namnet på den app som du använde när du skapade tjänsten QNA Maker. 


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](../How-To/create-knowledge-base.md)
