---
title: QnA-roboten med Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: nstulasi
ms.openlocfilehash: 0c6feb67b273ec30afba89f2d4d9b59a8a9f5acf
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731424"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Skapa en QnA-roboten med Azure Bot Service
Den här självstudien vägleder dig genom att skapa en QnA-roboten med Azure Bot service i Azure-portalen.

## <a name="prerequisite"></a>Krav
Innan du skapar, följer du stegen i [skapa en kunskapsbas](../How-To/create-knowledge-base.md) att skapa en QnA Maker-tjänsten med frågor och svar.

Roboten svarar på frågor från kunskapsbas som du skapade via QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Skapa en QnA-roboten
1. I den [Azure-portalen](https://portal.azure.com)väljer **skapa** ny resurs i menyn bladet och välj sedan **se alla**.

    ![Skapa för bot-tjänster](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. I sökrutan söker du efter **Web App-robot**.

    ![val för bot-tjänst](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. I den **Bot Service bladet**, ange nödvändig information och välj **skapa**. Detta skapar och distribuerar bot-tjänst med QnAMakerDialog till Azure.

    - Ange **appnamn** till din robot namn. Namnet används som underdomänen när din robot distribueras till molnet (exempelvis mynotesbot.azurewebsites.net).
    - Välj prenumeration, resursgrupp, App service-plan och plats.
    - Välj den **frågor och svar** (Node.js eller C#)-mall för fältet Bot mall.
    - Markera kryssrutan bekräftelse för juridiskt meddelande. Villkoren i juridiskt meddelande är lägre än kryssrutan.

        ![val för bot-tjänst](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Bekräfta att bot-tjänsten har distribuerats.

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
