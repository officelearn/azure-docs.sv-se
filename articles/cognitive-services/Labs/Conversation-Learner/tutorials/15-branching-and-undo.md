---
title: Hur du använder förgrening och ångra åtgärder med en konversation deltagaren program - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder förgrening och ångra åtgärder med en konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 724a9e47267e0bd7417130efe54c609ac7a465fb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354033"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Hur du använder förgrening och ångra åtgärder
I den här självstudiekursen kommer vi gå igenom Ångra och grenstruktur åtgärder.

## <a name="details"></a>Information
- Ångra: kan utvecklare ”återställa” en användarens indata eller åtgärd val. I bakgrunden ”Ångra” faktiskt skapar en ny dialogruta och nytt spelar det upp till föregående steg.  Detta innebär att entiteten identifiering motringning och API-anrop i dialogrutan anropas igen.

- Gren: skapar en ny train-dialogruta som börjar på samma sätt som en befintlig träna dialogrutan – merarbete som sparas manuellt återinföra dialogrutan stängs. I bakgrunden ”gren” skapar en ny dialogruta och spelar befintliga train dialogrutan upp till det valda steget igen.  Detta innebär att entiteten identifiering motringning och API-anrop i dialogrutan anropas igen.


## <a name="requirements"></a>Krav
Den här kursen krävs att pizza ordning bot körs:

    npm run demo-pizza

### <a name="open-the-demo"></a>Öppna demonstrationen

Klicka på TutorialDemo Pizza ordning i App-lista över webbgränssnittet. 

Information om Pizza ordning demo finns Pizza ordning kursen.

## <a name="undo"></a>Ångra

Vi ångra en del av dialogrutan och skapa den på steget.

### <a name="training-dialogs"></a>Dialogrutor för utbildning
Låt oss börja en utbildningen. 

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
1. Ange ”beställa ett pizza'.
2. Klicka på poäng åtgärd.
3. Klicka om du vill markera 'vad du vill på din pizza ”?
4. Ange ”svamp och ost”.
5. Klicka på poäng åtgärder.
3. Klicka på ”du har $Toppings på din pizza'.
6. Välj 'Vill du göra något annat ”?
7. Ange ”ta bort svamp och lägga till paprikor”.
    - Välj svamp och avmarkera Toppings entiteten. Vi skapar en åtgärd som vi kommer att ångra.
2. Klicka på Ångra steg.
    - Observera att den sista posten tas bort, och vi är tillbaka på 'Vill du göra något annat ”?  (skärmbilden nedan)
2. Ange ”ta bort svamp och lägga till paprikor”.
8. Markera ”du har $Toppings på din pizza'
    - Kontrollera att båda enheter väljs på rätt sätt.
2. Klicka på poäng åtgärd. Du kan fortsätta med dialogrutan korrigerade nu.
4. Klicka på klar lärare.

Nu har du sett hur du använder Ångra om du vill ta bort en användarindata och åtgärden.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Gren

Exempelvis vi öppnar du en befintlig train dialogrutan och skapa en annan dialogruta för tåg av förgrening.

1. Klicka Train-dialogrutor 'nya order' att öppna dialogrutan befintliga. 
2. Klicka på sist 'Nej' i dialogrutan (se skärmbilden nedan).
3. Klicka på avdelningskontor.
    - Observera att ”Nej” tas bort och hela dialogrutan hittills kopieras till en ny. 
    - Detta sparar du behöva ange den föregående stängs för att utforska en ny ”gren” från och med nu.
1. Ange ”Ja”.
2. Klicka på poäng åtgärd.
3. Välj ”du har $Toppings på din pizza'.
6. Välj 'Vill du göra något annat ”?
7. Ange ”Nej”.
4. Klicka på klar lärare.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Versionshantering och taggning](./16-versioning-and-tagging.md)
