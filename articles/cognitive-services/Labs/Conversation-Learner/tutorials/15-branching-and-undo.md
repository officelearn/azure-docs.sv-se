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
ms.openlocfilehash: 05140693026e21a73b756ed0ea7bc9936bef067e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173306"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Hur du använder branchning och ångra åtgärder
I den här självstudien ska vi gå igenom Ångra och branchning åtgärder.


## <a name="details"></a>Information
- Ångra: kan utvecklare ”Ångra” användarens indata eller åtgärd val. I bakgrunden ”Ångra” faktiskt skapar en ny dialogruta och nytt spelar det upp till föregående steg.  Det innebär att entiteten identifiering av återanrop och API-anrop i dialogrutan anropas igen.

- Gren: skapar en ny train-dialogruta som börjar på samma sätt som en befintlig träna dialogrutan – detta sparar arbetet med manuellt återinföra dialogrutan stängs. I bakgrunden ”gren” skapar en ny dialogruta och spelar dialogrutan befintliga träna upp till det valda steget igen.  Det innebär att entiteten identifiering av återanrop och API-anrop i dialogrutan anropas igen.


## <a name="requirements"></a>Krav
Den här självstudien krävs att pizza ordning bot körs:

    npm run demo-pizza

### <a name="open-the-demo"></a>Öppna demon

Klicka på TutorialDemo Pizza ordningen i listan modell av webbgränssnittet. 

Information om hur demon Pizza ordning, finns i självstudien Pizza ordning.

## <a name="undo"></a>Ångra

Vi ångra en del av dialogrutan och återskapa den från det steget.

### <a name="training-dialogs"></a>Dialogrutor för utbildning
Låt oss börja en utbildningssession. 

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
1. Ange ”beställa en platt'.
2. Klicka på poäng åtgärd.
3. Klicka för att väljer 'vad du vill på din pizza ”?
4. Ange ”svamp och ost”.
5. Klicka på poäng åtgärder.
3. Klicka på ”du har $Toppings på din pizza'.
6. Välj ”vill du något annat”?
7. Ange ”ta bort svamp och lägga till peppers”.
    - Välj svamp och avmarkera Toppings entiteten. Vi skapar en åtgärd som vi kommer att ångra.
2. Klicka på Ångra steg.
    - Den sista posten tas bort och vi är tillbaka på ”vill du något annat”?  (på skärmbilden nedan)
2. Ange ”ta bort svamp och lägga till peppers”.
8. Klicka för att välja ”du har $Toppings på din pizza'
    - Kontrollera att båda entiteter är markerade på rätt sätt.
2. Klicka på poäng åtgärd. Du kan fortsätta med den korrigerade dialogrutan nu.
4. Klicka på klar undervisning.

Du har nu sett hur du använder Ångra om du vill ta bort en användare som indata och åtgärd.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Gren

Till exempel vi öppnar en befintlig train-dialogruta och skapa en annan träna dialogruta av förgrening.

1. Klicka på Train-dialogrutor och sedan 'nya order' att öppna dialogrutan befintliga. 
2. Klicka på sist 'Nej' i dialogrutan (se skärmbilden nedan).
3. Klicka på grenen.
    - ”Nej” tas bort och hela dialogrutan hittills kopieras till en ny. 
    - Detta sparar du behöva ange föregående aktiverar för att utforska en ny ”gren” från och med nu.
1. Ange ”Ja”.
2. Klicka på poäng åtgärd.
3. Välj ”du har $Toppings på din pizza'.
6. Välj ”vill du något annat”?
7. Ange ”Nej”.
4. Klicka på klar undervisning.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Versionshantering och taggning](./16-versioning-and-tagging.md)
