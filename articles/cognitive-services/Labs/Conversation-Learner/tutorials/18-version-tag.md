---
title: Så här använder du versions märkning med en Conversation Learner modell – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder versions hantering och taggning med en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 0279363c039e3ec3c2deac3bc7f71c32c547e9d1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703379"
---
# <a name="how-to-use-version-tagging"></a>Så här använder du versions taggning

I den här självstudien beskrivs hur du taggar versioner av din Conversation Learner modell och anger vilken version som är "Live".  

## <a name="requirements"></a>Krav
Den här självstudien kräver att du använder bot Framework-emulatorn för att skapa logg dialog rutor, inte logg dialog rutans webb gränssnitt.  

Den här självstudien kräver att roboten för allmän självstudie körs:

    npm run tutorial-general

## <a name="details"></a>Information

Taggade versioner av modellen är statiska. Du kan inte redigera eller ändra dem. När du redigerar din modell redigerar du alltid huvud versionen. När du lägger till en ny tagg Conversation Learner samla in en ögonblicks bild av modellen vid den tidpunkten. 

Din robot använder den version av modellen som du har valt som "Live"-version, men alla konversationer som den har kan bara visas när "redigerings tag gen" är inställd på "Master". Om egenskapen "Edit tag" för modellen har angetts till något annat än "Master", kan du Visa ögonblicks bilden av modellen, men du kan inte ändra den på något sätt.

## <a name="steps"></a>Steg

### <a name="install-the-bot-framework-emulator"></a>Installera bot Framework-emulatorn

1. Gå till [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Ladda ned och installera emulatorn.

### <a name="create-a-model"></a>Skapa en modell

1. Från start sidan modell lista klickar du `New Model` på knappen.
2. Tryck på RETUR i fälttypen,"självstudie-18-version".`Name`
4. Klicka på inställningar i den vänstra panelen.
5. Kopiera innehållet i fältet CONVERSATION_LEARNER_MODEL_ID till Urklipp.

### <a name="configure-the-emulator"></a>Konfigurera emulatorn

1. Öppna filen ". miljö" i rotmappen för Conversation Learner.
2. Lägg till en rad i filen ". miljö" så här:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Starta om Conversation Learner tjänsten genom att avsluta från kommando tolken och köra igen:
    - `npm run tutorial-general`
4. I bot Framework-emulator skapar du en ny robot konfiguration, anger slut punkts-URL: en till`http://localhost:3978/api/messages`

### <a name="version-1"></a>Version 1

Vi ska skapa en enda åtgärd för version 1.

1. I den vänstra panelen i webb gränssnittet klickar du på åtgärder och sedan på `New Action` knappen.
2. I fältet "robots svar" anger du "Hej där (version 1)".
3. Klicka på knappen `Save`.

Nu ska vi tagga detta som "version 1" av modellen.

1. Klicka på inställningar i den vänstra panelen och klicka ![](../media/tutorial18_version_tags.PNG)sedan på ikonen "versions Taggar" för att `New Tag` Visa knappen som du bör Klicka på.
    - Ge den namnet "version 1"
1. I list rutan "Live tag" väljer du "version 1".  
    - Nu kommer kanaler som använder den här roboten att använda "version 1" av vår modell.
    - Dialog rutorna entiteter, åtgärder och utbildning i den här modellen av version 1 kan inte längre ändras.
    - Om du väljer "version 1" som "redigerings tag" kan du bara visa modellen och inte redigera den.
    - Lämna "Edit tag" inställt på "Master", det är den enda versionen av modellen som kan redige ras.

Nu visas "version 1" i rutnätet "versions Taggar".

### <a name="version-2"></a>Version 2

Nu ska vi redigera vår modell för att skilja den från version 1.

1. Klicka på "åtgärder" i den vänstra panelen.
2. I rutnätet åtgärder klickar du på "Hej där (version 1)".
3. Ändra fältet "robots svar" till "Hej där (version 2)".
4. Klicka på knappen `Save`.
5. Klicka på knappen `New Action`.
6. I "robotens svar"-fält typ, "bye bye (version 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Bekräfta bot Framework-emulatorn använder version 1

1. Skriv meddelandet "Hej där" i bot Framework-emulatorn.
2. Observera att bot-meddelandet svarar med "Hej där (version 1)".
    - Detta verifierar att version 1 är "Live".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Visa konversations loggar i Conversation Learner webb gränssnitt

1. Klicka på "Logga dialog rutor" i den vänstra panelen
    - Om du inte ser några dialog rutor klickar du på knappen Uppdatera.
2. Observera taggen "version 1" i rutnätet.
3. I rutnätet klickar du på "Hej där (version 1)"

> [!NOTE]
> Vi kan göra korrigeringar genom att välja bland alla funktioner som är tillgängliga för närvarande Conversation Learner, men du kommer att göra dessa ändringar till Master och inte till version 1.

Nu har du sett hur versions hantering fungerar och hur du kan interagera med bot med hjälp av bot Framework-emulatorn.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Räkna upp entiteter och ange enhets åtgärder](./tutorial-enum-set-entity.md)
