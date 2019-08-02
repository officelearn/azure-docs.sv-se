---
title: Så här distribuerar du en Conversation Learner bot-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du distribuerar en Conversation Learner bot.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 05fd83506aac26df33f18bec83dcadac8dee2d90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705272"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Så här distribuerar du en Conversation Learner bot

Det här dokumentet beskriver hur du distribuerar en Conversation Learner robot – antingen lokalt eller i Azure.

## <a name="prerequisite-determine-the-model-id"></a>Förkrav: Bestäm modell-ID 

Om du vill köra en robot utanför Conversation Learner användar gränssnittet, måste du ange Conversation Learner modell-ID: t som roboten ska använda – d.v.s. ID: t för Machine Learning-modellen i Conversation Learner molnet.  (Om du däremot kör roboten genom Conversation Learner användar gränssnitt väljer användar gränssnittet vilket modell-ID.).  

Så här hämtar du modell-ID:

1. Starta din robot och Conversation Learner användar gränssnitt.  Fullständiga instruktioner finns i snabb starts guiden. att sammanfatta:

    I ett kommando fönster:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    I anther kommando fönster

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Öppna webbläsare för att`http://localhost:5050` 

3. Klicka på den Conversation Learner modell som du vill hämta ID för

4. Klicka på "Inställningar" i navigerings fältet till vänster.

5. GUID för "modell-ID" visas längst upp på sidan.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Alternativ 1: Distribuera en Conversation Learner robot för att köras lokalt

Detta distribuerar en robot till den lokala datorn och visar hur du kan komma åt den med bot Framework-emulatorn.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurera din robot för åtkomst utanför Conversation Learner användar gränssnitt

När du kör en robot lokalt lägger du till program-ID: t `.env` i robotens fil:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Starta sedan din robot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Roboten körs nu lokalt.  Du kan komma åt den med bot Framework-emulatorn.

### <a name="download-and-install-the-emulator"></a>Hämta och installera emulatorn

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Anslut emulatorn till din robot

1. I det övre vänstra hörnet av emulatorn anger `http://127.0.0.1:3978/api/messages`du i rutan "Ange din slut punkts-URL".  Lämna de andra fälten tomma och klicka på "Anslut".

2. Du är nu konversation med din robot.

## <a name="option-2-deploy-to-azure"></a>Alternativ 2: Distribuera till Azure

Publicera din Conversation Learner robot på samma sätt som du publicerar andra bot. På hög nivå laddar du upp din kod till en värd webbplats, anger lämpliga konfigurations värden och registrerar sedan roboten med olika kanaler. Detaljerade instruktioner finns i den här videon som visar hur du publicerar din robot med hjälp av Azure Bot Service.

När roboten har distribuerats och körs kan du ansluta olika kanaler till den som Facebook, teams, Skype osv. använda en Azure robot Channel-registrering. Dokumentation om den här processen finns i: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Nedan visas steg för steg hur du distribuerar en Conversation Learner robot till Azure.  Dessa instruktioner förutsätter att din robot källa är tillgänglig från en molnbaserad källa, till exempel Azure DevOps Services, GitHub, BitBucket eller OneDrive, och kommer att konfigurera din robot för kontinuerlig distribution.

1. Logga in på Azure Portal på https://portal.azure.com

2. Skapa en ny resurs för "Web App bot" 

    1. Ge roboten ett namn
    2. Klicka på "robot mall", välj "Node. js", välj "grundläggande" och klicka sedan på knappen "Välj"
    3. Klicka på "skapa" för att skapa Web App bot.
    4. Vänta tills webb Programens bot-resurs har skapats.

3. I Azure Portal redigerar du den webb programs bot-resurs som du nyss skapade.

   1. Klicka på navigerings objektet "program inställningar" till vänster
   1. Rulla ned till avsnittet appinställningar
   2. Lägg till följande inställningar:

       Miljövariabel | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | GUID för program-ID, hämtas från Conversation Learner-ANVÄNDARGRÄNSSNITTET under "Inställningar" för modellen >
       LUIS_AUTHORING_KEY               | LUIS redigerings nyckel för den här modellen
       LUIS_SUBSCRIPTION_KEY            | Krävs inte, men rekommenderas för publicerade robotar för att undvika att använda redigerings kvoten.
    
   4. Klicka på "Spara" nästan längst upp på sidan
   5. Öppna navigerings objektet "Build" till vänster
   6. Klicka på Konfigurera kontinuerlig distribution 
   7. Klicka på ikonen "installations" under distributioner
   8. Klicka på "obligatoriska inställningar"
   9. Välj källan där din robot kod är tillgänglig och konfigurera källan.
