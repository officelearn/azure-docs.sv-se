---
title: Introduktion till att träna en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du tränar en modell, inklusive branchning och redigering av tidigare utbildning via Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c657025ce588363cf76ce10868d809a9aff69222
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705617"
---
# <a name="introduction-to-training"></a>Introduktion till utbildning

I den här självstudien lär du dig grunderna i att träna en modell, förgrena in en ny utbildning baserat på en tidigare utbildning och redigera ett robot svar för att ändra det.

## <a name="video"></a>Video

[![Förhands granskning av introduktion till utbildning](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information

- Action Ett robot svar på användarindata.
- Träning: Hur vi lär oss en robot för att svara på användarindata.
- Branchning Ändringen av en användarindata i en sparad träna-dialog ruta för att skapa en ny träna-dialog ruta som börjar på samma sätt som den ursprungliga, men tar konversationen i en annan riktning.

## <a name="steps"></a>Steg

### <a name="create-a-new-model"></a>Skapa en ny modell

1. I webb gränssnittet klickar du på ny modell
2. Skriv "inspirera bot" som "namn". Klicka sedan på Skapa.

### <a name="create-an-action"></a>Skapa en åtgärd

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I fältet "robots svar" anger du "Hej! Vill du bli inspirerad idag? ".
    - Lämna alla andra fält och kryss rutor i standardinställningen.
3. Klicka på Skapa.

### <a name="first-training-and-creating-another-action-while-training"></a>Den första utbildningen och skapa en annan åtgärd medan du tränar

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Hello". 
    - Detta simulerar användarens sida av konversationen.
3. Klicka på poängsättnings åtgärder.
4. Välj svaret "Hej! Vill du bli inspirerad idag? ".
5. Som användare svarar du på "Ja".
6. Klicka på poängsättnings åtgärder.
7. Klicka på knappen "+ åtgärd". 
    - Det tar dig till den välbekanta dialog rutan "skapa en åtgärd".
8. Skriv robotens svar som, "du är toppen!"
9. Klicka på Skapa.
10. Observera att roboten svarar omedelbart.
11. Klicka på knappen Spara.

### <a name="branch-a-second-training-off-of-the-first-training"></a>Förgrena en andra utbildning från den första utbildningen
1. Klicka på rutnäts raden som sammanfattar den första utbildningen. 
    - På så sätt kan du Visa och redigera den befintliga utbildningen.
2. Klicka på användar svaret "Ja". 
    - Detta visar redigerings kontroller.
3. Klicka på gren ikonen. 
    - Detta innebär att du uppmanas att ange en annan användarindata för en ny konversation.
4. Skriv "nej", tryck på RETUR eller klicka på knappen "skapa". 
    - Nu kommer du att ha en ny instans av en träna-dialog, men den ursprungliga är oförändrad.
5. Klicka på poängsättnings åtgärder.
6. Klicka på robotens felaktiga svar som precis visades.
7. Klicka på knappen "+ åtgärd" 
    - så att vi kan skapa en ny åtgärd för roboten att svara på.
8. Skriv in robotens svar som, "inga problem! Ha en bra dag! "
9. Klicka på Skapa
10. Observera att roboten svarar omedelbart.
11. Klicka på knappen Spara.

### <a name="test-the-trainings"></a>Testa utbildningarna
1. Klicka på "Logga dialog rutor" och sedan på "ny logg dialog ruta" i den vänstra panelen.
2. Skriv i meddelandet "Hej". 
3. Observera att roboten svarar automatiskt på det sätt vi tränade den.
4. Skriv användarens svar, ja.
5. Observera att robot svaret visar att den första utbildningen fungerar.
6. Klicka på knappen tids gräns för session. Detta talar om för Conversation Learner vi vill börja om, vilket ignorerar konversationen som precis ägde rum.
7. Skriv i meddelandet "Hej". 
8. Observera att roboten svarar automatiskt på det sätt vi tränade den.
9. Ange användar svaret "nej".
10. Observera att robot svaret visar att den andra utbildningen fungerar.
11. Klicka på knappen "Slutför testning".

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vänta och ej väntande åtgärder](./03-wait-vs-nonwait-actions.md)
