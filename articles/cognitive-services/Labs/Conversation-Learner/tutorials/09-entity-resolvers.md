---
title: Entiteten matchare i en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder entiteten matchare i Konversationsdeltagare.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cc1f9ea18d425679a4db9c7b91a1c0a3c4451d4b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796954"
---
# <a name="entity-resolvers"></a>Entiteten matchare

Den här kursen visar hur du använder entiteten matchare i Konversationsdeltagare

## <a name="video"></a>Video

[![Entiteten matchare självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

- Matchare typen är en valfri egenskap för anpassade entiteter.
- Entiteten matchare utnyttja kraften hos förtränade entitet identifierare i LUIS att ge ytterligare information och tydlighet för din anpassade entitet.

## <a name="steps"></a>Steg

### <a name="create-a-new-model"></a>Skapa en ny modell

1. Klicka på knappen ”ny modell” i Användargränssnittet för webben.
2. I fältet ”Name”, skriver du ”entitet matchare”, träffar ange eller klicka på knappen ”Skapa”.

### <a name="create-a-pair-of-entities"></a>Skapa ett par med entiteter

1. I den vänstra rutan klickar du på ”enheter” och sedan på knappen ”ny entitet”.
2. Skriv ”avgång” i fältet ”entitetsnamn”.
3. Välj ”datetimeV2” i ”matchare Type” listrutan.
4. Klicka på knappen ”Skapa”.
5. Klicka på ”OK” när du har läst informationen popup-fönstret.
6. Skapa en annan entitet med namnet ”return” som också har en ”datetimeV2” matchare typ följa samma steg.

### <a name="create-a-pair-of-actions"></a>Skapa ett par åtgärder

1. I den vänstra rutan klickar du på ”åtgärder” och sedan på knappen ”ny åtgärd”.
2. I ”Robotens svaret” fälttyp ”du lämnar på $departure och returnera på $return”.
    - VIKTIGT - när att skriva in ”$[entityName]” måste du når anger eller klicka på entiteten i listrutan, annars Konversationsdeltagare kommer på det här som text i stället för en entitet.
    - Observera att fältet ”entiteter krävs” kommer också att få dessa entiteter och de kan inte tas bort. Detta förhindrar att den här åtgärden från att bli tillgängliga förrän båda krävs entiteter finns.
3. Klicka på knappen ”Skapa”.
4. Klicka på knappen ”ny åtgärd” igen för att skapa en andra åtgärd.
5. I ”Robotens svaret” fälttyp, ”när du planerar att resa”?.
6. I ”diskvalificera entiteter” fälttypen, ”avgång” och även typen, ”return”.
    - Dessa berätta för våra robotar kan inte vidta åtgärden om något av dessa entiteter innehåller ett värde.
7. Klicka på knappen ”Skapa”.


### <a name="training"></a>Utbildning

1. Titta på den ”utbildning: [Status]” i det övre vänstra hörnet en del av sidan och vänta tills den för att vara ”slutfört”.
    - Du kan klicka på länken ”Uppdatera” om det tar för lång.
    - Utbildning status krävs ”slutfört” så att våra entitet matchare fungerar när vi tränar modellen.
2. På den vänstra panelen klickar du på ”Train-dialogrutor” och sedan på knappen ”Ny träna dialogruta”.
3. Typen i den första användaren uttryck ”boka mig en flygning”. 
4. Klicka på knappen ”poäng åtgärder”.
5. Välj svar ”när du planerar att resa”?.
6. Som användare, svara med, ”lämnar morgon och returnera söndag nästa vecka”.
    - Observera hur Konversationsdeltagare har identifierat två ”har redan tränats datum” entiteter i den användaren aktivera.
7. På panelen ”entitet identifiering” markerar du texten ”morgon” och kategorisera det som ”avgång”
8. Även använda etiketten texten ”Sunday nästa vecka” som ”return”
9. Klicka på knappen ”poäng åtgärder”.
    - Observera hur den ”minne”-rutan innehåller avgång och gå sedan tillbaka datumen.
    - Hovra över var och en och se hur entiteterna är date-objekt som tydligt fånga det faktiska kalender i stället för ”Sunday” eller ”imorgon”.
10. Välj den ”du lämnar på...” Bot-svar.
11. Klicka på knappen ”Spara”.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Alternativa indata](./10-alternative-inputs.md)
