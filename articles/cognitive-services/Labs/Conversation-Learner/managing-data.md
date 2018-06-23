---
title: Hantera användardata med konversation deltagaren - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om att hantera användardata med konversation deltagaren.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8d42f903559a1e07b42ded33972be4b552f21b5e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353976"
---
# <a name="managing-user-data"></a>Hantera användardata

Den här sidan beskrivs vad Molntjänsten konversation deltagaren loggar vid dialogrutor med slutanvändare.  Det beskriver också hur du associerar konversation deltagaren loggar med användar-ID, så att du kan hämta eller ta bort alla loggar som är associerade med en viss användare.

## <a name="overview-of-end-user-data-logging"></a>Översikt över loggning av data för licensavtalet

Som standard loggas Molntjänsten konversation deltagaren samverkan mellan användare och din bot.  Dessa loggar är viktiga för att förbättra din bot så att du kan identifiera fall där din bot extraherade felaktig entiteten eller valda felaktig åtgärd.  Dessa fel kan sedan korrigeras genom att gå till sidan ”Log-dialogrutor” i Användargränssnittet, att göra korrigeringar och lagra den här korrigerade dialogrutan som en ny train-dialogruta. Mer information finns i kursen på ”loggen dialoger”.

## <a name="how-to-disable-logging"></a>Inaktivera loggning

Du kan styra om konversationer med slutanvändare finns på sidan ”Inställningar” för tillämpningsprogrammet konversation deltagaren.  Det finns en kryssruta för ”loggen konversationer”.  Genom att avmarkera den här kryssrutan loggas konversationer med slutanvändare inte.

## <a name="what-is-logged"></a>Vad är inloggad 

I loggen dialoger lagrar konversation deltagaren indata från användaren, enheten värden, valda åtgärder och tidsstämplar för varje gång.  Dessa loggar lagras för en viss tidsperiod, och sedan tas bort (se hjälpsidan på ”standardvärdet och gränser” mer information).  

Konversationen deltagaren skapar ett unikt ID för varje loggade dialogrutan.  Konversationen deltagaren har *inte* lagra användar-ID med loggade dialogrutor.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Associera loggat dialogrutor med ett användar-ID

Ofta är det viktigt att du kan associera loggade dialogrutor med ID för användare – till exempel, för att kunna hämta eller ta bort loggade dialogrutor från en viss användare.  Eftersom Konversations deltagaren inte lagrar användar-ID, måste den här associationen utvecklarens kod ska behållas.  

Om du vill skapa den här mappningen hämta ID för dialogrutan loggas i `EntityDetectionCallback`; sedan i din bot storage lagrar associationen mellan användar-ID och den här loggade dialogrutan.  

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

## <a name="headers-for-http-calls"></a>Sidhuvuden för HTTP-anrop

Lägg till följande huvud i var och en av HTTP-anrop nedan:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

där `<LUIS_AUTHORING_KEY>` är THOMAS redigering nyckel som används för att komma åt dina konversation deltagaren program.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Hur du skaffar rådata för loggade dialogrutan

Du kan använda HTTP-anropet för att erhålla rådata för en logg dialogrutan:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Där `<appId>` är GUID för den här konversationen deltagaren program och `<logDialgoId>` är ID för dialogrutan loggen som ska hämtas.  

Observera att loggen dialogrutor kan redigeras av utvecklaren och lagras som träna dialogrutor.  När det är klart lagrar konversation deltagaren ID i dialogrutan ”källa” logg med train dialogrutan.  Dessutom kan en train-dialogruta vara ”steg” i Användargränssnittet; Om en train dialogrutan har en associerad loggen dialogrutan-ID för meddelandekälla, sedan markeras filialer från den train dialogrutan med samma loggen dialogrutan ID.

Följ dessa steg för att hämta alla train-dialogrutor som har härletts från en logg-dialogruta.

Först hämta alla train dialogrutor:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Där `<appId>` är GUID för den här konversationen deltagaren program.  

Detta returnerar alla train dialogrutor.  Söklistan för den associerade `sourceLogDialogId`, och notera den associerade `trainDialogId`. 

För en enskild träna dialog med ID:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Där `<appId>` är GUID för den här konversationen deltagaren program och `<trainDialogId>` är ID för dialogrutan train som du vill hämta.  

## <a name="how-to-delete-a-logged-dialog"></a>Hur du tar bort en loggade dialog

Du kan använda HTTP-anropet om du vill ta bort en logg dialogrutan angivna ID:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Där `<appId>` är GUID för den här konversationen deltagaren program och `<logDialogId>` är ID för dialogrutan logg som du vill ta bort. 

Du kan använda HTTP-anropet om du vill ta bort en train dialogrutan angivna ID:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Där `<appId>` är GUID för den här konversationen deltagaren program och `<trainDialogId>` är ID för dialogrutan train som du vill ta bort. 
