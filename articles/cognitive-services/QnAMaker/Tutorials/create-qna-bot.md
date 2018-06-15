---
title: Skapa frågor och svar om bot med Azure Bot Service - kognitiva Azure-tjänster | Microsoft Docs
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: f2721dfa9a0922ee4a5af1eb66fd4506feb94d28
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355920"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Skapa frågor och svar om Bot med Azure Bot Service
Den här självstudiekursen vägleder dig genom att skapa frågor och svar om bot med tjänsten Azure Bot på Azure-portalen.

## <a name="prerequisite"></a>Krav
Innan du skapar, följer du stegen i [skapa en kunskapsbas]() att skapa en tjänst för frågor och svar om Maker med frågor och svar.

Bot svarar på frågor från i knowledge base om du har skapat, via QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Skapa frågor och svar om Bot
1. I den [Azure-portalen](https://portal.azure.com)väljer **skapa** ny resurs i menyn bladet och välj sedan **se alla**.

    ![Skapa en bot tjänst](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. I sökrutan söker du efter **Web App Bot**.

    ![val av bot service](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. I den **Bot Service bladet**, ange nödvändig information och välj **skapa**. Detta skapar och distribuerar bot tjänsten med QnAMakerDialog till Azure.

    - Ange **appnamn** till din bot namn. Namnet används som underdomänen när din bot distribueras till molnet (exempelvis mynotesbot.azurewebsites.net).
    - Välj den prenumeration, resursgrupp, apptjänstplan och plats.
    - Välj den **frågor och svar** (Node.js- eller C#) mall för fältet Bot mall.
    - Markera kryssrutan bekräftelse för juridiskt meddelande. Villkoren i juridiskt meddelande är lägre än kryssrutan.

        ![val av bot service](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Bekräfta att tjänsten bot har distribuerats.

    - Välj **meddelanden** (klockikonen finns längs överkanten av Azure portal). Meddelandet kommer att ändras från **distributionen har startat** till **distributionen lyckades**.
    - När meddelandet ändras till **distributionen lyckades**väljer **finns resurs** på detta meddelande.

## <a name="chat-with-the-bot"></a>Chatta med Bot
Att välja **finns resurs** går du till den bot resursbladet.

När bot är registrerad klickar du på **Test i Web chatta** att öppna fönstret Web chatta. Skriv ”hello” i Web chatta.

![Frågor och svar om bot webbchatt](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Bot svarar med ”Ställ in QnAKnowledgebaseId och QnASubscriptionKey i App-inställningar. Lär dig hur du hämtar dem på https://aka.ms/qnaabssetup”. Det här svaret bekräftar att din frågor och svar om Bot har tagit emot meddelandet, men det finns inga frågor och svar om Maker kunskapsbas associerad ännu. Göra det i nästa steg.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Ansluta kunskapsbasen frågor och svar om Maker till bot

1. Öppna **programinställningar** och redigera den **QnAKnowledgebaseId**, **QnAAuthKey**, och **QnAEndpointHostName** fält som ska innehålla värden för frågor och svar om Maker kunskapsbasen.

    ![App-inställningar](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Hämta knowledge base-ID och värd-url för nyckeln endpoint från fliken Inställningar i kunskapsbasen i https://qnamaker.ai.
    - Logga in på [frågor och svar om Maker](https://qnamaker.ai)
    - Gå till kunskapsbasen
    - Klicka på den **inställningar** fliken
    - **Publicera** kunskapsbas, om inte redan gjort det.

    ![Frågor och svar om Maker värden](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Om du vill ansluta förhandsversionen av kunskapsbasen med frågor och svar om bot värdet för **Ocp-Apim-prenumeration-nyckeln** till **QnAAuthKey**. Lämna den **QnAEndpointHostName** tom.

## <a name="test-the-bot"></a>Testa bot
I Azure-portalen klickar du på **testa i Web chatta** att testa bot. 

![Frågor och svar om Maker bot](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Dina frågor och svar om Bot nu svar från knowledge base.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Integrera frågor och svar om Maker och THOMAS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Se också

- [Hantera kunskapsbasen](https://qnamaker.ai)
- [Aktivera din bot i olika kanaler](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
