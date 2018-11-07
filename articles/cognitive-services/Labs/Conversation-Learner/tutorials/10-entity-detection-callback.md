---
title: Hur du använder entiteten identifiering motringning med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder entiteten identifiering motringning med en Konversationsdeltagare modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e9a3b0a2be58313266b949b907e4eb49a318a6dc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260082"
---
# <a name="how-to-use-entity-detection-callback"></a>Hur du använder entiteten identifiering av återanrop

Den här självstudien visar återanropet som entiteten identifiering och illustrerar ett vanligt mönster för att lösa entiteter.

## <a name="video"></a>Video

[![Självstudien 10 förhandsversion](https://aka.ms/cl-tutorial-10-preview)](https://aka.ms/blis-tutorial-10)

## <a name="requirements"></a>Krav
Den här självstudien kräver att den `tutorialEntityDetectionCallback` bot körs.

    npm run tutorial-entity-detection

## <a name="details"></a>Information
Entiteten identifiering av återanrop kan använda anpassad kod för att hantera affärsregler som rör entiteter. Den här demonstrationen använder återanrop och programmässig entiteter för att matcha namnet anges av användaren till ett kanoniskt namn – till exempel hur du löser ”big apple” till ”new york”.

### <a name="open-the-demo"></a>Öppna demon

Klicka på självstudien-10-EntityDetectionCallback i listan över modellen. 

### <a name="entities"></a>Entiteter

Tre entiteter har definierats i modellen.

![](../media/tutorial10_entities.PNG)

1. Stad är en anpassad entitet som användaren ska tillhandahålla under textinmatning.
2. CityUnknown är en programmatisk entitet. Den här entiteten kommer få uppdateras av systemet. Den kopierar indata från användaren om systemet inte vet vilken stad som det är.
3. CityResolved är den stad som systemet veta om. Den här entiteten kommer att city kanoniskt namn, till exempel ”big apple' matchar med” new york ”.

### <a name="actions"></a>Åtgärder

Tre åtgärder har definierats i modellen.

![](../media/tutorial10_actions.PNG)

1. Den första åtgärden är ”vilken stad vill”?
2. Andra är ”jag vet inte den här stad, $CityUknown. Vilken stad vill ”?
3. Tredje är ”du SA $City och jag matchas som med $CityResolved”.

### <a name="callback-code"></a>Motringning kod

Nu ska vi titta på koden. Du kan hitta metoden EntityDetectionCallback i enhet C:\<installedpath > \src\demos\tutorialSessionCallbacks.ts fil.

![](../media/tutorial10_callbackcode.PNG)

Den här funktionen anropas när entiteten lösning har använts.
 
- Det första som den gör är tydlig $CityUknown. $CityUknown behålls endast på en enda tur eftersom den alltid hämtar avmarkerad i början.
- Sedan kan hämta listan över städer som har blivit erkänt. Ta det första och försök att lösa den.
- Den funktion som löser dessa (resolveCity) definieras ytterligare ovan i koden. Den har en lista över canonical stadsnamn. Den hittar namnet i listan, returneras den. Annars ser ut i ”cityMap” och returnerar det mappade namnet. Om det går inte att hitta en stad, returnerar null.
- Slutligen lagra staden har löst till ett namn, vi i $CityKnown entitet. Annars avmarkerar du vad du har sagt och fylla i $CityUknown entitet.

### <a name="train-dialogs"></a>Träna dialogrutor

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vilken stad vill”?
2. Ange ”new york”.
    - Texten har identifierats som en stad entitet.
5. Klicka på poäng åtgärder
    - `City` och `CityResolved` är ifyllda.
6. Välj ”du SA $City och jag matchas som med $CityResolved”.
7. Klicka på klar undervisning.

Lägg till ett annat exempel dialogrutan:

1. Klicka på dialogrutan för nytt träna.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vilken stad vill”?
2. Ange ”big apple”.
    - Texten har identifierats som en stad entitet.
5. Klicka på poäng åtgärder
    - `CityResolved` visar effekten av kod som körs.
6. Välj ”du SA $City och jag matchas som med $CityResolved”.
7. Klicka på klar undervisning.

Lägg till ett annat exempel dialogrutan:

1. Klicka på dialogrutan för nytt träna.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vilken stad vill”?
2. Ange ”foo”.
    - Det här är ett exempel på en stad systemet inte vet. 
5. Klicka på poäng åtgärder
6. Välj ”jag vet inte den här stad, $CityUknown. Vilken stad vill ”?.
7. Ange ”new york”.
8. Klicka på poäng åtgärder.
    - `CityUknown` har rensats, och `CityResolved` har fyllts i.
6. Välj ”du SA $City och jag matchas som med $CityResolved”.
7. Klicka på klar undervisning.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Sessionen återanrop](./11-session-callbacks.md)
