---
title: Så här distribuerar du en konversation deltagaren bot - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du distribuerar en konversation deltagaren bot.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 77cc998227d996a6e52b1b5629204da5dc735ede
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353961"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Så här distribuerar du en konversation deltagaren bot

Det här dokumentet förklarar hur du distribuerar en konversation deltagaren bot--antingen lokalt eller i Azure.

## <a name="prerequisite-determine-the-application-id"></a>Förutsättning: ta reda på program-ID 

Om du vill köra en bot utanför konversation deltagaren Användargränssnittet måste du ange konversation deltagaren program-ID som kommer att använda bot--t.ex. ID för maskininlärningsmodell i konversationen deltagaren molnet.  (Däremot när du kör bot via Användargränssnittet för konversationen deltagaren Användargränssnittet väljer vilka program-ID).  

Här är hur du hämtar det program-ID:

1. Starta din bot och konversation deltagaren Användargränssnittet.  Se Snabbstartsguide fullständiga instruktioner; Sammanfattningsvis:

    I ett kommandofönster:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    I en annan kommandofönster

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Öppna webbläsaren http://localhost:5050 

3. Klicka på konversation deltagaren-program som du vill hämta ID för

4. Klicka på ”inställningar” i navigeringsfältet till vänster.

5. ”App-ID”-GUID visas längst upp på sidan.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Alternativ 1: Distribuera en konversation deltagaren bot ska köras lokalt

Detta distribuerar en bot till din lokala dator och visar hur du kan komma åt den med hjälp av Bot Framework-emulatorn.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurera din bot för åtkomst utanför konversation deltagaren Användargränssnittet

När du kör en bot lokalt, lägga till det program-ID i bot `.env` fil:

    ```
    CONVERSATION_LEARNER_APP_ID=<YOUR_APP_ID>
    ```

Starta din bot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Bot körs nu lokalt.  Du kan komma åt den med emulatorn Bot Framework.

### <a name="download-and-install-the-emulator"></a>Hämtar och installerar emulatorn

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Ansluta emulatorn till din bot

1. I det övre vänstra hörnet av emulatorn, i rutan ”anger slutpunkts-URL”, ange `http://127.0.0.1:3978/api/messages`.  Lämna de andra fälten tomma och klicka på ”Anslut”.

2. Du nu konversation med din bot.

## <a name="option-2-deploy-to-azure"></a>Alternativ 2: Distribuera till Azure

Publicera din konversation deltagaren bot liknar på samma sätt som du skulle publicera andra bot. På en hög nivå, ladda upp din kod till en webbplats som värd, ange värden för lämplig konfiguration och registrera sedan bot med olika kanaler. Detaljerade anvisningar finns i den här videon visar hur du publicerar ditt bot med hjälp av Azure Bot Service.

När bot har distribuerats och kör du kan ansluta olika kanaler till den, till exempel Facebook, grupper, Skype etc med hjälp av en Azure Bot Kanalregistrering. Dokumentation om detta finns i processen: https://docs.microsoft.com/en-us/bot-framework/bot-service-quickstart-registration

Nedan finns stegvisa instruktioner för att distribuera en konversation deltagaren Bot till Azure.  Dessa instruktioner förutsätter att bot-källan är tillgänglig från en molnbaserad källa, till exempel VSTS, GitHub, BitBucket eller OneDrive och konfigurerar din bot för kontinuerlig distribution.

1. Logga in på Azure portal på https://portal.azure.com

2. Skapa en ny resurs ”Web App Bot” 

    1. Namnge bot
    2. Klicka på ”Bot mall”, Välj ”Node.js”, Välj ”Basic” och klicka på knappen ”Välj”
    3. Klicka på ”Skapa” för att skapa Web App Bot.
    4. Vänta tills Web App Bot resursen skapas.

3. Redigera Web App Bot resursen som du skapade i Azure-portalen.

    1. Klicka på ”programinställningar” nav objekt till vänster
    1. Rulla ned till avsnittet ”App-inställningar”
    2. Lägg till de här inställningarna:

        Miljövariabel | värde
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
        CONVERSATION_LEARNER_APP_ID      | Program-Id-GUID som hämtas från konversationen deltagaren Användargränssnittet under ”inställningar” för appen >
        LUIS_AUTHORING_KEY               | Redigera nyckel för den här appen THOMAS
    
    4. Klicka på ”Spara” längst upp på sidan
    5. Öppna ”skapa” nav objekt till vänster
    6. Klicka på ”Konfigurera kontinuerlig distribution” 
    7. Klicka på ikonen ”inställningar” under distributioner
    8. Klicka på ”nödvändiga inställningar”
    9. Välj källa där bot koden är tillgänglig och konfigurera källan.
