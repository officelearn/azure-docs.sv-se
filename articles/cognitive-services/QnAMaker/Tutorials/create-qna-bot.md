---
title: QnA-roboten – Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
description: Skapa en chattrobot för frågor och svar från sidan Publicera för en befintlig kunskapsbasen. Den här bot använder Bot Framework SDK-v4. Du inte behöver skriva någon kod för att skapa roboten tillhandahålls all kod åt dig.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.openlocfilehash: 85b0004288a06a834b61f6e3d50017d35d66ce86
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59263884"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Självstudier: Skapa en QnA-roboten med Azure Bot Service v4

Skapa en chattrobot för frågor och svar från den **publicera** för en befintlig kunskapsbas. Den här bot använder Bot Framework SDK-v4. Du inte behöver skriva någon kod för att skapa roboten tillhandahålls all kod åt dig.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa en Azure Bot Service från en befintlig kunskapsbas
> * Chatta med roboten att kontrollera koden fungerar 

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste ha en publicerad kunskapsbas för den här självstudiekursen. Om du inte har någon, följer du stegen i [skapa och svaret från KB](create-publish-query-in-portal.md) självstudie om du vill skapa en QnA Maker kunskapsbas med frågor och svar.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Skapa en QnA-roboten

Skapa en robot som ett klientprogram för kunskapsbasen. 

1. I QnA Maker-portalen går du till den **publicera** sidan och publicera din kunskapsbas. Välj **skapa robot**. 

    ![Gå till sidan Publicera i QnA Maker-portalen och publicera din kunskapsbas. Välj Skapa Bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Azure-portalen öppnas med bot skapa konfigurationen.

1.  Ange inställningar för att skapa roboten:

    |Inställning|Värde|Syfte|
    |--|--|--|
    |Robotnamn|`my-tutorial-kb-bot`|Det här är Azure-resursnamn för roboten.|
    |Prenumeration|Se syfte.|Välj samma prenumeration som du använde för att skapa QnA Maker-resurser.|
    |Resursgrupp|`my-tutorial-rg`|Den resursgrupp som används för alla bot-relaterade Azure-resurser.|
    |Plats|`west us`|Plats för robotens Azure-resurs.|
    |Prisnivå|`F0`|Den kostnadsfria nivån för Azure bot service.|
    |Appnamn|`my-tutorial-kb-bot-app`|Det här är en webbapp med stöd för din robot endast. Det får inte vara samma appnamn eftersom QnA Maker-tjänsten redan använder. Dela QnA Maker-webbapp med någon annan resurs stöds inte.|
    |SDK-språk|C#|Det här är det underliggande programmeringsspråket som används av bot framework SDK. Du kan välja mellan C# eller Node.js.|
    |Frågor och svar om Auth-nyckel|**Ändra inte**|Det här värdet är ifyllt åt dig.|
    |App Service-plan/plats|**Ändra inte**|Platsen är inte viktigt för den här självstudien.|
    |Azure Storage|**Ändra inte**|Konversationen data lagras i Azure Storage-tabeller.|
    |Application Insights|**Ändra inte**|Loggning skickas till Application Insights.|
    |Microsoft App-ID|**Ändra inte**|Active directory-användare och lösenord krävs.|

    ![Skapa kunskapsbas bot med de här inställningarna.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Vänta några minuter tills bot skapa processen meddelandet rapporterar att processen lyckades.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Chatta med roboten

1. Öppna den nya bot-resursen från meddelandet i Azure-portalen. 

    ![Öppna den nya bot-resursen från meddelandet i Azure-portalen.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Från **Bot management**väljer **Test i Web Chat** och ange: `How large can my KB be?`. Roboten svarar med: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Testa den nya kunskapsbas-roboten.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Läs mer om Azure robotar [Använd QnA Maker att besvara frågor](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>QnA Maker robotar som rör

* QnA Maker hjälp bot används i QnA Maker-portalen är tillgänglig som en [bot exempel](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![QnA Maker hjälpikonen bot är röda robot](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Hälsovård robotar](https://docs.microsoft.com/HealthBot/qna_model_howto) Använd QnA Maker som en av sina [språkmodeller](https://docs.microsoft.com/HealthBot/qna_model_howto).

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien bot kan du ta bort roboten i Azure-portalen. 

Om du har skapat en ny resursgrupp för robotens resurser tar du bort resursgruppen. 

Om du inte har skapat en ny resursgrupp måste att hitta de resurser som är associerade med roboten. Det enklaste sättet är att söka efter namnet på bot och robotapp. Bot-resurserna innehåller:

* App Service-planen
* Söktjänsten
* Cognitive service
* App service
* Du kan också kan även innehålla application insights-tjänsten och lagring för application insights-data

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Koncept: kunskapsbas](../concepts/knowledge-base.md)

## <a name="see-also"></a>Se också

- [Hantera din kunskapsbas](https://qnamaker.ai)
- [Aktivera din robot i olika kanaler](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
