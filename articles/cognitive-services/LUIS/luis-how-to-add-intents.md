---
title: Lägg till avsikter i LUIS-program
titleSuffix: Azure Cognitive Services
description: Lägg till avsikter att LUIS-app för att identifiera grupper av frågor eller kommandon som har samma avsikt.
services: cognitive-services
author: diberry
manager: cgronlun
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 9b7207a16a89e48ad64b2dbc48a5293d0cf8aa25
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036090"
---
# <a name="manage-intents"></a>Hantera avsikter 
Lägg till [avsikter](luis-concept-intent.md) till din LUIS-app för att identifiera grupper av frågor eller kommandon som har samma avsikt. 

Avsikter hanteras från den **skapa** avsnitt i det övre verktygsfältet. Du lägger till och hantera dina avsikter från den **avsikter** sidan är tillgänglig i den vänstra panelen. 

Följande procedur visar hur du lägger till ”Bookflight” avsikten i appen TravelAgent.

## <a name="add-intent"></a>Lägg till avsikt

1. Öppna din app (till exempel TravelAgent) genom att klicka på namnet på **Mina appar** , och klicka sedan på **avsikter** på den vänstra panelen. 
2. På den **avsikter** klickar du på **skapa nya avsikt**.

3. I den **skapa nya avsikt** skriver avsikten namn ”BookFlight” och klickar på **klar**.

    ![Lägg till avsikt](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    På sidan avsikt information för den nyligen tillagda avsikten, [Lägg till yttranden](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Byt namn på avsikten

1. På den **avsikt** klickar du på ikonen Byt namn på ![Byt namn på avsikten](./media/luis-how-to-add-intents/Rename-Intent-btn.png) bredvid namnet på avsikt. 

2. På den **avsikt** kan namnet på aktuella avsikt visas i en dialogruta. Redigera avsikt namn och tryck på RETUR. Det nya namnet sparas och visas på sidan avsikt.

    ![Redigera avsikt](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Ta bort avsikt
När du tar bort ett intent än avsikt None, kan du lägga till alla talade avsikt NONE. Detta är användbart om du behöver flytta yttranden i stället för att tas bort.   

1. På den **avsikt** klickar du på den **ta bort avsikt** knappen bredvid höger om avsiktlig namn. 

    ![Ta bort avsikt knapp](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Klicka på ”Ok” på den bekräftande dialogrutan.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Lägg till ett uttryck på avsikt sida

På sidan avsikt, anger du en relevanta uttryck som du förväntar dig från dina användare, till exempel `book 2 adult business tickets to Paris tomorrow on Air France` i textrutan nedan avsikt namn och tryck på RETUR. 
 
>[!NOTE]
>LUIS konverterar alla yttranden till gemener.

![Skärmbild av avsikter detaljsidan med uttryck markerat](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Yttranden läggs till i listan yttranden för den aktuella avsikten. När ett uttryck har lagts till, [märka alla entiteter](luis-how-to-add-example-utterances.md) inom talade och [träna](luis-how-to-train.md) din app. 

## <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från ett uttryck
Se [Lägg till mönster från befintliga uttryck på avsikten eller entitet](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Redigera ett uttryck på avsikt sida

Om du vill redigera ett uttryck, Välj ellipsen (***...*** ) längst till höger på raden för den uttryck och välj sedan **redigera**. Ändra texten och tryck på RETUR på tangentbordet.

![Skärmbild av avsikter detaljsidan med knappen med tre punkter markerat](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Omtilldela yttranden på avsikt sida
Du kan ändra syftet med en eller flera yttranden genom att tilldela dem till en annan avsikt. 

Om du vill tilldela om en enda uttryck till en annan typ av avsikt, längst till höger i uttryck raden att välja rätt avsikt namnet under den **märkta avsikt** kolumn. Uttryck tas bort från den aktuella avsikt uttryck lista. 

![Skärmbild av BookFlight avsikt sida med ett uttryck avsikt under etiketterade avsikt kolumn har markerats](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Om du vill ändra syftet med flera yttranden, markerar du kryssrutorna till vänster om talade och välj sedan **omtilldela avsikt**. Välj rätt avsikten i listan.

![Skärmbild av BookFlight avsikt sida med ett uttryck som kontrolleras och omtilldela avsikt knappen markerad](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Ta bort yttranden på avsikt sida

Om du vill ta bort ett uttryck, Välj ellipsen (***...*** ) längst till höger på raden för den uttryck och välj sedan **ta bort**. Uttryck tas bort från listan och LUIS-app.

![Skärmbild av avsikter sidan, med alternativet Ta bort markerade](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Ta bort flera uttryck:

1. Markera kryssrutorna till vänster om talade och välj sedan **ta bort yttranden (s)**. 

    ![Skärmbild av avsikter detaljsidan med yttranden kontrolleras och ta bort utterance(s) knappen markerad](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Välj **klar** i den **ta bort yttranden?** standardarbetsytan i dialogrutan.

## <a name="search-in-utterances-on-intent-page"></a>Sök i yttranden på avsikt sida
Du kan söka efter uttryck som innehåller text (ord eller fraser) i det syftet uttryck lista. Till exempel märker du ett fel som innebär att ett visst ord och du vill hitta alla exempel som innehåller viss ordet. 

1. Välj förstoringsglasikonen i verktygsfältet.

    ![Skärmbild av avsikter sida med sökikonen förstoringsglas markerat](./media/luis-how-to-add-intents/magnifying-glass.png)

2. En textruta för sökning visas. Ange ett ord eller en fras i sökrutan i det övre högra hörnet av listan över yttranden. Talade lista uppdateringar om du vill visa de uttryck som innehåller söktext. 

    ![Skärmbild av avsikter sida med söktextruta markerat](./media/luis-how-to-add-intents/search-textbox.png)

    Om du vill avbryta sökningen och återställer din fullständig lista över yttranden, tar du bort den söktext som du har skrivit. Om du vill stänga sökrutan för bibliotekskontrollen, väljer du på förstoringsglaset i verktygsfältet igen.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Förutsägelse avvikelse fel på avsikt sida
Ett uttryck i ett intent som kan ha en diskrepans mellan den valda metoden och poäng för förutsägelse. LUIS anger skillnaden med en röd ram runt poängen. 

![Skärmbild av BookFlight avsikt sida med förutsägelse avvikelse poäng markerat](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Filtrera efter avsikt förutsägelse avvikelse fel på avsikt sida
Om du vill filtrera listan uttryck för att endast yttranden med en avsiktlig förutsägelse avvikelse, byta inställning från **visa alla** till **endast fel** i verktygsfältet. 

## <a name="filter-by-entity-type-on-intent-page"></a>Filtrera efter entitetstypen på avsikt sida
Använd den **entitet filter** listrutan i verktygsfältet för att filtrera talade av enheten. 

![Skärmbild av avsikter sida med filter för entiteten markerat](./media/luis-how-to-add-intents/filter-by-entities.png) 

Välj blå filterfältet med ordet eller frasen under verktygsfältet om du vill ta bort filtret.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>Växla till token vyn på avsikt sida
Visa/dölj **token visa** visa token i stället för typen entitetsnamn. På tangentbordet kan du också använda **kontroll + E** att växla vyn. 

![Skärmbild av BookFlight avsikten med Token vy markerat](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Träna din app när du har ändrat modell med avsikter
När du lägger till, redigera eller ta bort avsikter, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för ändringarna att påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

När du lägger till avsikter till din app, nästa uppgift är att börja lägga till [exempel yttranden](luis-how-to-add-example-utterances.md) för avsikter som du har lagt till. 
