---
title: Lägga till avsikter i THOMAS program | Microsoft Docs
description: Använd språket förstå (THOMAS) om du vill lägga till avsikter att appar förstå användarförfrågningar och ta hänsyn till dem korrekt.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.service: cognitive-services
ms.openlocfilehash: 6e013e994a3bcb60c3104aa10cd7bad1535706f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355764"
---
# <a name="manage-intents"></a>Hantera avsikter 
Lägg till [intents](luis-concept-intent.md) till THOMAS appen för att identifiera grupper av frågor eller kommandon som har samma avsikt. 

Du kan lägga till och hantera dina avsikter från den **Intents** sidan är tillgänglig från **avsikter** THOMASS vänstra panelen. 

Följande procedur visar hur du lägger till ”Bookflight” avsikten i appen TravelAgent.

## <a name="add-intent"></a>Lägg till avsikt

1. Öppna din app (till exempel TravelAgent) genom att klicka på namnet på **Mina appar** , och klickar sedan på **Intents** i den vänstra panelen. 
2. På den **Intents** klickar du på **skapa nya avsikt**.

    ![Avsikter lista](./media/luis-how-to-add-intents/IntentsList.png)
3. I den **skapa nya avsikt** skriver avsikten name ”BookFlight” och klicka på **klar**.

    ![Lägg till avsikt](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    På sidan avsiktshantering information av nytillagda avsikt [lägga till utterances](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Byt namn på avsikten

1. På den **avsikt** klickar du på ikonen Byt namn på ![Byt namn på avsikten](./media/luis-how-to-add-intents/Rename-Intent-btn.png) bredvid namnet på avsiktshantering. 

2. På den **avsikt** sida, namnet på aktuella avsiktshantering visas i en dialogruta. Redigera avsiktshantering namn och tryck på RETUR. Det nya namnet sparas och visas på sidan avsiktshantering.

    ![Redigera avsikt](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Ta bort avsikt
När du tar bort syftet än avsiktshantering ingen, kan du lägga till alla utterances avsiktshantering NONE. Detta är användbart om du behöver flytta utterances i stället för att ta bort dem.   

1. På den **avsikt** klickar du på den **ta bort avsikt** knappen bredvid höger om avsiktshantering namn. 

    ![Avsiktshantering knappen Ta bort](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Klicka på ”Ok-knappen i den bekräftande dialogrutan.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Lägg till en utterance på avsiktshantering sida

Ange en relevant utterance som du förväntar dig från dina användare, t.ex på sidan avsiktshantering `book 2 adult business tickets to Paris tomorrow on Air France` i textrutan under avsiktshantering namn och tryck på RETUR. 
 
>[!NOTE]
>THOMAS konverterar alla utterances till gemener.

![Skärmbild av Intents på sidan med utterance markerat](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Utterances läggs till utterances för den aktuella avsikten. När en utterance läggs [etikett alla entiteter](luis-how-to-add-example-utterances.md) inom utterances och [träna](luis-how-to-train.md) din app. 

## <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från ett utterance
Se [Lägg till mönster från befintliga utterance på avsikten eller entiteten](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Redigera en utterance på avsiktshantering sida

Om du vill redigera en utterance väljer du tre punkter (...)-ikonen längst till höger på raden för att utterance och välj sedan **redigera**. Ändra texten och tryck på RETUR på tangentbordet.

![Skärmbild av Intents på sidan med tre punkter ikon markerat](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Omtilldela utterances på avsiktshantering sida
Du kan ändra syftet med en eller flera utterances genom att tilldela dem till en annan avsikt. 

Om du vill tilldela en enda utterance till en annan typ av avsikt, längst till höger i den utterance rad väljer du rätt avsiktshantering namn under den **etikett avsikt** kolumn. Utterance tas bort från den aktuella avsikt utterance lista. 

![Skärmbild av BookFlight avsiktshantering sida med en utterance syfte under etiketterade markerade avsiktshantering kolumnen](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Om du vill ändra syftet med flera utterances, markerar du kryssrutorna till vänster om utterances och välj sedan **omtilldela avsikt**. Välj rätt avsikten i listan.

![Skärmbild av BookFlight avsiktshantering sida med en utterance kontrolleras och omtilldela avsiktshantering knappen markerad](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Ta bort utterances på avsiktshantering sida

Om du vill ta bort en utterance väljer du tre punkter (...)-ikonen längst till höger på raden för att utterance och välj sedan **ta bort**. Utterance tas bort från listan och THOMAS appen.

![Skärmbild av Intents på sidan med ta borttagningsalternativet markerat](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Ta bort flera utterances:

1. Markera kryssrutorna till vänster om utterances och välj sedan **ta bort utterances (s)**. 

    ![Skärmbild av Intents på sidan med utterances kontrolleras och ta bort utterance(s) knappen markerad](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Välj **klar** i den **ta bort utterances?** popup-fönstret.

## <a name="search-in-utterances-on-intent-page"></a>Sök i utterances på avsiktshantering sida
I en avsikt, kan du söka efter utterances som innehåller text (ord eller fraser). Till exempel märker du ett fel som berör ett visst ord och du vill söka efter alla exempel som innehåller viss ordet. 

1. Välj förstoringsglaset i verktygsfältet.

    ![Skärmbild av Intents sidan med sökikonen förstoringsglas markerat](./media/luis-how-to-add-intents/magnifying-glass.png)

2. En sökning textruta visas. Ange ett ord eller en fras i sökrutan i det övre högra hörnet av listan över utterances. Utterances lista uppdateringar om du vill visa de utterances som innehåller söktexten. 

    ![Skärmbild av Intents sidan med Sök textruta markerat](./media/luis-how-to-add-intents/search-textbox.png)

    Om du vill avbryta sökningen och återställa en fullständig lista över utterances, att ta bort den text som du skrev. Om du vill stänga textrutan sökningen väljer du förstoringsglaset i verktygsfältet igen.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Förutsägelse diskrepans fel på avsiktshantering sida
En utterance i syftet kan ha en avvikelse mellan den valda metoden och poängsättningen förutsägelse. THOMAS anger skillnaden med en röd ruta runt poängen. 

![Skärmbild av BookFlight avsikt sida med förutsägelse diskrepans poäng markerat](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Filtrera efter avsiktshantering förutsägelse diskrepans fel på avsiktshantering sida
Om du vill filtrera listan utterance att endast utterances med en avsiktshantering förutsägelse avvikelse växla från **visa alla** till **endast fel** i verktygsfältet. 

## <a name="filter-by-entity-type-on-intent-page"></a>Filtrera efter entitetstypen på avsiktshantering sida
Använd den **entitet filter** listrutan i verktygsfältet för att filtrera utterances av enheten. 

![Skärmbild av Intents sidan med filter för entiteten markerat](./media/luis-how-to-add-intents/filter-by-entities.png) 

Markera kryssrutan blå filter med ordet eller frasen under verktygsfältet för att ta bort filtret.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>Växla till token på avsiktshantering sida
Växla **token visa** visa token i stället för entiteten typnamn. På tangentbordet kan du också använda **Ctrl + E** att växla vyn. 

![Skärmbild av BookFlight avsikten med Token vy markerat](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Träna din app när du har ändrat modellen med avsikter
När du lägger till, redigera eller ta bort avsikter, [träna](luis-how-to-train.md) och [publicera](PublishApp.md) din app för att ändringarna ska påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

När du lägger till avsikter till din app är nästa uppgift börja lägga till [exempel utterances](luis-how-to-add-example-utterances.md) för avsikter som du har lagt till. 
