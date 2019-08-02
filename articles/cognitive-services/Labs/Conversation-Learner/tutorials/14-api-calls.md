---
title: Använda API-anrop med en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder API-anrop med en Conversation Learner-modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 261536932cc82a28ad4ee3ffc3575ea41fe9ec5b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703912"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Så här lägger du till API-anrop till en Conversation Learner modell

I den här självstudien visas hur du lägger till API-anrop till din modell. API-anrop är funktioner som du definierar och skriver i din robot och som Conversation Learner kan anropa.

## <a name="video"></a>Video

[![För hands version av API-anrop](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Krav
I den här självstudien krävs att roboten "tutorialAPICalls. TS" körs.

    npm run tutorial-api-calls

## <a name="details"></a>Information

- API-anrop kan läsa och ändra entiteter.
- API-anrop har åtkomst till objektet för minnes hanteraren.
- API-anrop kan ta argument.

### <a name="open-the-demo"></a>Öppna demon

I webb gränssnittet klickar du på "Importera självstudier" och väljer modellen med namnet "självstudie-14-APICalls".

### <a name="entities"></a>Entiteter

Vi har definierat en entitet i modellen som kallas `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-anrop
Koden för API-anropen definieras i den här filen: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- Återanropet returnerar en slumpmässig hälsning `greeting` som definierats i matrisen. `RandomGreeting`
- `Multiply` Återanropet multiplicerar två tal som skickas av den åtgärd som anropar det och returnerar ett resultat som kan återges i användar gränssnittet.
    - Observera att minnes hanteraren är det första argumentet. 
    - Observera att API-återanrop kan ta flera indata, i `num1string` det `num2string`här fallet och.
- `ClearEntities` Återanropet rensar antalet enheter så att användaren kan ange ett annat tal. 
    - Visar hur API-anrop kan manipulera entiteter.

### <a name="actions"></a>Åtgärder
Vi har skapat fyra åtgärder. Tre av dem är "icke-väntande" API-åtgärder, med den fjärde är en "text"-åtgärd som ber användaren om en fråga som liknar vad vi har sett i andra självstudier. Gör så här om du vill se hur de skapades:
1. På den vänstra panelen klickar du på åtgärder och sedan på någon av de fyra åtgärder som anges i rutnätet.
2. Observera värdena för varje fält i formuläret som öppnas.
3. Lägg märke `Refresh` till knappen bredvid API-fältet.
    - Om vi skulle stoppa bot-roboten och göra ändringar i API: erna medan UI-sidan är upp kan du klicka `Refresh` på knappen för att hämta de senaste ändringarna.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, multiplicering och RandomGreeting
Alla tre av dessa åtgärder är API-typ. De förlitar sig på API-callback-funktionerna för att utföra en del arbete och eventuellt returnera ett värde som visas för användaren.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Vilken siffra vill du multiplicera med 12"
Det här är åtgärden "text" och ställer bara frågan till användaren. Även om den här åtgärden inte interagerar med en av API-återanropen, uppmanas användaren att svara med ett nummer som går in i minnet för en entitet som sedan kan användas av åtgärden "multiplicera" som använder något av API-återanropen.


### <a name="train-dialog"></a>Träna dialog

Låt oss gå igenom en "tränings dialog".

1. Klicka `Train Dialogs`på i den vänstra panelen och `New Train Dialog` sedan på knappen.
2. Skriv "Hej".
3. Klicka på knappen `Score Actions`.
4. Välj `RandomGreeting`. 
    - Detta kommer att köra slumpmässiga API-anrop för hälsning.
    - Detta kommer inte att vänta på ett användar svar.
5. Välj `What number to do you want to multiply by 12?`
6. Skriv ett tal, alla siffror och bara ett tal.
    - Observera att ditt nummer automatiskt är märkt som `number` entiteten.
7. Klicka på knappen `Score Actions`.
8. `Multiply` Välj åtgärden.
    - Observera resultatet av multiplikationen med 12.
    - Observera att minnet fortfarande innehåller det värde som du angav för`number`
9. `ClearEntities` Välj åtgärden.
    - Observera att enhet svärdet för `number` har rensats från minnet.
10. Klicka på knappen `Save`.

Nu har du sett hur du registrerar API-återanrop, deras vanliga mönster och hur du definierar argument och associerar värden och entiteter i dem.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kort del 1](./15-cards.md)
