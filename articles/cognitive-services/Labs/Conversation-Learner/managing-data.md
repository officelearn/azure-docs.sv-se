---
title: Hantera användar data med Conversation Learner-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du hanterar användar data med Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 857e899764d284e2d78f1172fa8eeac04c57d618
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705202"
---
# <a name="managing-user-data"></a>Hantera användar data

Den här sidan beskriver vad Conversation Learner moln tjänsten loggar när du utför dialoger med slutanvändare.  Det beskriver också hur du associerar Conversation Learner loggar med användar-ID: n, så att du kan hämta eller ta bort alla loggar som är kopplade till en viss användare.

## <a name="overview-of-end-user-data-logging"></a>Översikt över loggning av slut användar data

Som standard loggar Conversation Learner moln tjänsten interaktioner mellan slutanvändare och din robot.  Dessa loggar är viktiga för att förbättra din robot, så att du kan identifiera fall där din robot extraherade felaktig entitet eller valt felaktig åtgärd.  Felen kan sedan korrigeras genom att gå till sidan "Logga dialoger" i användar gränssnittet, göra ändringar och lagra den korrigerade dialog rutan som en ny träna-dialog ruta. Mer information finns i självstudierna i "Logga dialoger".

## <a name="how-to-disable-logging"></a>Så här inaktiverar du loggning

Du kan kontrol lera om konversationer med slutanvändare finns på sidan Inställningar för din Conversation Learners modell.  Det finns en kryss ruta för "logg konversationer".  Genom att avmarkera den här rutan loggas inte konversationer med slutanvändare.

## <a name="what-is-logged"></a>Vad är loggat 

I dialog rutor i loggar Conversation Learner lagrar användarindata, enhets värden, valda åtgärder och tidsstämplar för varje tur.  Dessa loggar lagras under en viss tids period och ignoreras sedan (se hjälp sidan för "standardvärde och gränser" för information).  

Conversation Learner skapar ett unikt ID för varje loggad dialog ruta.  Conversation Learner lagrar *inte* en användar identifierare med loggade dialog rutor.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Associera loggade dialog rutor med ett användar-ID

Det är ofta viktigt att kunna associera loggade dialog rutor med ID: t för användaren, till exempel för att kunna hämta eller ta bort Inloggade dialog rutor från en viss användare.  Eftersom Conversation Learner inte lagrar en användar identifierare måste den här associationen upprätthållas av utvecklarens kod.  

Om du vill skapa den här mappningen hämtar du ID: t `EntityDetectionCallback`för den loggade dialog rutan i. sedan kan du lagra associationen mellan användar-ID och den här loggade dialog rutan i robotens lagring.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>Rubriker för HTTP-anrop

I vart och ett av HTTP-anropen nedan lägger du till följande huvud:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

där `<LUIS_AUTHORING_KEY>` är redigerings nyckeln för Luis som används för att komma åt dina Conversation Learner-program.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Hämta rå data för en loggad dialog ruta

Du kan använda detta HTTP-anrop för att hämta rå data för en logg dialog ruta:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Där `<appId>` är GUID för den här Conversation Learner modellen och `<logDialgoId>` är ID: t för logg dialog rutan som du vill hämta.  

> [!NOTE]
> Logg dialog rutor kan redige ras av utvecklaren och sedan lagras som träna dialog rutor.  När detta är slutfört lagrar Conversation Learner ID: t för "källa"-logg dialog rutan med träna-dialog rutan.  Dessutom kan en träna-dialog ruta bli "branchd" i användar gränssnittet. om en träna-dialog ruta har ett associerat dialog-ID för käll loggen, markeras grenar från den träna-dialog rutan med samma logg dialog-ID.

Följ dessa steg om du vill hämta alla träna dialog rutor som har härletts från en logg dialog.

Börja med att hämta alla träna dialog rutor:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Där `<appId>` är GUID för den här Conversation learners modellen.  

Detta returnerar alla träna-dialog rutor.  Sök i den här listan efter `sourceLogDialogId`associerade och anteckna associerade. `trainDialogId` 

Till en enda träna-dialog efter ID:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Där `<appId>` är GUID för den här Conversation Learner modellen och `<trainDialogId>` är ID: t för den träna-dialog som du vill hämta.  

## <a name="how-to-delete-a-logged-dialog"></a>Så här tar du bort en loggad dialog ruta

Om du vill ta bort en logg dialog ruta med ett ID kan du använda HTTP-anropet:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Där `<appId>` är GUID för den här Conversation Learner modellen och `<logDialogId>` är ID: t för logg dialog rutan som du vill ta bort. 

Om du vill ta bort en dialog ruta för ett tränat ID kan du använda HTTP-anropet:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Där `<appId>` är GUID för den här Conversation Learner modellen och `<trainDialogId>` är ID: t för den träna-dialog som du vill ta bort. 
