---
title: Lägg till avsikter
titleSuffix: Language Understanding - Azure Cognitive Services
description: Lägg till avsikter att LUIS-app för att identifiera grupper av frågor eller kommandon som har samma avsikt.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: c8bcbe011dc4ff9841a06b914a90ae7f9a14ee74
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598510"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Lägg till avsikter att fastställa användarens avsikt är att yttranden

Lägg till [avsikter](luis-concept-intent.md) till din LUIS-app för att identifiera grupper av frågor eller kommandon som har samma avsikt. 

Avsikter hanteras från övre navigeringsfältet i **skapa** avsnittet sedan från den vänstra panelen **avsikter**. 

## <a name="create-an-app"></a>Skapa en app

1. Logga in på den [LUIS](https://www.luis.ai) portal.

1. Välj **Create new app** (Skapa ny app). 

1. Namnge den nya appen `MyHumanResourcesApp`. Välj den **engelska** kultur. Beskrivningen är valfri. 

1. Välj **Done** (Klar). 

## <a name="add-intent"></a>Lägga till avsikt

1. Appen öppnas på den **avsikter** lista.

1. På sidan **Intents** (Avsikter) väljer du **Create new intent** (Skapa ny avsikt).

1. I den **skapa nya avsikt** dialogrutan Ange avsikt namn `GetEmployeeInformation`, och klicka på **klar**.

    ![Lägg till avsikt](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Lägg till en exempel-uttryck

Exempel yttranden är text-exempel på användarnas frågor eller kommandon. Om du vill hålla Språkförståelse (LUIS), som du behöver lägga till exempel yttranden till en avsikt.

1. På den **GetEmployeeInformation** avsikt information anger en relevanta uttryck som du förväntar dig från dina användare, till exempel `Does John Smith work in Seattle?` i textrutan nedan avsikt namn och tryck på RETUR.
 
    ![Skärmbild av avsikter detaljsidan med uttryck markerat](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS konverterar alla yttranden till gemener och lägger till blanksteg runt token, till exempel bindestreck.

## <a name="intent-prediction-discrepancy-errors"></a>Avsiktshantering förutsägelse avvikelse fel 

Ett uttryck i ett intent som kan ha en avsiktlig förutsägelse avvikelse mellan den valda metoden och poäng för förutsägelse. LUIS anger skillnaden med en röd ram runt den **märkta avsikt** på raden i exempel-uttryck. 

![Skärmbild av avsikter detaljsidan med uttryck förutsägelse avvikelse fel](./media/luis-how-to-add-intents/prediction-discrepancy-intent.png) 

I det övre navigeringsfältet väljer **träna**. Avvikelse för förutsägelse är nu borta.

## <a name="add-a-custom-entity"></a>Lägg till en anpassad entitet

När ett uttryck har lagts till en avsikt, kan du välja text från uttryck att skapa en anpassad entitet. En anpassad entitet är ett sätt att tagga text för extrahering, tillsammans med rätt avsikten. 

1. Markera ord, `Seattle`, i uttryck. Hakparenteser ritas runt texten och en nedrullningsbar meny. 

    ![Skärmbild av avsikter informationssidan anpassad entitet skapas](./media/luis-how-to-add-intents/create-custom-entity.png) 

    Det här exemplet väljer ett enstaka ord för att markera som en entitet. Du kan markera enstaka fungerar och fraser som entiteter.

1. Ange i översta textrutan-menyn, `Location`och välj sedan **Skapa ny entitet**. 

    ![Skärmbild av avsikter informationssidan skapar anpassade entitetsnamn](./media/luis-how-to-add-intents/create-custom-entity-name.png) 

1. I den **vilken typ av enhet vill du skapa?** popup-fönstret för att skapa en entitet, kontrollera att den **entitetsnamn** är _plats_, och **entitetstyp**  är _enkel_. Välj **Done** (Klar).

## <a name="entity-prediction-discrepancy-errors"></a>Entiteten förutsägelse avvikelse fel 

Entiteten är understruket i rött att indikera en [entitet förutsägelse avvikelse](luis-how-to-add-example-utterances.md#entity-status-predictions). Eftersom detta är den första förekomsten av en entitet kan det finns inte tillräckligt med exempel för LUIS för att ha en hög exakthet som den här texten är taggade med rätt entiteten. Den här skillnaden tas bort när appen har tränats. 

![Skärmbild av avsikter informationssidan, anpassad entitetsnamn markeras i blått](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Texten markeras i blått, som anger en entitet.  

## <a name="add-a-prebuilt-entity"></a>Lägg till en fördefinierade entitet

Mer information finns i [fördefinierade entitet](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="using-the-contextual-toolbar"></a>Med hjälp av sammanhangsbaserad verktygsfältet

När en eller flera exempel yttranden väljs i listan genom att markera kryssrutan till vänster om ett uttryck kan i verktygsfältet ovanför listan uttryck du utföra följande åtgärder:

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
* Lägg till ett mönster: Ett mönster kan du ta en vanliga uttryck och markera replaceable ignorable text, vilket minskar behovet av flera uttryck i avsikten. 

Den **märkta avsikt** kolumn kan du ändra syftet med uttryck.

## <a name="train-your-app-after-changing-model-with-intents"></a>Träna din app när du har ändrat modell med avsikter

När du lägger till, redigera eller ta bort avsikter, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app så att ändringarna tillämpas på slutpunkten frågor. 

## <a name="next-steps"></a>Nästa steg

Läs mer om att lägga till [exempel yttranden](luis-how-to-add-example-utterances.md) med entiteter. 
