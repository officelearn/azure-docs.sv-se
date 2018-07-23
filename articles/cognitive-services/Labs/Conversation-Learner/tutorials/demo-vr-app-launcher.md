---
title: Demonstrera Konversationsdeltagare modeller, virtuell verklighet appstartaren - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en demo Konversationsdeltagare modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 896ec007c03e30e5c20a5344430be040271bc00b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171158"
---
# <a name="demo-virtual-reality-app-launcher"></a>Demo: Virtuella verklighet appstartaren

Den här demonstrationen visar en virtuell verklighet modellen starta som har stöd för kommandon, till exempel ”starta Skype och placera i på väggen”. En användare behöver att säga ett namn och plats för att starta appen. Start av modellen hanteras av ett API-anrop. När ett appnamn känns från användaren, kontrollerar entityDetectionCallback om begärda appen matchar en eller flera appar i listan över installerade appar. Den hanterar fallet där begärda appen inte har installerats, och där appnamnet är tvetydig (matchar fler än en installerad app).

## <a name="video"></a>Video

[![Demo VR-Appförhandsvisning](http://aka.ms/cl-demo-vrapp-preview)](http://aka.ms/blis-demo-vrapp)

## <a name="requirements"></a>Krav

Den här självstudien krävs att VRAppLauncher bot körs:

    npm run demo-vrapp
    
### <a name="open-the-demo"></a>Öppna demon

Klicka på VRAppLauncher i listan modell av webbgränssnittet. 

## <a name="entities"></a>Entiteter

Vi har skapat fyra entiteter:

- Programnamn: till exempel Skype
- PlacementLocation: för exempel väggen
- UnknownAppName: en programmatisk entitet som systemet anger när den inte kan identifiera ett entitetsnamn användaren säger, till exempel eftersom den inte har installerats.
- DisAmbigAppNames: en matris med två eller flera installerad app-namn som matchar vad användaren SA. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Åtgärder

Vi har skapat en uppsättning åtgärder som omfattar ett API som kallas LaunchApp, vilket startar funktionsanrop för att starta en app.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Dialogrutor för utbildning
Vi har definierat ett antal utbildning dialogrutor.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

Till exempel nu ska vi prova en undervisning-session.

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
1. Ange ”Hej”.
2. Klicka på poäng åtgärd.
3. Klicka för att markera ”vilken app du vill starta”?
4. Ange ”outlook”.
    - LUIS identifierar den som en entitet.
5. Klicka på poäng åtgärder.
3. Klicka för att välja ”var du vill placera den”?
4. Ange ”på väggen'.
    - LUIS identifierar den som en PlacementLocation.
2. Ange poäng åtgärder.
6. Välj ”LaunchApp”
7. System: 'startar outlook på väggen'.
    - Detta utlöses ett API-anrop. Koden för det här anropet är på C:\<\installedpath > \src\demos\demoVRAppLauncher.ts. Men innehåller den inte faktiskt koden för att starta Outlook för den här demon.
    - Det tar bort AppName och PlacementLocation entiteter. Returnerar strängen ovan som svar.
4. Klicka på klar undervisning.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Låt oss börja en annan utbildningssessionen till hantering av okänd och tvetydig entiteter.

1. Klicka på dialogrutan för nytt träna.
1. Ange ”starta OneNote”. 
    - Modellen identifierar OneNote som ett appnamn. Den `EntityDetectionCallback` funktion som definierats i koden matchar namnet som angetts av användaren till ett appnamn som matchar den app-lista som definierats i koden. Den returnerar sedan uppsättningen med alla matchande appar. 
    - Om listan över matchningar är noll, det innebär att appen inte har installerats. Placeras den i unknownAppName.
    - Om den hittar fler än en app, kopierar dem till den `DisambigAppNames` och rensar AppName-entiteten.
2. Klicka på poäng åtgärd.
3. Klicka för att välja ”tyvärr jag inte vet appen $UknownAppName”.
4. Ange ”starta Amazon”. Vi kommer att testa andra sökvägen.
5. Klicka på poäng åtgärder.
    - Amazon Video och musik Amazon finns nu i `DisambigAppNames` minne och OneNote har rensats.
3. Klicka för att markera det finns några appar som låter som den...
    - Poängen är inte mycket hög eftersom vi bara har definierat några utbildning dialogrutor i det här läget. Definiera mer utbildning dialogrutor säkerställer modellen mer avgörande.
2. Ange poäng åtgärder.
4. Klicka på klar undervisning.

Du har nu sett hur du gör entitet lösning. Demon också illustreras API återanrop och visade en mall för att samla in information, söker efter förekomst och tvetydighet och vidta rätt åtgärd i enlighet med detta.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera en robot som Konversationsdeltagare](../deploy-to-bf.md)
