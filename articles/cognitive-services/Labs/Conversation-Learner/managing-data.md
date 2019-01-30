---
title: Hantera användardata med Konversationsdeltagare - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du hanterar användardata med Konversationsdeltagare.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1d3687585a663cf937374e76dafe834c641ad3a2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215290"
---
# <a name="managing-user-data"></a>Hantera användardata

Den här sidan beskriver vilka Molntjänsten Konversationsdeltagare loggar vid dialogrutor med slutanvändare.  Det beskriver också hur du associerar Konversationsdeltagare loggar med användar-ID, så att du kan hämta eller ta bort alla loggar som är associerade med en viss användare.

## <a name="overview-of-end-user-data-logging"></a>Översikt över loggning av data för slutanvändare

Som standard loggar Molntjänsten Konversationsdeltagare samverkan mellan användare och din robot.  Dessa loggar är viktiga för att förbättra din robot, så att du kan identifiera fall där din robot extraherade felaktig entitet eller valt felaktig åtgärd.  Dessa fel kan sedan korrigeras genom att gå till sidan ”Log-dialogrutor” i Användargränssnittet, att göra korrigeringar och lagra den här korrigerad dialogrutan som en ny train-dialogruta. Mer information finns i självstudiekursen på ”Log dialogrutor”.

## <a name="how-to-disable-logging"></a>Så här inaktiverar loggning

Du kan styra om konversationer med slutanvändare finns på sidan ”Inställningar” för din Konversationsdeltagare-modell.  Det finns en kryssruta för ”Log konversationer”.  Genom att avmarkera den här kryssrutan, loggas inte konversationer med användare.

## <a name="what-is-logged"></a>Vad är inloggad 

I loggen dialogrutor lagrar Konversationsdeltagare indata från användaren, entitetsvärden, valda åtgärder och tidsstämplar för varje gång.  Dessa loggar lagras under en viss tidsperiod, och sedan tas bort (finns på hjälpsidan på ”standardvärdet och gränser” mer information).  

Konversationsdeltagare skapar ett unikt ID för varje loggade dialogrutan.  Konversationsdeltagare har *inte* lagra användar-ID med loggade dialogrutor.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Associera loggas dialogrutor med ett användar-ID

Ofta är det viktigt att du kan associera loggade dialogrutor med ID för användaren – till exempel, för att kunna hämta eller ta bort loggade dialogrutor från en viss användare.  Eftersom Konversationsdeltagare inte lagrar användar-ID, måste den här associationen utvecklarens kod ska behållas.  

Hämta ID i dialogrutan loggas i för att skapa den här mappningen `EntityDetectionCallback`; sedan lagrar associationen mellan användar-ID och den här dialogrutan som loggas i din robot-lagring.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific datastore, store an association
    // bewteen your user identifier and this session ID.
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

Lägg till följande huvud i var och en av HTTP-anrop nedan:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

där `<LUIS_AUTHORING_KEY>` är LUIS redigering av nyckel som används för att få åtkomst till dina konversationen Learner program.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Hur du skaffar rådata för en loggade dialogruta

Du kan använda den här HTTP-anrop för att hämta rådata för en logg-dialogruta:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Där `<appId>` är GUID för den här Konversationsdeltagare-modellen och `<logDialgoId>` är ID för log-dialogrutan som du vill hämta.  

> [!NOTE]
> Log dialogrutor kan redigeras av utvecklaren och sedan lagras som tränar dialogrutor.  När det är klart, lagrar Konversationsdeltagare ID för ”källa” log dialogen med train-dialogrutan.  Dessutom kan kan en dialogruta för träna vara ”förgrenade” i Användargränssnittet för; Om en dialogruta för träna har en associerad källa log dialog-ID, kommer grenar från dialogrutan träna markeras med samma log dialogrutan ID.

Följ dessa steg om du vill hämta alla träna dialogrutor som skapats från en log-dialogruta.

Först hämta alla träna dialogrutor:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Där `<appId>` är GUID för den här Konversationsdeltagare-modellen.  

Det här returnerar alla träna dialogrutor.  Sök efter den här listan för den associerade `sourceLogDialogId`, och notera den associerade `trainDialogId`. 

Träna dialogrutan efter ID till en enda:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Där `<appId>` är GUID för den här Konversationsdeltagare-modellen och `<trainDialogId>` är ID för dialogrutan träna som du vill hämta.  

## <a name="how-to-delete-a-logged-dialog"></a>Hur du tar bort en loggade dialogruta

Om du vill ta bort en log-dialogruta beroende dess ID, kan du använda den här HTTP-anrop:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Där `<appId>` är GUID för den här Konversationsdeltagare-modellen och `<logDialogId>` är ID för log-dialogrutan som du vill ta bort. 

Om du vill ta bort en träna dialogruta beroende dess ID, kan du använda den här HTTP-anrop:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Där `<appId>` är GUID för den här Konversationsdeltagare-modellen och `<trainDialogId>` är ID för dialogrutan träna som du vill ta bort. 
