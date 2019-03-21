---
title: Hur du använder Version taggning med en modell för konversationen Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder versionshantering och taggning med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5073d3ab967c4c4e1b90636c247839875a6aa0d7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086317"
---
# <a name="how-to-use-version-tagging"></a>Hur du använder Version taggning

Den här självstudien visar hur du kan tagga versioner av konversationen Learner modellen och ange vilken version som är ”live”.  

## <a name="requirements"></a>Krav
Den här självstudien kräver med Bot Framework-emulatorn för att skapa Log-dialogrutor, inte Log dialogrutan Webbgränssnittet.  

Den här självstudien krävs att Allmänt självstudien Bot körs:

    npm run tutorial-general

## <a name="details"></a>Information

Taggade versioner av modellen är statiskt. Du kan inte redigera eller ändra dem. När du redigerar din modell kan du alltid redigerar huvudservern version. När du lägger till en ny tagg fångar Konversationsdeltagare en ögonblicksbild av modellen i det här läget i tid. 

Din robot kommer att använda versionen av modellen som du har valt som ”Live”-version, men alla konversationer har kan visas endast när den ”Redigera tagg” anges till ”Huvudgrenen”. Om egenskapen ”Redigera tagg” i modellen är inställd på något annat än ”Master”, kan du visa ögonblicksbilden av modellen, men du kan inte ändra det på något sätt.

## <a name="steps"></a>Steg

### <a name="install-the-bot-framework-emulator"></a>Installera Bot Framework-emulatorn

1. Gå till [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Ladda ned och installerar sedan emulatorn.

### <a name="create-a-model"></a>Skapa en modell

1. Från modellen lista startsidan klickar du på den `New Model` knappen.
2. I den `Name` fälttyp, ”självstudien-18-versionshantering”, tryck på RETUR.
4. Klicka på ”inställningar” på den vänstra panelen.
5. Kopiera innehållet i fältet CONVERSATION_LEARNER_MODEL_ID till Urklipp.

### <a name="configure-the-emulator"></a>Konfigurera emulatorn

1. Öppna filen ”.env” i rotmappen Konversationsdeltagare.
2. Lägg till en rad i filen ”.env” så här:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Starta om tjänsten Konversationsdeltagare genom att avsluta från Kommandotolken och kör igen:
    - `npm run tutorial-general`
4. I Bot Framework-emulatorn, skapa en ny bot-konfiguration genom att ange slutpunkts-URL `http://localhost:3978/api/messages`

### <a name="version-1"></a>Version 1

Vi skapar en enda åtgärd för Version 1.

1. I den vänstra panelen för Webbgränssnittet klickar du på ”åtgärder” och klicka sedan på den `New Action` knappen.
2. I ”Robotens svaret” anger du ”Hej där (version 1)”.
3. Klicka på knappen `Save`.

Nu ska vi tagga detta som ”Version 1” av modellen.

1. Klicka på ”inställningar” i den vänstra panelen och sedan klicka på den ![](../media/tutorial18_version_tags.PNG)”Version taggar”-ikonen för att visa den `New Tag` knappen som du på.
    - Ge den namnet ”Version 1”
1. I ”Live taggen” nedrullningsbar listruta väljer ”Version 1”.  
    - Nu använder kanaler med den här Bot ”Version 1” av vår modell.
    - Entiteter, åtgärder och träna dialogrutor med den här Version 1-modellen kan inte längre ändras.
    - Om du väljer ”Version 1” som ”redigera taggen” kommer endast att kunna se modellen och redigera den inte.
    - Lämna ”Redigera tagg” inställd på ”Master”, är det den enda versionen av den modell som kan redigeras.

Nu visas ”Version 1” i rutnätet ”Version taggar”.

### <a name="version-2"></a>Version 2

Vi kommer nu att redigera vår modell för att skilja den från Version 1.

1. I den vänstra panelen klickar du på ”åtgärder”.
2. I rutnätet åtgärder klickar du på ”Hej där (version 1)”.
3. Ändra ”Robotens response” fältet till ”Hej där (version 2)”.
4. Klicka på knappen `Save`.
5. Klicka på knappen `New Action`.
6. I ”Robotens svaret” fältet Typ, ”bye bye (version 2)”.

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Bekräfta Bot Framework-emulatorn är med hjälp av Version 1

1. Skriv meddelandet ”Hey, det” i Bot Framework-emulatorn.
2. Observera att roboten svarar med ”Hej där (version 1)”.
    - Detta verifierar att Version 1 är ”live”.

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Visa Konversations-loggar i konversationen Learner-Webbgränssnittet

1. I den vänstra panelen klickar du på på ”Log-dialogrutor”
    - Klicka på Uppdatera om du inte ser alla dialogrutor.
2. Observera taggen ”Version 1” i rutnätet.
3. I rutnätet, klickar du på ”Hej där (version 1)”

> [!NOTE]
> Vi kan göra ändringar genom att välja från alla tillgängliga Konversationsdeltagare-funktioner, men dessa ändringar kommer att göras till master-databasen och inte till Version 1.

Du har nu sett hur versionshantering fungerar och hur du interagerar med robot som använder Bot Framework-emulatorn.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Demo – återställning av lösenord](./demo-password-reset.md)
