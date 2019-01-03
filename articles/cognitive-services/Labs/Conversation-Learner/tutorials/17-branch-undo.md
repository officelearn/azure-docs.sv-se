---
title: Hur du använder branchning och ångra åtgärder med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder branchning och ångra åtgärder med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: d57270a4db2129370da7336d8a7bf012848890c2
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796847"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Hur du använder branchning och ångra åtgärder
I den här självstudien ska vi gå igenom Ångra och branchning åtgärder.


## <a name="details"></a>Information
### <a name="undo"></a>Ångra
Kan utvecklare ”Ångra” senaste användarens indata eller åtgärd val. I bakgrunden ”Ångra” faktiskt skapar en ny dialogruta och spelar upp det upp till föregående steg.  Det innebär att entiteten identifiering av återanrop och API-anrop i dialogrutan anropas igen.

### <a name="branch"></a>Branch
Skapar en ny dialogruta träna börjar på samma sätt som en befintlig träna dialogrutan – detta sparar arbetet med manuellt återinföra dialogrutan stängs. I bakgrunden ”gren” skapar en ny dialogruta och spelar upp dialogrutan befintliga träna upp till det valda steget.  Det innebär att entiteten identifiering av återanrop och API-anrop i dialogrutan anropas igen.


## <a name="requirements"></a>Krav
Den här självstudien krävs att roboten som tar pizza order körs:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Öppna eller importera demon

Om du redan har arbetat självstudien pizza skrivordning, helt enkelt öppna sedan den modellen i listan i webbgränssnittet. Annars måste du klicka på ”Importera Tutorials” och välja modellen med namnet ”Demo-PizzaOrder”.

## <a name="undo"></a>Ångra

Här är ett exempel på hur du ser den `Undo` funktionen fungerar i praktiken:

### <a name="training-dialogs"></a>Dialogrutor för utbildning
1. Klicka på ”Train-dialogrutor” på den vänstra rutan och klicka sedan på den `New Train Dialog` knappen.
2. Skriv ”Order en platt”.
3. Klicka på knappen `Score Actions`.
4. Klicka för att väljer ”vad du vill på din pizza”?
5. Skriv ”allt”.
6. Klicka på knappen `Undo`.
    - Den sista posten tas bort, lämna det senaste Bot-svaret för ”vad du vill på din pizza”?

## <a name="branch"></a>Branch

Den här demonstrationen vi öppna en befintlig Train-dialogruta och skapa en ny Train-dialogruta från den av förgrening.

1. Klicka på ”Train-dialogrutor” på den vänstra panelen.
2. Lägg märke till rutnätet visas endast en utbildning som börjar med ”nya order”.
3. Klicka på ”nya order” i rutnätet för att öppna dialogrutan befintliga träna.
4. Klicka på sist ”Nej” i dialogrutan.
5. Klicka på ikonen ”gren”, det är inringat i rött i den här bilden:
    - ![](../media/tutorial15_branch.PNG)
    - Dialogrutan hela träna före ”Nej” kopieras till en ny Train-dialogruta.
    - Detta sparar du behöva ange föregående aktiverar för att utforska en ny konversation ”gren” från och med nu.
6. Skriv ”Ja”, träffar ange.
7. Klicka på knappen `Score Actions`.
    - I det här läget roboten hämtar ett svar automatiskt, men du vill använda inte svaret vi kommer att ändra den.
8. Klicka på det senaste Bot-svaret.
    - Detta väljer kan vi ett annat svar.
9. Välj ”UseLastToppings”.
10. Klicka på knappen `Score Actions`.
    - Roboten hämtar igen automatiskt ett svar. Det bör stå, ”du har köttråvara, ost och svamp på din pizza”. 
    - Den här gången vi gillar svaret så vi kommer att behålla.
11. Klicka på knappen `Score Actions`.
    - Igen hämtar roboten automatiskt ett svar, det ska stå ”vill du göra något annat”?
12. Skriv ”Nej”.
13. Klicka på knappen `Save Branch`.
14. Observera att rutnätet nu har två utbildningar som börjar med ”nya order”.
    - En av dem är det du använde för att förgrening av.
    - Och den andra är den förgrenade versionen som du just har sparat.
    - Klicka på var och en av dem att verifiera förväntningarna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Versionshantering och taggning](./18-version-tag.md)
