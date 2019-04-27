---
title: Hur du använder API-anrop med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder API-anrop med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: aba3c2eb925370704ea52364891502a7a09cc9ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635759"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Hur du lägger till API-anrop till en modell för Konversationsdeltagare

Den här självstudiekursen visar hur du lägger till API-anrop till din modell. API-anrop är funktioner som du definierar och skriva i din robot, och som kan anropa Konversationsdeltagare.

## <a name="video"></a>Video

[![Självstudiekursen förhandsversionen av API-anrop](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Krav
Den här självstudien krävs att ”tutorialAPICalls.ts” bot körs.

    npm run tutorial-api-calls

## <a name="details"></a>Information

- API-anrop kan läsa och ändra entiteter.
- API-anrop har åtkomst till objekt i minnet.
- API-anrop kan ta argument.

### <a name="open-the-demo"></a>Öppna demon

Klicka på ”Importera Tutorials” och välja modellen med namnet ”självstudien-14-APICalls” i webbgränssnittet.

### <a name="entities"></a>Entiteter

Vi har definierat en entitet i modellen kallas `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-anrop
Koden för API-anrop är definierad i den här filen: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- Den `RandomGreeting` återanrop returnerar ett slumpmässigt hälsning som definierats i den `greeting` matris.
- Den `Multiply` återanrop kommer multiplicerar två tal som skickas av den åtgärd som anropar den och returnerar ett resultat kan återges i Användargränssnittet.
    - Observera att minnet manager är det första argumentet. 
    - Observera att API-återanrop kan ta flera inmatningar i det här fallet `num1string` och `num2string`.
- Den `ClearEntities` återanrop tar bort entiteten nummer så att användaren kan ange ett annat nummer. 
    - Visar hur API-anrop kan ändra entiteter.

### <a name="actions"></a>Åtgärder
Vi har skapat fyra åtgärder. Tre av dem är ”icke-Wait” API-åtgärder, med fjärde är en ”Text”-åtgärd som uppmanar användaren en fråga som liknar vad vi har sett i andra självstudier. Se hur var och en har skapats gör du följande:
1. I den vänstra rutan klickar du på ”åtgärder” och klicka på någon av de fyra åtgärder som visas i rutnätet.
2. Lägg märke till värdena för varje fält i formuläret som visas.
3. Observera den `Refresh` knappen bredvid fältet API.
    - Om vi ska stoppa roboten och gör att ändra till API: erna medan sidan, kan du klicka på den `Refresh` knappen för att hämta de senaste ändringarna.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, multiplicera och RandomGreeting
Alla tre av dessa åtgärder är API-typen. De är beroende av de API-återanropsfunktionerna att utföra visst arbete och möjligen returnera ett värde som ska visas för användaren.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>”Vilket nummer vill du multiplicera med 12”
Detta är den ”Text”-åtgärden och det helt enkelt ställer en fråga för användaren. Även om den här åtgärden faktiskt inte samverka med en av API-återanrop, uppmanas användaren att svara med ett tal som hamnar i minnet för en entitet som sedan kan användas för ”multiplicera” åtgärden som använder en av API-återanrop.


### <a name="train-dialog"></a>Träna dialogrutan

Låt oss gå igenom en ”utbildning dialogruta”.

1. Klicka på den vänstra panelen `Train Dialogs`, kommer `New Train Dialog` knappen.
2. Skriv ”hello”.
3. Klicka på knappen `Score Actions`.
4. Välj `RandomGreeting`. 
    - På detta vis körs slumpmässigt hälsning API-anrop.
    - Detta väntar inte på ett användarsvar.
5. Välj `What number to do you want to multiply by 12?`
6. Ange ett tal, ett tal och bara ett tal.
    - Lägg märke till att din etiketterades automatiskt som den `number` entitet.
7. Klicka på knappen `Score Actions`.
8. Välj den `Multiply` åtgärd.
    - Observera resultatet av multiplikationen av 12.
    - Observera att minnet fortfarande innehåller värdet som du angav för `number`
9. Välj den `ClearEntities` åtgärd.
    - Lägg märke till att entiteten värde för `number` har det tagits bort från minnet.
10. Klicka på knappen `Save`.

Du har nu sett hur du registrerar API återanrop sina vanliga mönster och hur du definierar argument och koppla värden och entiteter i dem.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kort del 1](./15-cards.md)
