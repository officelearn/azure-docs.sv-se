---
title: Så här distribuerar du en Konversationsdeltagare robot - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du distribuerar en Konversationsdeltagare robot.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bc04afead8742c9f384287ecb8d33c54770456b6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447395"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Så här distribuerar du en robot som Konversationsdeltagare

Det här dokumentet beskriver hur du distribuerar en Konversationsdeltagare robot – antingen lokalt eller i Azure.

## <a name="prerequisite-determine-the-model-id"></a>Förutsättning: ta reda på modell-ID 

Om du vill köra en robot utanför konversationen Learner Användargränssnittet, måste du ange Konversationsdeltagare modell-ID som roboten använder – t.ex, ID för machine learning-modell i molnet Konversationsdeltagare.  (Däremot när du kör roboten via Användargränssnittet för konversationen Learner Användargränssnittet väljer vilken modell-ID.).  

Här är hur du skaffar modell-ID:

1. Starta din robot och konversationen Learner Användargränssnittet.  Se snabbstartsguiden fullständiga instruktioner; Sammanfattningsvis:

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

2. Öppna webbläsaren till http://localhost:5050 

3. Klicka på Konversationsdeltagare-modell som du vill hämta ID för

4. Klicka på ”inställningar” i navigeringsfältet till vänster.

5. ”Modell-ID”-GUID visas längst upp på sidan.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Alternativ 1: Distribuera en Konversationsdeltagare robotar kan köra lokalt

Detta distribuerar en robot till den lokala datorn och visar hur du kommer åt den med hjälp av Bot Framework-emulatorn.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurera din robot för åtkomst utanför konversationen Learner Användargränssnittet

När du kör en robot lokalt kan du lägga till program-ID till robotens `.env` fil:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Starta din robot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Roboten nu körs lokalt.  Du kan komma åt den med emulatorn Bot Framework.

### <a name="download-and-install-the-emulator"></a>Ladda ned och installerar sedan emulatorn

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Ansluta emulatorn till din robot

1. I det övre vänstra hörnet av emulatorn i rutan ”Ange slutpunkts-URL”, ange `http://127.0.0.1:3978/api/messages`.  Lämna övriga fält tomma, och klicka på ”Anslut”.

2. Du nu konversation med din robot.

## <a name="option-2-deploy-to-azure"></a>Alternativ 2: Distribuera till Azure

Publicera din liknar på samma sätt som du skulle publicera andra bot Konversationsdeltagare robot. På hög nivå, ladda upp din kod till en värdbaserad webbplats, anger du lämplig konfigurationsvärden och sedan registrera roboten med olika kanaler. Detaljerade anvisningar finns i den här videon visar hur du publicerar din bot med Azure Bot Service.

När roboten har distribuerats och kör du kan ansluta olika kanaler till den, till exempel Facebook, Teams, Skype osv. med hjälp av en registrering med Azure Bot-kanal. Dokumentation om den går du till avsnittet: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Nedan finns stegvisa instruktioner för att distribuera en konversation Learner robot till Azure.  Dessa instruktioner förutsätter att din bot-källan är tillgänglig från en molnbaserad källa, till exempel Azure DevOps Services, GitHub, BitBucket eller OneDrive och konfigurerar din robot för kontinuerlig distribution.

1. Logga in på Azure portal på https://portal.azure.com

2. Skapa en ny ”Web App-robot”-resurs 

    1. Namnge roboten
    2. Klicka på ”Bot mall”, väljer du ”Node.js”, välj ”grundläggande” och klicka på knappen ”Välj”
    3. Klicka på ”Skapa” för att skapa Web App-robot.
    4. Vänta tills Web App-robot-resurs som ska skapas.

3. Redigera Web App-robot-resurs som du skapade i Azure-portalen.

    1. Klicka på ”programinställningar” nav objekt till vänster
    1. Rulla ned till avsnittet ”Appinställningar”
    2. Lägg till de här inställningarna:

        Miljövariabel | värde
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
        CONVERSATION_LEARNER_MODEL_ID      | Program-Id-GUID som hämtas från konversationen Learner Användargränssnittet under ”inställningar” för modellen >
        LUIS_AUTHORING_KEY               | Redigera nyckel för den här modellen LUIS
        LUIS_SUBSCRIPTION_KEY            | Inte obligatoriskt, men rekommenderas för publicerade robotar att undvika att använda din kvot för redigering.
    
    4. Klicka på ”Spara” längst upp på sidan
    5. Öppna ”Build” nav-objekt till vänster
    6. Klicka på ”Konfigurera kontinuerlig distribution” 
    7. Klicka på ikonen ”inställningar” under distributioner
    8. Klicka på ”nödvändiga inställningar”
    9. Välj källa där din bot-kod är tillgänglig och konfigurera källan.
