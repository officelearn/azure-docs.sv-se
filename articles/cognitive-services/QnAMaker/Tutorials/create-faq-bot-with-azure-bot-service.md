---
title: 'Självstudie: skapa en robot för vanliga frågor och svar med Azure Bot Service'
description: I den här självstudien skapar du en robot med vanliga frågor och svar med QnA Maker och Azure Bot Service.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: ab78ac41be2f900d84e82c21e566f5250b2afd18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777390"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Självstudie: skapa en robot för vanliga frågor och svar med Azure Bot Service
Skapa en bot-robot med QnA Maker och Azure [bot service](https://azure.microsoft.com/en-us/services/bot-service/) utan kod.

I den här guiden får du lära dig att:

<!-- green checkmark -->
> [!div class="checklist"]
> * Länka en QnA Maker kunskaps bas till en Azure Bot Service
> * Distribuera bot
> * Chatta med roboten i Web Chat
> * Ljust bot i de kanaler som stöds

## <a name="create-and-publish-a-knowledge-base"></a>Skapa och publicera en kunskapsbas

Följ [snabb](../Quickstarts/create-publish-knowledge-base.md) starten för att skapa en kunskaps bas. När kunskaps basen har publicerats visas sidan nedan.

![Skärm bild av lyckad publicering](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Skapa en robot

När du har publicerat kan du skapa en robot från **publicerings** sidan:

* Du kan skapa flera robotar snabbt, allt som pekar på samma kunskaps bas för olika regioner eller prissättnings planer för enskilda robotar.
* Om du bara vill ha en robot för kunskaps basen använder du länken **Visa alla robotar på Azure Portal** för att visa en lista över din aktuella robotar.

När du gör ändringar i kunskaps basen och återpublicerar behöver du inte vidta några åtgärder i bot-roboten. Den har redan kon figurer ATS för att fungera med kunskaps basen och fungerar med alla framtida ändringar i kunskaps basen. Varje gång du publicerar en kunskaps bas uppdateras alla robotar som är anslutna till den automatiskt.

1. På sidan **publicera** i QNA Maker-portalen väljer du **skapa bot**. Den här knappen visas bara när du har publicerat kunskaps basen.

    ![Skärm bild som visar hur du skapar en robot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. En ny flik i webbläsaren öppnas för Azure Portal med sidan för att skapa Azure Bot Service. Konfigurera Azure bot service. Robot-och QnA Maker kan dela Web App Service-planen, men kan inte dela webbappen. Det innebär att **appens namn** måste vara ett annat än namnet på appen för tjänsten QNA Maker.

    * **Gör följande**
        * Ändra bot-referens – om den inte är unik.
        * Välj SDK-språk. När bot har skapats kan du ladda ned koden till din lokala utvecklings miljö och fortsätta utvecklings processen.
    * **Gör inte följande**
        * Ändra följande inställningar i Azure Portal när du skapar bot-roboten. De är i förväg ifyllda för din befintliga kunskaps bas:
           * QnA auth-nyckel
           * App Service-plan och plats


1. När roboten har skapats öppnar du **bot-tjänstens** resurs.
1. Under **bot-hantering**väljer du **test i Web Chat**.
1. Skriv följande i Chat-prompten för att **skriva ditt meddelande**:

    `Azure services?`

    Chatt-roboten svarar med ett svar från din kunskaps bas.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Ange en användar fråga i test-webbchatten.":::
1. Ljust bot i fler kanaler som [stöds](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0&preserve-view=true).
