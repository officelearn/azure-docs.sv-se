---
title: Använda branchning och ångra åtgärder med en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder förgreningar och åtgärder för att ångra med en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f4f3024451696dbd0244d9da39cba67b49447af1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703624"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Använda branchning och ångra åtgärder
I den här självstudien kommer vi att gå igenom åtgärder för att ångra och förgrena.


## <a name="details"></a>Information
### <a name="undo"></a>Ångra
Tillåter utvecklare att "ångra" den senaste användarindata eller åtgärds val. "Ångra" i bakgrunden skapar i själva verket en ny dialog ruta och spelas upp i föregående steg.  Det innebär att entitetens identifierings-och API-anrop i dialog rutan kommer att anropas igen.

### <a name="branch"></a>Gren
Skapar en ny träna-dialog ruta som börjar på samma sätt som en befintlig träna-dialog – det här sparar arbetet med att manuellt ange dialog rutan igen. I bakgrunden skapar "gren" en ny dialog ruta och spelar upp den befintliga träna-dialog rutan upp till det valda steget.  Det innebär att entitetens identifierings-och API-anrop i dialog rutan kommer att anropas igen.


## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten som tar pizza-beställningar körs:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Öppna eller importera demonstrationen

Om du redan har arbetat genom självstudien om pizza-beställning öppnar du bara den modellen från listan i webb gränssnittet. Annars måste du klicka på "Importera självstudier" och välja modellen "demo-PizzaOrder".

## <a name="undo"></a>Ångra

Här är ett exempel på hur du kan se `Undo` hur funktionen fungerar:

### <a name="training-dialogs"></a>Tränings dialog rutor
1. Klicka på "träna dialog rutor" i den vänstra panelen och klicka sedan på `New Train Dialog` knappen.
2. Skriv "Beställ a pizza".
3. Klicka på knappen `Score Actions`.
4. Klicka för att välja "Vad vill du ha på din pizza?"
5. Skriv "vad".
6. Klicka på knappen `Undo`.
    - Den sista posten tas bort och lämnar det sista robot svaret "Vad vill du ha på din pizza?"

## <a name="branch"></a>Gren

I den här demon öppnar vi en befintlig träna-dialog och skapar en ny träna-dialog ruta från den genom förgrening.

1. Klicka på "träna dialoger" i den vänstra panelen.
2. Observera rutnätet. du bör bara se en utbildning som börjar med "ny beställning".
3. I rutnätet klickar du på "ny ordning" för att öppna dialog rutan befintliga träna.
4. Klicka på den sista i dialog rutan.
5. Klicka på ikonen "gren", den är inringad i rött i den här bilden:
    - ![](../media/tutorial15_branch.PNG)
    - Hela träna-dialog rutan före "nej" kopieras till en ny träna-dialog ruta.
    - På så sätt slipper du att ange föregående åter för att utforska en ny konversation "gren" från den här punkten.
6. Skriv "Ja" och tryck på RETUR.
7. Klicka på knappen `Score Actions`.
    - I det här läget väljer bot automatiskt ett svar, men vi gillar inte svaret så vi ska ändra det.
8. Klicka på det sista robot svaret.
    - På så sätt kan vi välja ett annat svar.
9. Välj "UseLastToppings".
10. Klicka på knappen `Score Actions`.
    - Återigen väljer roboten ett svar automatiskt. Det bör stå "du har korv, ost och svamp på din pizza.". 
    - Den här gången vill vi att vi ska behålla svaret.
11. Klicka på knappen `Score Actions`.
    - Om bot-roboten automatiskt väljer ett svar, bör det stå "vill du ha något annat?"
12. Skriv "nej".
13. Klicka på knappen `Save Branch`.
14. Observera att rutnätet nu har två utbildningar som börjar med "ny beställning".
    - En av dem är den som du använde för att förgrena ut från.
    - Och den andra är den Grenade version som du nyss sparade.
    - Klicka på var och en av dem för att kontrol lera förväntningarna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Versions hantering och taggning](./18-version-tag.md)
