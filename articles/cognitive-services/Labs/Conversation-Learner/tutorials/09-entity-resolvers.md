---
title: Enhets matchare i en Conversation Learner modell – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder enhets matchare i Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 60c4abf1590cb91fd460cc6a2a5ba75a225ebd80
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704043"
---
# <a name="entity-resolvers"></a>Enhets matchare

Den här självstudien visar hur du använder enhets matchare i Conversation Learner

## <a name="video"></a>Video

[![Förhands granskning av enhets lösare för entitet](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information

- Matchnings typ är en valfri egenskap för anpassade entiteter.
- Enhets matchare använder kraften hos de förtränade enhets identifierarna i LUIS för att ge ytterligare information och klarhet för din anpassade entitet.

## <a name="steps"></a>Steg

Starta på Start sidan i webb gränssnittet.

### <a name="create-the-model"></a>Skapa modellen

1. Välj **ny modell**.
2. Ange **enhets matchare** för **namn**.
3. Välj **Skapa**.

### <a name="create-a-pair-of-entities"></a>Skapa ett par med entiteter

1. Välj **entiteter** i den vänstra panelen och sedan **ny entitet**.
2. Ange **avgångs** **enhets namn**.
3. Välj **datetimeV2** som **matchnings typ**.
4. Välj **Skapa**. Stäng informations fönstret genom att välja **OK**.
5. Upprepa steg 1-4 om du vill skapa en andra entitet med namnet **Return** med matchnings typen **datetimeV2** .

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Skapa ett åtgärds par

1. Välj **åtgärder** i den vänstra panelen och sedan **ny åtgärd**.
2. Ange **att du lämnar $Departure och returnerar $Return** för robotens **svar..** ..
    - VIKTIGT – när du skriver i $ [entityName] måste du trycka på RETUR eller klicka på entiteten i den nedrullningsbara listan, Conversation Learner annars kan det vara text i stället för en entitet.
    - Observera att fältet **obligatoriska entiteter** också kommer att hämta dessa entiteter och de kan inte tas bort. Detta förhindrar att den här åtgärden blir tillgänglig förrän båda nödvändiga entiteter finns.
3. Välj **Skapa**.
4. Välj **ny åtgärd** för att skapa en andra åtgärd.
5. Ange **när du planerar att resa?** för **robotens svar..** ..
6. Ange **avgångs** och **RETUR** fördekvalificerande entiteter. Dessa säger att vår robot inte vidtar den här åtgärden om någon av dessa entiteter innehåller ett värde.
7. Välj **Skapa**.

![](../media/T09_actions.png)

### <a name="training"></a>Utbildning

1. Se **utbildningen: [status]** i det övre vänstra hörnet för **slutförd**.
    - Du kan klicka på länken "uppdatera" om detta tar för lång tid.
    - Utbildnings status "slutförd" är nödvändig så att våra enhets matchare fungerar när vi tränar modellen.

2. Klicka på "träna dialog rutor" i den vänstra panelen och klicka sedan på knappen "ny träna dialog".
3. Skriv in den första användaren uttryck, "boka mig en flygning". 
4. Klicka på knappen "Poäng åtgärder".
5. Välj svaret "när planerar du att resa?".
6. Som användaren svarar med, "lämnar imorgon och returnerar söndag nästa vecka".
    - Observera hur Conversation Learner har identifierat två "förtränade datum"-entiteter i den användaren.
7. I panelen "entitets identifiering" väljer du texten "imorgon" och märk den som "avresa"
8. Märk även texten "söndag nästa vecka" som "retur"
9. Klicka på knappen "Poäng åtgärder".
    - Observera att "minne"-fönstret innehåller avsändar-och retur datum.
    - Hovra över var och en och se hur entiteterna är datum objekt som tydligt fångar in det faktiska kalender datumet i stället för "söndag" eller "imorgon".
10. Välj "du lämnar..." Robot svar.
11. Klicka på knappen Spara.

![](../media/T09_training.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Räkna upp entiteter](./tutorial-enum-set-entity.md)
