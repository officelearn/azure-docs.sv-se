---
title: Hur du använder Använd API-anrop med ett samtal deltagaren program - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om att använda API-anrop för användning med en konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ec752cbadfac7a47e08ed7b0ffe8bb475969fac5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354036"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-application"></a>Hur du lägger till API-anrop till en konversation deltagaren program

Den här kursen visar hur du lägger till API-anrop till ditt program. API-anrop kan funktioner som du definierar och skriva i dina bot och som kan anropa konversation deltagaren.

## <a name="requirements"></a>Krav
Den här kursen kräver att ”tutorialAPICalls.ts” bot körs.

    npm run tutorial-api-calls

## <a name="details"></a>Information

- API-anrop kan läsa och ändra entiteter.
- API-anrop har åtkomst till objekt i minnet.
- API-anrop kan också dra argument – detta gör att återanvända samma API-anropet till tjänar olika syften.

### <a name="open-the-demo"></a>Öppna demonstrationen

Klicka på kursen-12-APICalls i App-lista över webbgränssnittet. 

### <a name="entities"></a>Entiteter

Vi har definierat en entitet i programmet kallas nummer.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-anrop
Koden för API-anrop har definierats i den här filen: C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- Första API återanropet är RandomGreeting. Returnerar en slumpmässig hälsning som definierats i variabeln hälsning.
- Multiplicera API-motringning: det kommer multiplicera två siffror som anges av användaren. Den returnerar sedan resultatet av multiplikationen av de två talen. Det visar att API-återanrop ta indata. Observera att minneshanteraren är det första argumentet. 
- ClearEntities API-motringning: rensar nummer entiteten så att användaren kan ange nästa nummer. Detta visar hur API-anrop kan ändra entiteter.

### <a name="actions"></a>Åtgärder

Vi har skapat fyra åtgärder. 

![](../media/tutorial12_actions.PNG)

- Förutom 'vilket nummer vill du att multiplicera med 12 ”? vilket är en communicative åtgärd finns det tre olika API-anrop som illustrerar de vanliga API-anrop mönster.

- RandomGreeting: är en icke-wait-åtgärd. Om du vill ställa in, i dialogrutan Skapa åtgärd, vi valt åtgärden typ av API_LOCAL och valt RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

Uppdatera-knappen bredvid API: et används om vi ska stoppa bot och göra ändringar i de API: er. Klicka på Uppdatera skulle hämtar de senaste ändringarna.

Här är hur man skapar den multiplicera åtgärd: när du har valt API_Local och API, vi angav en entitet ($number) för det första indatavärdet (num1string) och ett värde (12) för andra indatavärdet (num2string). Detta ger en nivå av trick mellan bot och API-anrop så samma återanropet kan mappas till några åtgärder i systemet och skiljer sig på hur åtgärderna som har tilldelats.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Train dialogrutan

Låt oss gå igenom en lärare dialogruta.

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
1. Ange ”Hej”.
2. Klicka på poäng åtgärd.
3. Klicka på RandomGreeting. Detta kommer att köras slumpmässiga hälsning API-anrop.
3. Markera 'vilket nummer vill vill du att multiplicera med 12 ”?
4. Ange ”8”. Klicka på poäng åtgärder.
4. Välj ' multiplicera $number 12'. Observera resultatet av multiplikationen.
5. Välj ”Rensa enheter”.
    - Observera att värdet för antalet entiteten har rensats.
3. Markera 'vilket nummer vill vill du att multiplicera med 12 ”?
4. Klicka på klart testning.

![](../media/tutorial12_dialog.PNG)

Nu har du sett hur du registrerar API återanrop sina vanliga mönster och hur du definierar argument och koppla värden och enheter i dem.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kort del 1](./13-cards-1.md)
