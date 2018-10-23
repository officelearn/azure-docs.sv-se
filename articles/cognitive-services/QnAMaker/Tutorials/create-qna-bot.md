---
title: QnA-roboten med Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
description: Den här självstudien vägleder dig genom att skapa en QnA-roboten med Azure Bot service v3 på Azure portal.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 30400b04ec08d936242b022f10cf1485e009e6d2
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647331"
---
# <a name="create-a-qna-bot-with-azure-bot-service-v3"></a>Skapa en QnA-roboten med Azure Bot Service v3
Den här självstudien vägleder dig genom att skapa en QnA-roboten med Azure Bot service v3 på Azure portal.

## <a name="prerequisite"></a>Krav
Innan du skapar, följer du stegen i [skapa en kunskapsbas](../How-To/create-knowledge-base.md) att skapa en QnA Maker-tjänsten med frågor och svar.

Roboten svarar på frågor från kunskapsbas som du skapade via QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Skapa en QnA-roboten
1. I den [Azure-portalen](https://portal.azure.com)väljer **skapa** ny resurs i menyn bladet och välj sedan **se alla**.

    ![Skapa för bot-tjänster](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. I sökrutan söker du efter **Web App-robot**.

    ![val för bot-tjänst](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. I den **Bot Service bladet**, ange nödvändig information:

    - Ange **appnamn** till din robot namn. Namnet används som underdomänen när din robot distribueras till molnet (exempelvis mynotesbot.azurewebsites.net).
    - Välj prenumeration, resursgrupp, App service-plan och plats.

4. Att se instruktionerna för att skapa en QnA-roboten med SDK v4 - Se [QnA v4 bot mallen](https://aka.ms/qna-bot-v4). Välj för att använda v3-mallar, SDK-version i **SDK v3** och språk du SDK **C#** eller **Node.js**.

    ![inställningar för bot-sdk](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Välj den **frågor och svar** mall för fältet Bot mall och sedan spara ändringarna genom att välja mallen **Välj**.

    ![val för bot-tjänst](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Granska dina inställningar och välj sedan **skapa**. Detta skapar och distribuerar bot-tjänst med QnAMakerDialog till Azure.

    ![val för bot-tjänst](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Bekräfta att bot-tjänsten har distribuerats.

    - Välj **meddelanden** (klockikonen finns längs överkanten av Azure portal). Meddelandet kommer att ändras från **distributionen har påbörjats** till **distributionen lyckades**.
    - När ändras meddelandet och **distributionen lyckades**väljer **gå till resurs** på som meddelanden.

## <a name="chat-with-the-bot"></a>Chatta med roboten
Att välja **gå till resurs** tar dig till robotens resursbladet.

När roboten är registrerad klickar du på **Test i Web Chat** att öppna fönstret Web Chat. Skriv ”hello” i Web Chat.

![Frågor och svar om bot webbchatt](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Roboten svarar med ”ange QnAKnowledgebaseId och QnASubscriptionKey i Appinställningar. Lär dig hur du hämtar dem på https://aka.ms/qnaabssetup”. Det här svaret bekräftar att din QnA-roboten har tagit emot meddelandet, men det finns inga QnA Maker-kunskapsbas som är associerade med den ännu. Göra det i nästa steg.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Ansluta kunskapsbasen QnA Maker till roboten

1. Öppna **programinställningar** och redigera den **QnAKnowledgebaseId**, **QnAAuthKey**, och **QnAEndpointHostName** fält som ska innehålla värden för QnA Maker kunskapsbasen.

    ![Appinställningar](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Hämta din kunskapsbas-ID och värd-url för slutpunktsnyckeln från inställningsfliken i kunskapsbasen i https://qnamaker.ai.
    - Logga in på [QnA Maker](https://qnamaker.ai)
    - Gå till din kunskapsbas
    - Klicka på den **inställningar** fliken
    - **Publicera** kunskapsbasen, om inte redan gjort det.

    ![QnA Maker-värden](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Om du vill ansluta förhandsversionen av kunskapsbasen med QnA-roboten, ange värdet för **Ocp-Apim-Subscription-Key** till **QnAAuthKey**. Lämna den **QnAEndpointHostName** tom.

## <a name="test-the-bot"></a>Testa roboten
I Azure-portalen klickar du på **testa i Web Chat** att testa roboten. 

![QnA Maker bot](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

QnA-roboten svar nu från knowledge base.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Integrera QnA Maker och LUIS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Se också

- [Hantera din kunskapsbas](https://qnamaker.ai)
- [Aktivera din robot i olika kanaler](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
