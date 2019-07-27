---
title: Lägg till avsikter – LUIS
titleSuffix: Azure Cognitive Services
description: Lägg till avsikter att LUIS-app för att identifiera grupper av frågor eller kommandon som har samma avsikt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 639079a43e75741d78496939f90324b076b164d5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563690"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Lägg till avsikter för att fastställa användar avsikt för yttranden

Lägg till [avsikter](luis-concept-intent.md) till din LUIS-app för att identifiera grupper av frågor eller kommandon som har samma avsikt. 

Avsikter hanteras från övre navigeringsfältet i **skapa** avsnittet sedan från den vänstra panelen **avsikter**. 

## <a name="add-intent"></a>Lägga till avsikt

1. På sidan **Intents** (Avsikter) väljer du **Create new intent** (Skapa ny avsikt).

1. I den **skapa nya avsikt** dialogrutan Ange avsikt namn `GetEmployeeInformation`, och klicka på **klar**.

    ![Lägg till avsikt](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Lägg till en exempel-uttryck

Exempel yttranden är text-exempel på användarnas frågor eller kommandon. Om du vill hålla Språkförståelse (LUIS), som du behöver lägga till exempel yttranden till en avsikt.

1. På den **GetEmployeeInformation** avsikt information anger en relevanta uttryck som du förväntar dig från dina användare, till exempel `Does John Smith work in Seattle?` i textrutan nedan avsikt namn och tryck på RETUR.
 
    ![Skärmbild av avsikter detaljsidan med uttryck markerat](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS konverterar alla yttranden till gemener och lägger till blanksteg runt token, till exempel bindestreck.

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

När ett uttryck har lagts till en avsikt, kan du välja text från uttryck att skapa en anpassad entitet. En anpassad entitet är ett sätt att tagga text för extrahering, tillsammans med rätt avsikten. 

Läs mer i [lägga till entitet till uttryck](luis-how-to-add-example-utterances.md) .

## <a name="entity-prediction-discrepancy-errors"></a>Entiteten förutsägelse avvikelse fel 

Entiteten är understruket i rött att indikera en [entitet förutsägelse avvikelse](luis-how-to-add-example-utterances.md#entity-status-predictions). Eftersom detta är den första förekomsten av en entitet kan det finns inte tillräckligt med exempel för LUIS för att ha en hög exakthet som den här texten är taggade med rätt entiteten. Den här skillnaden tas bort när appen har tränats. 

![Skärmbild av avsikter informationssidan, anpassad entitetsnamn markeras i blått](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Texten markeras i blått, som anger en entitet.  

## <a name="add-a-prebuilt-entity"></a>Lägg till en fördefinierade entitet

Mer information finns i [fördefinierade entitet](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Med hjälp av sammanhangsbaserad verktygsfältet

När ett eller flera exempel yttranden har marker ATS i listan, genom att markera rutan till vänster om en uttryck, i verktygsfältet ovanför uttryck-listan kan du utföra följande åtgärder:

* Omtilldela avsikt: flytta utterance(s) till olika avsikt
* Ta bort utterance(s)
* Entiteten filter: Visa endast yttranden som innehåller filtrerade entiteter
* Visa alla / endast fel: Visa yttranden med förutsägelse fel eller visa alla yttranden
* Visa entiteter/token: Visa rå text av uttryck eller visa entiteter med entitetsnamn
* Förstoringsglas: Sök efter uttryck som innehåller viss text

## <a name="working-with-an-individual-utterance"></a>Arbeta med en enskilda uttryck

Följande åtgärder kan utföras på en enskild uttryck från ellips-menyn till höger om uttryck:

* Redigera: ändra texten i uttryck
* Ta bort: ta bort uttryck från avsikten. Om du fortfarande vill uttryck, en bättre metod är att flytta den till den **ingen** avsikt. 
* Lägg till ett mönster: Ett mönster gör att du kan använda en gemensam uttryck och markera text och text som kan ignoreras, vilket minskar behovet av mer yttranden i avsikten. 

Den **märkta avsikt** kolumn kan du ändra syftet med uttryck.

## <a name="train-your-app-after-changing-model-with-intents"></a>Träna din app när du har ändrat modell med avsikter

När du lägger till, redigera eller ta bort avsikter, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app så att ändringarna tillämpas på slutpunkten frågor. 

## <a name="next-steps"></a>Nästa steg

Läs mer om att lägga till [exempel yttranden](luis-how-to-add-example-utterances.md) med entiteter. 
