---
title: Entiteten matchare i en konversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder entiteten matchare i Konversationsdeltagare.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca78e2536a922165f40bbcbabcae005021aa70b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707874"
---
# <a name="entity-resolvers"></a>Entiteten matchare

Den här kursen visar hur du använder entiteten matchare i Konversationsdeltagare

## <a name="video"></a>Video

[![Entiteten matchare självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudien Bot körs

    npm run tutorial-general

## <a name="details"></a>Information

- Matchare typen är en valfri egenskap för anpassade entiteter.
- Entiteten matchare utnyttja kraften hos förtränade entitet identifierare i LUIS att ge ytterligare information och tydlighet för din anpassade entitet.

## <a name="steps"></a>Steg

Starta på startsidan i Webbgränssnittet.

### <a name="create-the-model"></a>Skapa modellen

1. Välj **nya modellen**.
2. Ange **entitet matchare** för **namn**.
3. Välj **Skapa**.

### <a name="create-a-pair-of-entities"></a>Skapa ett par med entiteter

1. Välj **entiteter** i den vänstra panelen, sedan **ny entitet**.
2. Ange **avgång** för **entitetsnamn**.
3. Välj **datetimeV2** för **matchare typ**.
4. Välj **Skapa**. Stäng endast i informationssyfte popup-fönstret genom att välja **OK**.
5. Upprepa steg 1-4 om du vill skapa en andra enhet med namnet **returnera** med **datetimeV2** matchare typen.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Skapa ett par åtgärder

1. Välj **åtgärder** i den vänstra panelen, sedan **ny åtgärd**.
2. Ange **du lämnar på $departure och returnera på $return** för **Robotens svar...** .
    - VIKTIGT - när att skriva i $[entityName] måste du trycker på RETUR eller klickar på entiteten i nedrullningsbara menyn annars Konversationsdeltagare kommer på det här som text i stället för en entitet.
    - Observera att den **krävs entiteter** fält kommer också att få dessa entiteter och de kan inte tas bort. Detta förhindrar att den här åtgärden från att bli tillgängliga förrän båda krävs entiteter finns.
3. Välj **Skapa**.
4. Välj **ny åtgärd** att skapa en andra åtgärd.
5. Ange **när du planerar att resa?** för **Robotens svar...** .
6. Ange **avgång** och **returnera** för **diskvalificera entiteter**. Dessa berätta för våra robotar kan inte vidta åtgärden om något av dessa entiteter innehåller ett värde.
7. Välj **Skapa**.

![](../media/T09_actions.png)

### <a name="training"></a>Utbildning

1. Titta på den **utbildning: [Status]** i det övre vänstra hörnet för **slutförd**.
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
    - Hovra över var och en och se hur entiteterna är date-objekt som tydligt avbilda faktiska kalenderdatumet istället för ”Sunday” eller ”imorgon”.
10. Välj den ”du lämnar på...” Bot-svar.
11. Klicka på knappen ”Spara”.

![](../media/T09_training.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Alternativa indata](./10-alternative-inputs.md)
