---
title: 'Snabb start: skapa, träna och publicera kunskaps bas – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. QnA Maker kunskaps basen i det här exemplet skapas från en enkel webb sida med vanliga frågor och svar för att besvara frågor om återställning av BitLocker-nyckel.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc64196969b23f0aad77ff4d4495e4bb3e569c32
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888241"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Snabb start: skapa, träna och publicera QnA Maker kunskaps bas

Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. Den här artikeln innehåller ett exempel på hur du skapar en QnA Maker kunskaps bas från en enkel webb sida med vanliga frågor och svar om återställning av BitLocker-nyckel.

Ta med en användare av en CHI2TEST för att göra din kunskap mer engagerande med dina användare.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Krav

> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-new-qna-maker-knowledge-base"></a>Skapa en ny QnA Maker kunskaps bas

1. Logga in på [QnAMaker.AI](https://QnAMaker.ai) -portalen med dina Azure-autentiseringsuppgifter.

1. På QnA Maker-portalen väljer du **skapa en kunskaps bas**.

1. På sidan **skapa** väljer du **skapa en QNA-tjänst**. Du dirigeras till [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) för att konfigurera en QnA Maker-tjänst i din prenumeration. 

1. I QnA Maker-portalen väljer du QnA Maker-tjänsten i list rutorna. Om du har skapat en ny QnA Maker tjänst måste du uppdatera sidan.

   ![Skärm bild av att välja en QnA Maker tjänst kunskaps bas](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Ge kunskaps basen **mitt exempel på QNA KB**.

1. Lägg till ett Word-exempel dokument som en URL: 

    `https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`

1. Välj `+ Add URL`.

1. Lägg till  **_Professional_ CHI2TEST-chatt** till din KB. 

1. Välj **skapa din KB**.

    Extraherings processen tar några minuter att läsa dokumentet och identifiera frågor och svar.

    När QnA Maker har skapat kunskaps basen öppnas sidan **kunskaps bas** . Du kan redigera innehållet i kunskaps basen på den här sidan.

## <a name="add-a-new-question-and-answer-set"></a>Lägg till en ny fråga och svars uppsättning

1. På sidan **Redigera** på QNA Maker-portalen väljer du **Lägg till QNA-par**.
1. Lägg till följande fråga: 

    `How many Azure services are used by a knowledge base?`

1. Lägg till svaret som formaterats med _markdown_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Lägg till frågan som text och svaret formaterat med markdown.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Markdown-symbolen `*`används för punkter. `\n` används för en ny rad.  

    På sidan **Redigera** visas markdown. När du använder **test** panelen senare ser du att markdown visas som den ska. 

## <a name="save-and-train"></a>Spara och öva

Välj **Spara och öva** i det övre högra hörnet för att spara dina ändringar och öva med QnA Maker-modellen. Redigeringarna försvinner om de inte sparas.

## <a name="test-the-knowledge-base"></a>Testa kunskaps basen

1. I den QnA Maker portalen, i det övre högra hörnet, väljer du **test** för att testa att ändringarna du gjort har genomförts. 
1. Ange ett exempel på en användar fråga i text rutan. 

    `How many Azure services are used by a knowledge base?`  

    ![ Ange ett exempel på en användar fråga i text rutan. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Välj **Granska** för att undersöka svaret i detalj. Test fönstret används för att testa dina ändringar i kunskaps basen innan du publicerar din kunskaps bas.

1. Välj **testa** igen för att stänga **test** panelen.

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen

När du publicerar en kunskaps bas flyttas innehållet i din kunskaps bas från `test`-indexet till ett `prod`-index i Azure Search.

![Skärm bild av hur du flyttar innehållet i kunskaps basen](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. I QnA Maker Portal väljer du **publicera**. Bekräfta sedan genom att välja **Publicera** på sidan.

    QnA Maker-tjänsten har nu publicerats. Du kan använda slutpunkten i ditt program eller din robotkod.

    ![Skärm bild av lyckad publicering](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Skapa en robot

När du har publicerat kan du skapa en robot från **publicerings** sidan: 

* Du kan skapa flera robotar snabbt, allt som pekar på samma kunskaps bas för olika regioner eller prissättnings planer för enskilda robotar. 
* Om du bara vill ha en robot för kunskaps basen använder du länken **Visa alla robotar på Azure Portal** för att visa en lista över din aktuella robotar. 

När du gör ändringar i kunskaps basen och återpublicerar behöver du inte vidta några åtgärder i bot-roboten. Den har redan kon figurer ATS för att fungera med kunskaps basen och fungerar med alla framtida ändringar i kunskaps basen. Varje gång du publicerar en kunskaps bas uppdateras alla robotar som är anslutna till den automatiskt.

1. På sidan **publicera** i QNA Maker-portalen väljer du **skapa bot**. Den här knappen visas bara när du har publicerat kunskaps basen.

    ![Skärm bild som visar hur du skapar en robot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. En ny flik i webbläsaren öppnas för Azure Portal med sidan för att skapa Azure Bot Service. Konfigurera Azure bot service. 
    
    * Ändra inte följande inställningar i Azure Portal när du skapar bot-roboten. De är i förväg ifyllda för din befintliga kunskaps bas: 
        * QnA auth-nyckel
        * App Service-plan och plats
    * Robot-och QnA Maker kan dela Web App Service-planen, men kan inte dela webbappen. Det innebär att **appens namn** måste vara ett annat än namnet på appen för tjänsten QNA Maker. 

1. När roboten har skapats öppnar du **bot-tjänstens** resurs. 
1. Under **bot-hantering**väljer du **test i Web Chat**.
1. Skriv följande i Chat-prompten för att **skriva ditt meddelande**:

    `Azure services?`

    Chatt-roboten svarar med ett svar från din kunskaps bas. 

    ![Ange en användar fråga i test-webbchatten.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa QnA Maker-och bot Framework-resurser i Azure Portal. 

## <a name="next-steps"></a>Nästa steg

Mer information:

* [Markdown-format i svar](../concepts/data-sources-supported.md)
* [Testa din markdown](../concepts/data-sources-supported.md#testing-your-markdown)
* QnA Maker [data källor](../Concepts/data-sources-supported.md). 
* [Konfigurations inställningar för bot-resurser](../tutorials/create-qna-bot.md).

> [!div class="nextstepaction"]
> [Lägg till frågor med metadata](add-question-metadata-portal.md)

