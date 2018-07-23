---
title: Hur du använder Använd API-anrop med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder API-anrop för användning med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: d16f36a70ac176a895d9ba44d42d3fae5730f7ea
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173272"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Hur du lägger till API-anrop till en modell för Konversationsdeltagare

Den här självstudiekursen visar hur du lägger till API-anrop till din modell. API-anrop är funktioner som du definierar och skriva i din robot, och som kan anropa Konversationsdeltagare.

## <a name="video"></a>Video

[![Förhandsversion av självstudien 12](http://aka.ms/cl-tutorial-12-preview)](http://aka.ms/blis-tutorial-12)

## <a name="requirements"></a>Krav
Den här självstudien krävs att ”tutorialAPICalls.ts” bot körs.

    npm run tutorial-api-calls

## <a name="details"></a>Information

- API-anrop kan läsa och ändra entiteter.
- API-anrop har åtkomst till objekt i minnet.
- API-anrop kan också dra argument – på så sätt kan återanvända samma API-anropet till har olika syften.

### <a name="open-the-demo"></a>Öppna demon

Klicka på självstudien-12-APICalls i listan modell av webbgränssnittet. 

### <a name="entities"></a>Entiteter

Vi har definierat en entitet i modellen kallas tal.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-anrop
Koden för API-anrop är definierad i den här filen: C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- Det första API-återanrop är RandomGreeting. Den returnerar ett slumpmässigt hälsning som definierats i variabeln hälsning.
- Multiplicera API-motringning: det kommer multiplicerar två tal som anges av användaren. Den returnerar sedan resultatet av multiplikationen av de två talen. Detta visar att API-återanrop kan ta indata. Observera att minnet manager är det första argumentet. 
- ClearEntities API-motringning: tar bort entiteten nummer så att användaren kan ange närmast. Detta visar hur API-anrop kan ändra entiteter.

### <a name="actions"></a>Åtgärder

Vi har skapat fyra åtgärder. 

![](../media/tutorial12_actions.PNG)

- Förutom ”vilket nummer vill du multiplicera med 12”? vilket är en communicative åtgärd finns det tre olika API-anrop som illustrerar de vanliga mönster som API-anrop.

- RandomGreeting: är en icke-wait-åtgärden. Om du vill ställa in detta, i dialogrutan Skapa åtgärd vi valt åtgärd typ av API_LOCAL och valt RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

Uppdatera-knappen bredvid API: et används om vi ska stoppa roboten och göra ändringar i API: erna. När du klickar på Uppdatera skulle över de senaste ändringarna.

Här är hur man skapar den åtgärden att multiplicera: när du har valt API_Local och API: et, vi angav en entitet ($number) för det första indatavärdet (num1string) och ett värde (12) för andra indatavärdet (num2string). Detta ger en nivå av trick mellan roboten och API-anrop så att samma motringningen kan mappas till några åtgärder i systemet och de skiljer sig åt på hur åtgärderna som är tilldelade.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Träna dialogrutan

Låt oss gå igenom en undervisning dialogruta.

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
1. Ange ”Hej”.
2. Klicka på poäng åtgärd.
3. Klicka för att välja RandomGreeting. På detta vis körs slumpmässigt hälsning API-anrop.
3. Klicka för att välja ”vilket nummer du vill du multiplicera med 12”?
4. Ange ”8”. Klicka sedan på poäng åtgärder.
4. Välj ”multiplicera $number 12'. Observera resultatet av multiplikationen.
5. Välj ”Rensa enheter”.
    - Den `number` entitets-värdet har rensats.
3. Klicka för att välja ”vilket nummer du vill du multiplicera med 12”?
4. Klicka på klar testning.

![](../media/tutorial12_dialog.PNG)

Du har nu sett hur du registrerar API återanrop sina vanliga mönster och hur du definierar argument och koppla värden och entiteter i dem.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kort del 1](./13-cards-1.md)
