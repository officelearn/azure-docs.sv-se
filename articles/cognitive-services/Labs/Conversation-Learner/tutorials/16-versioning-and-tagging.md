---
title: Hur du använder versionshantering och märkning med ett samtal deltagaren program - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder versionshantering och märkning med ett samtal deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ea013db078ff33f8597b0e15a8fc951e8ae320e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354042"
---
# <a name="how-to-use-versioning-and-tagging"></a>Hur du använder versionshantering och taggning

Den här kursen visar hur du kan tagga versioner av appen konversation deltagaren och ange vilken version som är ”live”.  

## <a name="requirements"></a>Krav
Den här kursen krävs med hjälp av bot-emulatorn för att skapa loggen dialogrutor, inte loggen dialogrutan Webbgränssnittet.  

Den här kursen kräver att den allmänna självstudiekursen bot körs:

    npm run tutorial-general

## <a name="details"></a>Information

När du redigerar kan du redigerar alltid taggen ”överordnad” – du kan skapa taggade versioner från master (som i stort sett ta en ögonblicksbild av master), men du kan inte redigera taggade versioner.

## <a name="steps"></a>Steg

### <a name="install-the-bot-framework-emulator"></a>Installera Bot framework emulatorn

- Gå till [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Ladda ned och installerar emulatorn.

### <a name="create-an-app"></a>Skapa en app

1. Klicka på ny App
2. Ange i fältet Namn på kursen-16-Versioning
3. Klicka på Skapa 
4. Klicka på inställningar
5. Kopiera App-ID

### <a name="configure-the-emulator"></a>Konfigurera emulatorn

- Öppna filen .env i rotmappen konversation deltagaren.
- Klistra in det App-ID som värde för CONVERSATION_LEARNER_APP_ID
- Starta om tjänsten konversation deltagaren genom att avsluta från Kommandotolken och kör igen:
 
    npm kör kursen-Allmänt 

### <a name="actions"></a>Åtgärder

Nu ska vi skapa en åtgärd:

1. Växla till webbgränssnittet.
1. Klicka på Åtgärder sedan ny åtgärd.
2. Ange i svaret ”, Hej där (version 1)”.
3. Klicka på Spara.


![](../media/tutorial16_action1.PNG)

Skapa en ny tagg:

3. Klicka på ”inställningar” och skapa en ny ”tagg”.
    - Anropa den ”version 1”
4. Ange ”version 1” ska vara ”live”.  
    - Effekten av att ställa in taggen live till ”version 1” är att kanaler som använder den här bot kommer att använda den ”version 1” taggen.
    - Taggade versioner av program påverkas inte av ändringar (ändra åtgärder, enheter, lägga till train dialogrutor).  
    - Redigering av ett program (ändra åtgärder, enheter, lägga till train dialogrutor) görs alltid för taggen ”överordnad”.  Med andra ord är ”överordnad” den enda tagg som kan ändras. andra taggar är fasta ögonblicksbilder.
    - Logga dialogrutor i Användargränssnittet för konversationen deltagaren alltid använda master (inte live taggen).

![](../media/tutorial16_v1_create.PNG)

Observera att den har skapats i inställningarna för:

![](../media/tutorial16_settings.PNG)

Lägg till en andra åtgärd:

1. Klicka på Åtgärder sedan ny åtgärd.
2. Ange svar ”bye bye (version 2)”.

Redigera första åtgärd:

1. Klicka på åtgärder.
2. Under åtgärder, klickar du på ”Hej där (version 1)”.
3. Ändra svaret ”Hej där (version 2)”.

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Växla till bot emulatorn

1. Ange ”goodbye” i bot Användargränssnittet.
2. Obs bot svarar med ”Hej där (version 1)”.
    - Detta visar version 1 är ”live”. 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Växla till webbgränssnittet

1. Klicka på Logga dialogrutor (om du inte ser alla dialogrutor uppdatera appen).
2. Klicka på ”Hej där (version 2)”

Observera att vi kan göra korrigeringar att välja från alla tillgängliga åtgärder. Dessa ändringar sker i bakgrunden.

Nu har du sett hur versionshantering fungerar och hur du kan interagera med bot med hjälp av Bot framework-emulatorn.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Demo - återställning av lösenord](./demo-password-reset.md)
