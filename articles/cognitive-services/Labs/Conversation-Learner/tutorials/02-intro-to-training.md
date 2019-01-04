---
title: Introduktion till träna en modell för Konversationsdeltagare - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du tränar en modell, inklusive branchning och redigera tidigare utbildning via Konversationsdeltagare.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e7405583a04699ce0481c95b22912e717f76743a
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796877"
---
# <a name="introduction-to-training"></a>Introduktion till utbildning

Den här kursen visar grunderna för att träna en modell, branchning av en ny utbildning baserat på en tidigare utbildning och redigera ett Bot-svar för att kunna ändra den.

## <a name="video"></a>Video

[![Introduktion till utbildning självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

- Åtgärder: Svaret Bot användarindata.
- Träna: Hur Lär vi en robot svarar på indata från användaren.
- Branchning: Ändring av användarindata inom en sparad träna dialogruta i syfte att skapa en ny Train-dialogruta som börjar samma som ursprungligt, men tar konversationen i en annan riktning.

## <a name="steps"></a>Steg

### <a name="create-a-new-model"></a>Skapa en ny modell

1. I Webbgränssnittet, klickar du på en ny modell
2. För ”namn”, skriver du ”inspirera Bot”. Klicka på Skapa.

### <a name="create-an-action"></a>Skapa en åtgärd

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. I ”Robotens svaret” anger du ”Hej! Vill du få inspiration idag ”?.
    - Lämna andra fält och kryssrutorna på sina standardinställningen.
3. Klicka på Skapa.

### <a name="first-training-and-creating-another-action-while-training"></a>Först träning och skapa en annan åtgärd vid utbildning

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, ange ”hello”. 
    - Det här simulerar användarens sida för konversationen.
3. Klicka på ”poäng åtgärder”.
4. Välj svar ”Hej! Vill du få inspiration idag ”?.
5. Som användare, svarar du med, ”yes”.
6. Klicka på ”poäng åtgärder”.
7. Klicka på den ”+ åtgärd” knappen. 
    - Då kommer du till dialogrutan välbekanta ”skapa en åtgärd”.
8. Skriv Robotens svar som ”du är fantastiska”!
9. Klicka på Skapa.
10. Observera att roboten svarar omedelbart.
11. Klicka på knappen ”Spara”.

### <a name="branch-a-second-training-off-of-the-first-training"></a>Gren en andra utbildning från den första utbildning
1. Klicka på den rad som sammanfattar första utbildningen. 
    - På så sätt kan du visa och redigera befintliga utbildningen.
2. Klicka på ”Ja” användarens svar. 
    - Detta visas redigeringskontroller.
3. Klicka på ikonen gren. 
    - Detta visas en uppmaning om en annan användarindata för en ny konversation.
4. Typen i ”Nej” träffar ange eller klicka på knappen ”Skapa”. 
    - Då du har en ny instans av en dialogruta för Train, det ursprungliga förblir oförändrat.
5. Klicka på ”poäng åtgärder”.
6. Klicka på den Bot felaktigt svar som visas.
7. Klicka på den ”+ åtgärd” knappen 
    - så att vi kan skapa en ny åtgärd för robot att svara med.
8. Skriv Robotens svar som ”inga problem! Ha en bra dag ”!
9. Klicka på Skapa
10. Observera att roboten svarar omedelbart.
11. Klicka på knappen ”Spara”.

### <a name="test-the-trainings"></a>Testa utbildningar
1. Klicka på ”Log-dialogrutor” och ”dialogrutan för nytt Log” på den vänstra panelen.
2. Ange meddelandet, ”Hej”. 
3. Observera att roboten automatiskt svarar på det sätt som vi har tränats.
4. Skriv ”yes” i svaret från användaren.
5. Lägg märke till Bot-svaret visar att den första utbildningen fungerar.
6. Klicka på knappen ”tidsgräns för Session”. Det här visar Konversationsdeltagare vi vill börja igen, ignorera de konversationsanpassade aktiveras som bara ägde rum.
7. Ange meddelandet, ”Hej”. 
8. Observera att roboten automatiskt svarar på det sätt som vi har tränats.
9. Skriv ”Nej” i svaret från användaren.
10. Lägg märke till Bot-svaret visar att andra utbildningen fungerar.
11. Klicka på knappen ”klar testning”.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vänta och icke-wait-åtgärder](./03-wait-vs-nonwait-actions.md)
