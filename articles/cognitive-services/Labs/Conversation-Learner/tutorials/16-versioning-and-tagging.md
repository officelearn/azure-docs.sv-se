---
title: Hur du använder versionshantering och taggning med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder versionshantering och taggning med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c7f23d989cbfa0ece9e404a0fe0feb68cf5fddb2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170553"
---
# <a name="how-to-use-versioning-and-tagging"></a>Hur du använder versionshantering och taggning

Den här självstudien visar hur du kan tagga versioner av din Konversationsdeltagare modell och ange vilken version som är ”live”.  

## <a name="requirements"></a>Krav
Den här självstudien kräver bot-emulatorn för att skapa log dialogrutor, inte Log dialogrutan Webbgränssnittet.  

Den här självstudien krävs att Allmänt självstudiekursen bot körs:

    npm run tutorial-general

## <a name="details"></a>Information

När du redigerar, redigerar du alltid taggen som kallas ”huvud” – du kan skapa taggade versioner från master (vilket i princip ta en ögonblicksbild av master), men du kan inte redigera taggade versioner.

## <a name="steps"></a>Steg

### <a name="install-the-bot-framework-emulator"></a>Installera Bot framework-emulatorn

- Gå till [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Ladda ned och installerar sedan emulatorn.

### <a name="create-an-model"></a>Skapa en modell

1. Klicka på ny modell
2. Ange i fältet Namn på självstudien-16-versionshantering
3. Klicka på Skapa 
4. Klicka på inställningar
5. Kopiera modell-ID

### <a name="configure-the-emulator"></a>Konfigurera emulatorn

- Öppna filen .env i rotmappen Konversationsdeltagare.
- Klistra in modell-ID som värde för CONVERSATION_LEARNER_MODEL_ID
- Starta om tjänsten Konversationsdeltagare genom att avsluta från Kommandotolken och kör igen:
 
    npm kör självstudien-Allmänt 

### <a name="actions"></a>Åtgärder

Nu ska vi skapa en åtgärd:

1. Växla till webbgränssnittet.
1. Klicka på åtgärder och ny åtgärd.
2. Ange i svaret ”, Hej där (version 1)”.
3. Klicka på Spara.


![](../media/tutorial16_action1.PNG)

Skapa en ny tagg:

3. Klicka på ”inställningar” och skapa en ny ”tagg”.
    - Den kallas ”version 1”
4. Ange ”version 1” ska vara ”live”.  
    - Effekten av att ställa in taggen live för ”version 1” är att med hjälp av den här bot channels kommer att använda den ”version 1 – taggen.
    - Taggade versioner av modeller påverkas inte av ändringar (ändra åtgärder, entiteter, att lägga till träna dialogrutor).  
    - Redigering av en modell (ändra åtgärder, entiteter, att lägga till träna dialogrutor) görs alltid på ”huvud”-taggen.  D.v.s. är ”huvud” den enda tagg som kan ändras. andra taggar är fasta ögonblicksbilder.
    - Logga dialogrutor i Användargränssnittet för konversationen Learner alltid använda master (inte live taggen).

![](../media/tutorial16_v1_create.PNG)

Versionen har skapats i inställningarna för:

![](../media/tutorial16_settings.PNG)

Lägg till en andra åtgärd:

1. Klicka på åtgärder och ny åtgärd.
2. Ange ”bye bye (version 2)” som svar.

Redigera den första åtgärden:

1. Klicka på åtgärder.
2. Under åtgärder, klickar du på ”Hej där (version 1)”.
3. Ändra svaret till ”Hej där (version 2)”.

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Växla till bot-emulatorn

1. Ange ”goodbye” i bot Användargränssnittet.
2. Roboten svarar med ”Hej där (version 1)”.
    - Detta visar version 1 är ”live”. 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Växla till webbgränssnittet

1. Klicka på logg-dialogrutor (om du inte ser alla dialogrutor, klicka på Uppdatera).
2. Klicka på ”Hej där (version 2)”

> [!NOTE]
> Vi kan göra ändringar genom att välja från alla tillgängliga åtgärder. Dessa ändringar kommer att göras till huvudservern.

Du har nu sett hur versionshantering fungerar och hur du interagerar med robot som använder Bot framework-emulatorn.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Demo – återställning av lösenord](./demo-password-reset.md)
