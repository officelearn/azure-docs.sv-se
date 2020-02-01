---
title: 'Självstudie: QnA bot-Azure Bot Service-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Den här självstudien visar hur du skapar en QnA chatt-robot från publicerings sidan för en befintlig kunskaps bas. Den här roboten använder bot Framework SDK v4. Du behöver inte skriva någon kod för att skapa bot. all kod tillhandahålls åt dig.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: diberry
ms.openlocfilehash: a9afef07af1332910cc9e25f9d66db5930df3bd4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901655"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Självstudie: skapa en QnA-robot med Azure Bot Service v4

Skapa en QnA chatt-robot från **publicerings** sidan för en befintlig kunskaps bas. Den här roboten använder bot Framework SDK v4. Du behöver inte skriva någon kod för att skapa bot. all kod tillhandahålls åt dig.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa en Azure Bot Service från en befintlig kunskaps bas
> * Chatta med roboten att kontrollera koden fungerar

## <a name="prerequisites"></a>Krav

Du måste ha en publicerad kunskapsbas för den här självstudiekursen. Om du inte har en sådan följer du anvisningarna i själv studie kursen [skapa och svara från KB](create-publish-query-in-portal.md) för att skapa en QNA Maker kunskaps bas med frågor och svar.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Skapa en QnA-robot

Skapa en robot som ett klient program för kunskaps basen.

1. Gå till sidan **publicera** i QNA Maker-portalen och publicera din kunskaps bas. Välj **skapa bot**.

    [![i QnA Maker-portalen, gå till sidan publicera och publicera din kunskaps bas. Välj Skapa bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png#lightbox)

    Azure Portal öppnas med konfigurationen för att skapa bot.

1.  Ange inställningarna för att skapa roboten:

    |Inställning|Värde|Syfte|
    |--|--|--|
    |Robot referens|`qna-maker-central-us-bot`|Detta är Azure-resursens namn för bot.|
    |Prenumeration|Se syfte.|Välj samma prenumeration som du använde för att skapa QnA Maker-resurserna.|
    |Resursgrupp|`docs`|Den resurs grupp som används för alla robot-relaterade Azure-resurser.|
    |Location|`west us`|Robotens Azure-resurs plats.|
    |Prisnivå|`F0`|Den kostnads fria nivån för Azure bot service.|
    |Appnamn|`qna-maker-central-us-bot-app`|Det här är en webbapp som endast stöder din bot. Detta bör inte vara samma namn som QnA Maker tjänsten redan använder. Delning av QnA Makers webbapp med andra resurser stöds inte.|
    |SDK-språk|C#|Detta är det underliggande programmeringsspråk som används av robot Framework SDK. Dina val är [C#](https://github.com/Microsoft/botbuilder-dotnet) eller [Node. js](https://github.com/Microsoft/botbuilder-js).|
    |QnA auth-nyckel|**Ändra inte**|Det här värdet fylls i åt dig.|
    |App Service-plan/plats|**Ändra inte**|I den här självstudien är platsen inte viktig.|
    |Application Insights|**Ändra inte**|Loggningen skickas till Application Insights.|
    |Microsoft app-ID|**Ändra inte**|Active Directory-användare och lösen ord måste anges.|

    > [!div class="mx-imgBorder"]
    > ![skapa kunskaps bas roboten med de här inställningarna.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Vänta några minuter tills aviserings rapporten för att skapa aviseringar lyckades.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Chatta med roboten

1. Öppna den nya bot-resursen från meddelandet i Azure Portal.

    ![Öppna den nya bot-resursen från meddelandet i Azure Portal.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Från **bot-hantering**väljer du **test i Web Chat** och anger: `How large can my KB be?`. Roboten kommer att svara med:


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Testa den nya kunskaps bas roboten.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Mer information om Azure robotar finns i [använda QNA Maker för att besvara frågor](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien bot kan du ta bort roboten i Azure-portalen.

Om du har skapat en ny resurs grupp för robotens resurser tar du bort resurs gruppen.

Om du inte har skapat någon ny resurs grupp måste du hitta de resurser som är kopplade till bot. Det enklaste sättet är att söka efter namnet på robot-och robot-appen. Robot resurser innehåller:

* App Service-planen
* Söktjänsten
* Cognitive service
* App service
* Du kan också kan även innehålla application insights-tjänsten och lagring för application insights-data


## <a name="related-to-qna-maker-bots"></a>Relaterad till QnA Maker robotar

* [Sjukvårds robotar](https://docs.microsoft.com/HealthBot/qna_model_howto) använder QNA Maker som en av deras [språk modeller](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Koncept: kunskapsbas](../concepts/knowledge-base.md)

## <a name="see-also"></a>Se också

- [Hantera din kunskapsbas](https://qnamaker.ai)
- [Aktivera din robot i olika kanaler](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
