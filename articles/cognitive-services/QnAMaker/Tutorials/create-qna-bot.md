---
title: QnA-roboten – Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
description: Den här självstudien vägleder dig genom att skapa en QnA-roboten med Azure Bot service v3 på Azure portal.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: qna-maker`
ms.topic: article
ms.date: 10/25/2018
ms.author: tulasim
ms.openlocfilehash: f5587e14a0250e7312f1c95598b481bd052931c3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094839"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>Självstudier: Skapa en QnA-roboten med Azure Bot Service v3

Den här självstudien vägleder dig genom att skapa en QnA-roboten med Azure Bot service v3 i den [Azure-portalen](https://portal.azure.com) utan att behöva skriva någon kod. Ansluta en publicerade knowledge base (KB) till en robot som är lika enkelt som att ändra inställningar för bot-program. 

> [!Note] 
> Det här avsnittet avser version 3 av Bot-SDK. Du kan hitta version 4 [här](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs). 

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa en Azure Bot Service med QnA Maker-mall
> * Chatta med roboten att kontrollera koden fungerar 
> * Ansluta din publicerade KB till roboten
> * Testa bot med en fråga

I den här artikeln kan du använda kostnadsfria QnA Maker [service](../how-to/set-up-qnamaker-service-azure.md).

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en publicerad kunskapsbas för den här självstudiekursen. Om du inte har någon, följer du stegen i [skapa en kunskapsbas](../How-To/create-knowledge-base.md) att skapa en QnA Maker-tjänsten med frågor och svar.

## <a name="create-a-qna-bot"></a>Skapa en QnA-roboten

1. I Azure-portalen väljer du **Skapa en resurs**.

    ![Skapa för bot-tjänster](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. I sökrutan söker du efter **Web App-robot**.

    ![val för bot-tjänst](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. Ange nödvändig information i **Robottjänst**:

    - Ange **appnamn** till din robot namn. Namnet används som underdomänen när din robot distribueras till molnet (exempelvis mynotesbot.azurewebsites.net).
    - Välj prenumeration, resursgrupp, App service-plan och plats.

4. Välj för att använda v3-mallar, SDK-version i **SDK v3** och språk du SDK **C#** eller **Node.js**.

    ![inställningar för bot-sdk](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Välj den **frågor och svar** mall för fältet Bot mall och sedan spara ändringarna genom att välja mallen **Välj**.

    ![Spara bot service Mallval](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Granska dina inställningar och välj sedan **skapa**. Detta skapar och distribuerar bot-tjänst med till Azure.

    ![Skapa robot](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Bekräfta att bot-tjänsten har distribuerats.

    - Välj **meddelanden** (klockikonen finns längs överkanten av Azure portal). Meddelandet kommer att ändras från **distributionen har påbörjats** till **distributionen lyckades**.
    - När ändras meddelandet och **distributionen lyckades**väljer **gå till resurs** på som meddelanden.

## <a name="chat-with-the-bot"></a>Chatta med roboten

Att välja **gå till resurs** tar dig till robotens resurs.

Välj **Test i Web Chat** att öppna fönstret Web Chat. Skriv ”Hej” Web Chat.

![Frågor och svar om bot webbchatt](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Roboten svarar med ”ange QnAKnowledgebaseId och QnASubscriptionKey i Appinställningar. Det här svaret bekräftar att din QnA-roboten har tagit emot meddelandet, men det finns inga QnA Maker-kunskapsbas som är associerade med den ännu. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Ansluta kunskapsbasen QnA Maker till roboten

1. Öppna **programinställningar** och redigera den **QnAKnowledgebaseId**, **QnAAuthKey**, och **QnAEndpointHostName** fält som ska innehålla värden för QnA Maker kunskapsbasen.

    ![Appinställningar](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. Få din kunskapsbas-ID och värd-url för slutpunktsnyckeln från inställningsfliken i kunskapsbasen i QnA Maker-portalen.

    - Logga in på [QnA Maker](https://qnamaker.ai)
    - Gå till din kunskapsbas
    - Välj den **inställningar** fliken
    - **Publicera** kunskapsbasen, om inte redan gjort det.

    ![QnA Maker-värden](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Om du vill ansluta förhandsversionen av kunskapsbasen med QnA-roboten, ange värdet för **Ocp-Apim-Subscription-Key** till **QnAAuthKey**. Lämna den **QnAEndpointHostName** tom.

## <a name="test-the-bot"></a>Testa roboten

I Azure-portalen väljer du **testa i Web Chat** att testa roboten. 

![QnA Maker bot](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

QnA-roboten svar från knowledge base.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien bot kan du ta bort roboten i Azure-portalen. Bot-tjänsterna omfattar:

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
