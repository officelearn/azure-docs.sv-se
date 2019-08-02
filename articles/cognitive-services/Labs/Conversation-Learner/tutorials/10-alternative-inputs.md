---
title: Så här använder du alternativa indata med Conversation Learner-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder alternativa indata med Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7416939db8d6552f79fba700b8432de2ad228846
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704204"
---
# <a name="how-to-use-alternative-inputs"></a>Använda alternativa indata

Den här självstudien visar hur du använder fältet alternativa indata för användar-yttranden i undervisnings gränssnittet.

## <a name="video"></a>Video

[![Förhands granskning av alternativa indata](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information
Alternativa indata är alternativa, semantiskt likvärdiga användar yttranden som användaren kan ha sagt vid en viss tidpunkt i en tränings dialog ruta. Med dessa alternativa indata kan du mer komprimera variationer av yttranden utan att behöva adressera varje variation i separata tränings dialog rutor.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I webb gränssnittet klickar du på "ny modell".
2. Skriv "AlternativeInputs" i fältet "namn" och tryck på RETUR.
3. Klicka på knappen "skapa".

### <a name="entity-creation"></a>Skapa entitet

1. Klicka på entiteter i den vänstra panelen, sedan på knappen ny enhet.
2. Välj anpassad tränad för enhets typen.
3. Skriv "stad" för "entitetsnamnet".
4. Klicka på knappen "skapa".

![](../media/T10_actions.png)

Nu ska vi skapa tre åtgärder.

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I "robotens svar..." anger du "vilken ort?"
3. I enheten "förväntad entitet i användar svar..." anger du "ort".
4. Skriv "ort" i fältet "diskvalificerande rättigheter".
5. Klicka på knappen "skapa".

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I "robotens svar..." anger du "Väder i $city är förmodligen solig".
3. Klicka på knappen "skapa".

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>Skapa den tredje åtgärden

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I "robotens svar..." skriver du "försök att be om väder".
3. Skriv "ort" i fältet "diskvalificerande rättigheter".
4. Klicka på knappen "skapa".

![](../media/T10_action_create_3.png)

Nu har du tre åtgärder.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>Träna modellen

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Vad är väder?"
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret, "vilken ort?"
5. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Denver"
6. Klicka på knappen "Poäng åtgärder".
7. Välj svaret, "Väder i Denver är förmodligen solig".
8. Klicka på knappen Skicka ändringar.

![](../media/T10_training_1.png)

Låt oss träna modellen mer genom att skapa en annan träna-dialog.

### <a name="second-model-train-dialog"></a>Dialog rutan andra modell träna

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Vad kan du göra?"
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret, "försök att be om väder".
5. I panelen chat, där det står "Skriv ditt meddelande...", skriver du "Vad är väder i Seattle?"
6. Klicka på "Seattle" och sedan på "stad" från listan entitet.
7. Klicka på knappen "Poäng åtgärder".
8. Välj svaret, "Väder i Seattle är förmodligen solig".
9. Klicka på knappen Skicka ändringar.

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Dialog rutan för tredje modell träna med alternativa ingångar

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "hjälp"
3. Klicka på knappen "Poäng åtgärder".
    - Modellen är inte säker för det bästa alternativet, så den väljer den högsta percentilen som standard.
4. Klicka på knappen "överge undervisning" och sedan på bekräfta.

![](../media/T10_training_3.png)

Låt oss förbättra systemet med alternativa indata. Du kan lägga till alternativa ingångar under undervisning eller senare.

1. Klicka på "träna dialog rutor" i den vänstra panelen och välj sedan "Vad kan du göra?" i listan över träna-dialog rutor.
1. Klicka på "Vad kan du göra?" uttryck på panelen chat.
1. I avsnittet "Lägg till alternativa ingångar..." skriver du "Help" och trycker på RETUR.
1. Klicka på knappen "Spara ändringar".

![](../media/T10_training_4.png)

Nu ska vi lägga till en annan alternativ ingång för att hantera Houston.

1. Klicka på "Vad är väder i Seattle?" uttryck på panelen chat.
1. I avsnittet "Lägg till alternativa ingångar..." anger du "prediktion för Houston" och trycker på RETUR.
   - Fel meddelandet visar att de alternativa inmatningarna måste vara semantiskt likvärdiga och innehålla samma entiteter som den ursprungliga uttryck. inte bara samma värden för entiteter. Förekomst av samma entiteter krävs.
1. Klicka på "Houston" och välj "stad" i listan entiteter.
1. I avsnittet "Lägg till alternativa ingångar..." anger du "prognos för Seattle" och trycker på RETUR.
1. Klicka på "Seattle" och välj "stad" i listan entiteter.
1. Klicka på knappen "Spara ändringar".
1. Klicka på knappen "Spara redigering".

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>Testa modellen

1. Klicka på "Logga dialog rutor" i den vänstra panelen, sedan på "ny logg dialog ruta".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du "hjälp mig"
3. I panelen chat, där det står "Skriv ditt meddelande...", skriver du "prediktion for Denver"

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Logg dialog rutor](./11-log-dialogs.md)
