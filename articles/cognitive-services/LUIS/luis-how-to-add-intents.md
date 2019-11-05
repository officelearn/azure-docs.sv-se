---
title: Lägg till avsikter – LUIS
titleSuffix: Azure Cognitive Services
description: Lägg till avsikter i LUIS-appen för att identifiera grupper med frågor eller kommandon som har samma avsikt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 1f2f001489552203f0157dd24356341eb3184c81
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467544"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Lägg till avsikter för att fastställa användar avsikt för yttranden

Lägg [till avsikter](luis-concept-intent.md) i Luis-appen för att identifiera grupper med frågor eller kommandon som har samma avsikt. 

Avsikter hanteras från det övre navigerings fältets **build** -avsnitt och sedan från den vänstra panelens **avsikter**. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-intent"></a>Lägga till avsikt

1. På sidan **Intents** (Avsikter) väljer du **Create new intent** (Skapa ny avsikt).

1. I dialog rutan **Skapa ny avsikt** anger du namnet på avsikten `GetEmployeeInformation`och klickar på **Slutför**.

    ![Lägg till avsikt](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Lägg till ett exempel uttryck

Exempel på yttranden är text exempel på användar frågor eller kommandon. Om du vill lära dig Language Understanding (LUIS) måste du lägga till exempel yttranden i ett avsikts syfte.

1. På sidan information om **GetEmployeeInformation** -information anger du en relevant uttryck som du förväntar dig av dina användare, t. ex. `Does John Smith work in Seattle?` i text rutan under namnet på avsikten och trycker sedan på RETUR.
 
    ![Skärm bild av sidan information om information, där uttryck är markerat](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS konverterar alla yttranden till gemener och lägger till blank steg runt token, till exempel bindestreck.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Fel vid förutsägelse av avsikt 

Ett exempel på en uttryck i ett avsikt kan ha ett förutsägelse fel mellan avsikten med exempel uttryck för närvarande i och den förutsägelse avsikt som fastställs under utbildningen. 

Om du vill hitta uttryck förutsägelse fel och åtgärda dem kan du använda alternativet alternativ för **utvärdering** av **filter** alternativ för felaktig och oklar kombination med **visnings** alternativet i **detaljerad vy**. 

![Använd filter alternativet om du vill hitta uttryck förutsägelse fel och åtgärda dem.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

När filtren och vyn används och det finns exempel yttranden med fel visas yttranden och problemen i exempel uttryck-listan.

![! [När filtren och vyn används och det finns exempel yttranden med fel visas yttranden och problemen i exempel uttryck-listan.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Varje rad visar den aktuella inlärningens förutsägelse Poäng för exemplet uttryck, närmsta rival, vilket är skillnaden i dessa två resultat. 

### <a name="fixing-intents"></a>Åtgärda avsikter

Om du vill lära dig hur du åtgärdar förutsägelse fel för avsikt använder du [sammanfattnings instrument panelen](luis-how-to-use-dashboard.md). Sammanfattnings instrument panelen innehåller analys för den aktiva versionens senaste utbildning och erbjuder de vanligaste förslagen för att åtgärda din modell.  

## <a name="add-a-custom-entity"></a>Lägg till en anpassad entitet

När en uttryck har lagts till i ett avsikts sätt kan du välja text i uttryck för att skapa en anpassad entitet. En anpassad entitet är ett sätt att tagga text för extraktion, tillsammans med rätt avsikt. 

Läs mer i [lägga till entitet till uttryck](luis-how-to-add-example-utterances.md) .

## <a name="entity-prediction-discrepancy-errors"></a>Avvikelse fel i enhets förutsägelse 

Entiteten är understruken i rött för att indikera en [avvikelse i enhets förutsägelse](luis-how-to-add-example-utterances.md#entity-status-predictions). Eftersom det här är den första förekomsten av en entitet finns det inte tillräckligt många exempel för att LUIS ska ha ett högt förtroende för att texten ska märkas med rätt entitet. Den här avvikelsen tas bort när appen tränas. 

![Skärm bild av sidan information om avsikter, anpassat entitetsnamn markerat i blått](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Texten är markerad i blått, vilket indikerar en entitet.  

## <a name="add-a-prebuilt-entity"></a>Lägg till en fördefinierad entitet

Mer information finns i en [fördefinierad entitet](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Använda sammanhangsbaserade verktygsfält

När ett eller flera exempel yttranden har marker ATS i listan, genom att markera rutan till vänster om en uttryck, i verktygsfältet ovanför uttryck-listan kan du utföra följande åtgärder:

* Omtilldela avsikt: flytta uttryck (ar) till olika avsikter
* Ta bort uttryck (er)
* Enhets filter: Visa endast yttranden som innehåller filtrerade entiteter
* Visa endast alla/fel: Visa yttranden med förutsägelse fel eller Visa alla yttranden
* Vyn entiteter/token: Visa entiteter med entitetsnamn eller Visa rå uttryck text
* Förstorings glas: Sök efter yttranden som innehåller en speciell text

## <a name="working-with-an-individual-utterance"></a>Arbeta med en enskild uttryck

Följande åtgärder kan utföras på en enskild uttryck från ellips-menyn till höger om uttryck:

* Redigera: ändra texten för uttryck
* Ta bort: ta bort uttryck från avsikten. Om du fortfarande vill att uttryck ska du använda en bättre metod för att flytta den till **none** -avsikten. 
* Lägg till ett mönster: ett mönster gör att du kan använda en gemensam uttryck och markera text som kan förbytas, vilket minskar behovet av mer yttranden i avsikten. 

Med kolumnen **benämnaD avsikt** kan du ändra syftet med uttryck.

## <a name="train-your-app-after-changing-model-with-intents"></a>Träna din app efter att ha ändrat modellen med avsikter

När du har lagt till, redigerat eller tagit bort avsikter kan du [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app så att ändringarna tillämpas på slut punkts frågor. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att lägga till [exempel yttranden](luis-how-to-add-example-utterances.md) med entiteter. 
