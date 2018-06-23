---
title: Demonstrera konversation deltagaren program, virtuella verkligheten app programstart - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en demonstration konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3e41125bf7da9ee64d666d22cb275af01af54012
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354072"
---
# <a name="demo-virtual-reality-app-launcher"></a>Demo: Virtuella verkligheten app programstart

Den här demon illustrerar en virtuell verkligheten startprogrammet stöder kommandon som ”starta Skype och placera vägguttaget”. En användare behöver till en appnamn och plats för att starta appen. Starta appen hanteras av en API-anrop. När en appnamnet känns från användaren, kontrollerar entityDetectionCallback om appen begärda matchar en eller flera appar i listan över installerade appar. Den hanterar fall där begärda appen inte installeras och namnet på appen är tvetydig (matchar flera installerade appen).

## <a name="requirements"></a>Krav

Den här kursen krävs att VRAppLauncher bot körs

    npm run demo-vrapp

### <a name="open-the-demo"></a>Öppna demonstrationen

Klicka på VRAppLauncher i App-lista över webbgränssnittet. 

## <a name="entities"></a>Entiteter

Vi har skapat fyra enheter:

- Programnamn: till exempel Skype
- PlacementLocation: för exempel brandvägg
- UnknownAppName: en programmässiga entitet som systemet anger när den inte kan identifiera entitetsnamnet användaren säger, till exempel eftersom den inte har installerats.
- DisAmbigAppNames: en matris med två eller flera installerade appen namn som matchar användaren SA. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Åtgärder

Vi har skapat en uppsättning åtgärder som innehåller en API som kallas LaunchApp, vilket startar funktionsanropet att starta en app.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Dialogrutor för utbildning
Vi har definierat ett antal utbildning dialogrutor.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

T.ex, prova med en lärare session.

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
1. Ange ”Hej”.
2. Klicka på poäng åtgärd.
3. Klicka om du vill markera 'vilken app vill du starta ”?
4. Ange 'outlook'.
    - Observera att THOMAS identifieras som en enhet.
5. Klicka på poäng åtgärder.
3. Markera 'var du vill placera den ”?
4. Ange 'på brandvägg'.
    - Observera att THOMAS identifieras som en PlacementLocation.
2. Ange poäng åtgärder.
6. Välj 'LaunchApp'
7. System: 'startar outlook på vägguttaget'.
    - Observera att detta utlöste ett API-anrop. Koden för det här anropet är på C:\<\installedpath > \src\demos\demoVRAppLauncher.ts. Det har inte faktiskt logik för att starta outlook för den här demon men.
    - Det tar bort AppName och PlacementLocation entiteter. Returnerar strängen ovan som svar.
4. Klicka på klar lärare.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Vi börjar en annan utbildningen för hantering av okända och tvetydig entiteter.

1. Klicka på ny Train-dialogruta.
1. Ange ”start OneNote”. 
    - Den identifieras som ett namn för appen eftersom EntityDetectionCallback har definierats i koden tar namn användaren har angett och motsvarar det ett namn som matchar app-lista som definierats i koden. Den returnerar sedan mängd med alla matchande appar. 
    - Om listan är noll, betyder appen inte installeras. Placeras den i unknownAppName.
    - Om den hittar fler än en app den kopierar du dem till DisambigAppNames och avmarkera AppName entiteten.
2. Klicka på poäng åtgärd.
3. Markera 'Tyvärr inte vet appen $UknownAppName ”.
4. Ange ”start amazon”. Vi prova en annan sökväg.
5. Klicka på poäng åtgärder.
    - Obs Amazon Video och musik Amazon är nu i DisambigAppNames minne. Och OneNote har rensats.
3. Klicka om du vill markera det finns några appar som låter som de som...
    - Observera poängsättningen inte mycket hög eftersom vi har bara några få utbildning dialogrutor i det här läget. Ser ut som om vi behöver några mer här Se modellen mer avgörande.
2. Ange poäng åtgärder.
4. Klicka på klar lärare.

Nu har du sett hur du gör entitet lösning. Demonstrationen också visas API återanrop och visade en mall för att samla in information, kontroll av förekomst och tvetydighet och åtgärda den högra baserat på.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera en konversation deltagaren bot](../deploy-to-bf.md)
