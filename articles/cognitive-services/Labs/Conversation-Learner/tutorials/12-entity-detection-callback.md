---
title: Så här använder du motringning av enhets identifiering med en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder motringning för enhets identifiering med en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3f6094da7b103f1c99e9bb3fa9313354fcb2a8bc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703995"
---
# <a name="how-to-use-entity-detection-callback"></a>Så här använder du motringning av enhets identifiering

Den här självstudien visar återanrop för enhets identifiering och illustrerar ett gemensamt mönster för att matcha entiteter.

## <a name="video"></a>Video

[![För hands version av återanrop för entitets avkänning](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Krav
Den här självstudien `tutorialEntityDetectionCallback` kräver att bot körs.

    npm run tutorial-entity-detection

## <a name="details"></a>Information
Återanrop för enhets identifiering möjliggör modifiering av enhets igenkänningens beteende och modifiering av entiteten genom kod. Vi demonstrerar den här funktionen genom att söka igenom ett typiskt design mönster för motringning av enhets identifiering. Du kan till exempel lösa "Big äpple" till "New York".

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I webb gränssnittet klickar du på "ny modell".
2. Skriv "EntityDetectionCallback" i fältet "namn" och tryck på RETUR.
3. Klicka på knappen "skapa".

Tre entiteter behövs i det här exemplet, så vi skapar de tre.

### <a name="create-the-custom-trained-entity"></a>Skapa en anpassad, utbildad entitet

1. Klicka på entiteter i den vänstra panelen, sedan på knappen ny enhet.
2. Välj anpassad tränad för enhets typen.
3. Skriv "stad" för "entitetsnamnet".
4. Klicka på knappen "skapa".

### <a name="create-the-first-programmatic-entity"></a>Skapa den första program mässig entiteten

1. Klicka på knappen "ny entitet".
2. Välj "program mässig" som enhets typ.
3. Skriv "CityUnknown" som enhets namn.
4. Klicka på knappen "skapa".

### <a name="create-the-first-programmatic-entity"></a>Skapa den första program mässig entiteten

1. Klicka på knappen "ny entitet".
2. Välj "program mässig" som enhets typ.
3. Skriv "CityResolved" som enhets namn.
4. Klicka på knappen "skapa".

Skapa nu tre åtgärder.

### <a name="action-creation"></a>Skapa åtgärd

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I "robotens svar..." anger du "vilken ort vill du ha?"
3. I enheten "förväntad entitet i användar svar..." anger du "ort".
4. Skriv "ort" i fältet "diskvalificera entiteter".
5. Klicka på knappen "skapa".
6. Klicka på knappen Ny åtgärd.
7. I "robotens svar..." anger du "vilken ort vill du ha?"
8. I enheten "förväntad entitet i användar svar..." skriver du "Jag känner inte till den här staden".
9. Klicka på knappen "skapa".
10. Klicka på knappen Ny åtgärd.
11. I "robotens svar..." fältet skriver du "$CityResolved är mycket trevligt".
12. Klicka på knappen "skapa".

Här är callback-koden:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Träna modellen

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Hej"
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret, "vilken ort vill du ha?"
5. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Big Apple"
6. Klicka på knappen "Poäng åtgärder".
    - Genom att klicka på knappen utlöses återanrop för enhets identifiering
    - Återanrops koden anger CityResolved-enhetens värde korrekt till "New York"
7. Välj svaret, "New York är mycket trevligt."

Det här mönstret är typiskt för många bot-scenarier. Användarens yttranden och extraherade entiteter levereras till din affärs logik och den logiken omvandlar uttryck till kanoniskt format, som sedan sparas i programmatiska entiteter för efterföljande sväng i dialog rutan.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Motanrop för sessioner](./13-session-callbacks.md)
