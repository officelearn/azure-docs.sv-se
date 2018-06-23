---
title: Hur du använder entity identifiering motringning med en konversation deltagaren program - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig använda entiteten identifiering motringning med en konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e41ea5930ff0c8395d0c93aa42e224ebfc894ba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354021"
---
# <a name="how-to-use-entity-detection-callback"></a>Hur du använder enheten identifiering motringning

Den här kursen visar entitet identifiering återanropet och visar ett vanligt mönster för att lösa entiteter.

## <a name="requirements"></a>Krav
Den här kursen kräver att ”tutorialEntityDetectionCallback” bot körs.

    npm run tutorial-entity-detection

## <a name="details"></a>Information
Entiteten identifiering motringning kan med anpassad kod för att hantera affärsregler som rör entiteter. I den här demon använder vi återanrop och programmässiga enheter för att matcha namnet på staden som anges av användaren till ett kanoniskt namn – till exempel lösa ”stor Apples” till ”new york”.

### <a name="open-the-demo"></a>Öppna demonstrationen

Klicka på kursen-10-EntityDetenctionCallback i listan över appar. 

### <a name="entities"></a>Entiteter

Vi har definierat tre entiteter i programmet.

![](../media/tutorial10_entities.PNG)

1. Ort är en anpassad entitet som ger användaren som indata för text.
2. CityUnknown är en programmässiga enhet. Detta ska hämta fylls i av systemet. Den kommer att kopiera användarens indata om systemet inte vet vilken stad som den är.
3. CityResolved är den stad som systemet känna till. Detta kan vara stads kanoniskt namn, till exempel 'stort apple' matchar med new york.

### <a name="actions"></a>Åtgärder

Vi har skapat tre åtgärder. 

![](../media/tutorial10_actions.PNG)

1. Den första åtgärden är 'vilken stad vill du fortsätta?'
2. Andra är ' ort $CityUknown vet inte. Vilken stad vill du fortsätta?'
3. Tredje är ”du SA $City och jag matchas som $CityResolved”.

### <a name="callback-code"></a>Motringning kod

Nu ska vi titta på koden. Du kan hitta metoden EntityDetectionCallback på c:\<installedpath > \src\demos\tutorialSessionCallbacks.ts fil.

![](../media/tutorial10_callbackcode.PNG)

Den här funktionen anropas när entiteten lösning har använts.
 
- Det första som den gör är Rensa $CityUknown. $CityUknown behålls endast på en enda tur som den alltid hämtar rensas i början.
- Sedan kan hämta vi listan över orter har tolkats. Ta först och försök att matcha den.
- Funktionen som löser den (resolveCity) definieras ytterligare i koden ovan. Den har en lista över kanoniska stadsnamn. Namnet på staden hittas i listan, returneras den. Annars ser ut i 'cityMap' och returnerar mappade namnet. Om det går inte att hitta en stad, returnerar null.
- Slutligen lagra orten har lösts till ett namn, vi i $CityKnown entitet. Annars avmarkerar du vad användaren säger och fylla $CityUknown entitet.

### <a name="train-dialogs"></a>Träna dialogrutor

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vilken stad vill du fortsätta?'
2. Ange new york.
    - Observera att den hämtar känns igen som en stad entitet.
5. Klicka på poäng åtgärder
    - Observera att ort och CityResolved är ifyllda.
6. Välj ”du SA $City och jag matchas som $CityResolved”.
7. Klicka på klar lärare.

Lägg till en annan dialogruta för exempel:

1. Klicka på ny Train-dialogruta.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vilken stad vill du fortsätta?'
2. Ange 'stort apple'.
    - Observera att den hämtar känns igen som en stad entitet.
5. Klicka på poäng åtgärder
    - Observera att CityResolved illustrerar kod som körs.
6. Välj ”du SA $City och jag matchas som $CityResolved”.
7. Klicka på klar lärare.

Lägg till en annan dialogruta för exempel:

1. Klicka på ny Train-dialogruta.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vilken stad vill du fortsätta?'
2. Ange ”foo”.
    - Detta är ett exempel på en stad inte känner till systemet. 
5. Klicka på poäng åtgärder
6. Välj ' ort $CityUknown vet inte. Vilken stad vill du fortsätta?'.
7. Ange new york.
8. Klicka på poäng åtgärder.
    - Observera att CityUknown har rensats och CityResolved fylls i.
6. Välj ”du SA $City och jag matchas som $CityResolved”.
7. Klicka på klar lärare.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Sessionen återanrop](./11-session-callbacks.md)
