---
title: Lägg till exempel utterances i THOMAS appar | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du lägger till utterances i språk förstå (THOMAS) program.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 74a4b77bd9823e5462eecd438cf4c1d863e79892
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300646"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Lägg till exempel utterances och etikett med entiteter

Exempel utterances är text exempel på användarnas frågor och kommandon. Om du vill hålla språk förstå (THOMAS), måste du lägga till [exempel utterances](luis-concept-utterance.md) till en [avsikt](luis-concept-intent.md).

I allmänhet kan lägga till en exempel-utterance syftet först och sedan skapa entiteter och etikett utterances på sidan avsiktshantering. Om du skulle istället skapa entiteter först, se [lägga till enheter](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Lägg till en utterance
Ange en relevant exempel utterance som du förväntar dig från dina användare, t.ex på ett avsiktshantering sida `book 2 adult business tickets to Paris tomorrow on Air France` i textrutan under avsiktshantering namn och tryck på RETUR. 
 
>[!NOTE]
>THOMAS konverterar alla utterances till gemener.

![Skärmbild av Intents på sidan med utterance markerat](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Utterances läggs till utterances för den aktuella avsikten. 

## <a name="ignoring-words-and-punctuation"></a>Ignorerar ord och skiljetecken
Om du vill ignorera specifika ord eller skiljetecken i exempel utterance använder en [mönster](luis-concept-patterns.md#pattern-syntax) med den _Ignorera_ syntax. 

## <a name="add-simple-entity-label"></a>Lägg till enkel enhet etikett
I följande procedur skapar och etikett anpassade enheter i följande utterance på sidan avsiktshantering:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Välj ”luften Frankrike” i utterance till att det är en enkel enhet.

    > [!NOTE]
    > När du väljer ord i etiketter som enheter:
    > * För enstaka ord, markera den. 
    > * För en uppsättning två eller flera ord, väljer du i början och i slutet av uppsättningen.

2. I entiteten nedrullningsbara rutan som visas kan du välja en befintlig enhet eller lägga till en ny entitet. Om du vill lägga till en ny entitet, skriver du namnet i textrutan och välj sedan **Skapa ny entitet**. 
 
    ![Skärmbild av Intents sidan, med enkel enhet etiketter markerat](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. I den **vilken typ av enhet vill du skapa?** popup-dialogrutan Kontrollera entitetsnamnet och välj den enkla entitetstypen och välj sedan **klar**.

    ![Bild av dialogrutan](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Se [Data extrahering](luis-concept-data-extraction.md#simple-entity-data) vill veta mer om hur du extraherar den enkla enheten från slutpunkten JSON frågesvar. Försök enkel enhet [quickstart](luis-quickstart-primary-and-secondary-data.md) vill veta mer om hur du använder en enkel enhet.


## <a name="add-list-entity-and-label"></a>Lägga till listan entiteten och etikett
Lista över enheter representerar en fast, stängda uppsättning (exakt matchar) relaterade ord i systemet. 

Du kan ha två normaliserade värden för en lista över entitet drycker: vattenstämplar och soda pop. Varje normaliserat namn har synonymer. Vattenstämplar är synonymer H20 gas, platt. Synonymer är frukter, cola, ingefära för soda pop. Du inte vet vilka värden när du skapar entiteten. Du kan lägga till fler när du har granskat användare utterances med synonymer.

|Normaliserat namn|Synonymer|
|--|--|
|Vatten|H20 gas, fast|
|Soda pop|Frukter, cola, ingefära|

När du skapar en ny entitet i listan på sidan avsiktshantering gör du två saker som inte kanske är uppenbara. Först skapar du en ny lista genom att lägga till det första objektet i listan. Andra, kallas det första objektet i listan med ett ord eller en fras som du har valt från utterance. Medan du kan ändra inställningarna senare från sidan entiteten, kan det vara snabbare att välja en utterance som har det ord som du vill använda för namnet på listobjektet.

Till exempel om du vill skapa en lista över typer av dryck och du valt ordet `h2o` från utterance att skapa entiteten listan skulle ha ett objekt, vars namn har h20. Om du vill ha ett mer allmänt namn bör du välja en utterance som använder mer allmänna namn. 

1. Markera det ord som är det första objektet i listan i utterance, och sedan ange namnet på listan i textrutan, och välj sedan **Skapa ny entitet**.   

    ![Skärmbild av Intents på sidan med Skapa ny entitet markerat](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. I den **vilken typ av enhet vill du skapa?** dialogrutan Lägg till synonymer för den här listobjekt. Lägg till för objektet vattenstämplar i en lista med dryck `h20`, `perrier`, och `waters`, och välj **klar**. Observera att ”vatten” läggs eftersom listan synonymer matchas på nivån token. I engelska kultur som är på nivån word så ”vatten” kan inte matchas med ”vattenstämplar” om det inte var i listan. 

    ![Skärmbild av vilken typ av enhet gör du vill skapa dialogrutan](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Den här listan för drycker har endast en dryck typ vattenstämplar. Du kan lägga till fler dryck typer av etiketter andra utterances eller genom att redigera entiteten från den **entiteter** i det vänstra navigeringsfönstret. [Redigera](luis-how-to-add-entities.md#add-list-entities) entiteterna ger alternativ för att ange ytterligare objekt med motsvarande synonymer eller [importera](luis-how-to-add-entities.md#import-list-entity-values) en lista. 

    Se [Data extrahering](luis-concept-data-extraction.md#list-entity-data) lära dig mer om att extrahera listan entiteter från slutpunkten JSON-svar för frågan. Försök i [quickstart](luis-quickstart-intent-and-list-entity.md) vill veta mer om hur du använder en enhet i listan.

## <a name="add-synonyms-to-the-list-entity"></a>Lägga till synonymer i entiteten lista 
Lägg till en synonym till entiteten listan genom att välja ett ord eller en fras i utterance. Om du har en dryck listan entiteten och vill lägga till `agua` som en synonym för vattenstämplar följer du stegen:

I utterance, Välj synonymt word, exempelvis `aqua` för vattenstämplar, välj sedan namnet på entiteten i listan listruta som **drick**och välj **som synonymen**, Välj i listan objekt som den är synonym med, såsom **vattenstämplar**.

![Skärmbild av Intents på sidan med skapa en ny synonym markerat](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Skapa en ny entitet i listan
Skapa ett nytt objekt för en befintlig lista entitet genom att välja ett ord eller en fras i utterance. Om du har en dryck listan, och vill lägga till `tea` Följ stegen som ett nytt objekt:

I utterance, Välj word för det nya listobjektet exempelvis `tea`, välj sedan namnet på entiteten i i listan listruta **drick**och välj **skapa en ny synonym**. 

![Skärmbild för att lägga till nya objekt i listan](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

Ordet markeras nu i blått. Om du hovrar över ordet visar en tagg med listnamnet, till exempel te.

![Skärmbild av ny lista objektet tagg](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Omsluta entiteter i sammansatta etikett
Sammansatta enheter skapas från **entiteter**. Du kan inte skapa en sammansatt entitet från sidan Avsiktshantering. När du har skapat den sammansatta enheten omsluter du entiteterna i en utterance på sidan Avsiktshantering. 

Under förutsättning att utterance `book 2 tickets from Seattle to Cairo`, sammansatta utterance kan returnera entitetsinformation antal biljetter (2), ursprung (Seattle) och mål (Kairo) platser i en enda överordnad enhet. 

Följ dessa [steg](luis-how-to-add-entities.md#add-prebuilt-entity) att lägga till den **nummer** färdiga entitet. När enheten har skapats kan den `2` i utterance är blå, som anger om det är en märkt entitet. Fördefinierade entiteter är märkta med THOMAS. Du kan inte lägga till eller ta bort etiketten färdiga entitet från en enda utterance. Du kan bara lägga till eller ta bort alla färdiga etiketter genom att lägga till eller ta bort entiteten färdiga från programmet.

Följ dessa [steg](#add-hierarchical-entity-and-label) att skapa en **plats** hierarkiska entitet. Etikett i exempel utterance på ursprung och destination platser. 

Innan du omsluter entiteterna i en sammansatt entitet, se till att alla underordnade enheter som är markerade i blått, vilket innebär att de har delat i utterance.

1. Om du vill radbryta enskilda enheter till en sammansatt väljer du första märkt entiteten i utterance för sammansatta entiteten. I exempel-utterance `book 2 tickets from Seattle to Cairo`, den första enheten är antalet 2. En lista visas med alternativ för den här markeringen.

    ![Skärmbild av tal som är markerat och nedrullningsbara alternativ markerat](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Välj **omsluter sammansatt entitet** från den nedrullningsbara listan. 

    ![Skärmbild av nedrullningsbara alternativ för att byta sammansatt entitet med radbrytning i sammansatt entitet markerat](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Välj det sista ordet på den sammansatta entiteten. I utterance för det här exemplet väljer du ”Location::Destination” (som representerar Kairo). Den gröna linjen är nu under ord, inklusive icke-entiteter ord i utterance som är en kombination.

    ![Skärmbild av BookFlight avsikt sida med nummer markerat](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. Välj sammansatta entitetsnamnet från den nedrullningsbara listan. I det här exemplet som är **TicketOrder**.

    ![Skärmbild av wrapping ord med sammansatt entitet med sammansatta entitetsnamnet markerad i listrutan](./media/luis-how-to-add-example-utterances/wrap-4.png)

    När du omsluter entiteterna korrekt är en grön linje under hela frasen.

    ![Skärmbild av utterance med sammansatta entitet markerat](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Se [Data extrahering](luis-concept-data-extraction.md#composite-entity-data) lära dig mer om att extrahera sammansatt entitet från slutpunkten JSON-svar för frågan. Försök sammansatta entiteten [kursen](luis-tutorial-composite-entity.md) vill veta mer om hur du använder en sammansatt entitet.

## <a name="add-hierarchical-entity-and-label"></a>Lägg till hierarkiska entiteten och etikett
En hierarkisk entitet är en kategori av sammanhang inlärda och begreppsmässigt relaterade entiteter. I följande exempel innehåller entiteten ursprung och målplatserna. 

I utterance `Book 2 tickets from Seattle to Cairo`, Seattle är den ursprungliga platsen och Kairo är målplatsen. Varje plats är sammanhang annorlunda och inlärda word order och word val i utterance.

1. På sidan Avsiktshantering i utterance, markerar ”Seattle” och ange entitetsnamnet ' plats och välj sedan **Skapa ny entitet**.

    ![Skärmbild av skapa hierarkiska entitet etiketter dialogrutan](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. I popup-dialogrutan Välj hierarkiska för **entitetstypen**, Lägg sedan till `Origin` och `Destination` som underordnade och välj sedan **klar**.

    ![Skärmbild av Intents på sidan med ToLocation entitet markerat](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. Ordet i utterance var märkta med den överordnade hierarkiska entiteten. Du måste tilldela ordet till en underordnad entitet. Gå tillbaka till utterance på sidan avsiktshantering. Markera ord, och sedan den nedrullningsbara listan Välj entitetsnamnet som du skapade och följ på menyn till höger för att välja rätt underordnad entitet.

    ![Skärmbild av Intents på sidan med ToLocation entitet markerat](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Underordnade entitetsnamn måste vara unikt inom alla entiteter i samma app. Två olika hierarkiska enheter får inte innehålla underordnade entiteter med samma namn. 

    Se [Data extrahering](luis-concept-data-extraction.md#hierarchical-entity-data) lära dig mer om att extrahera hierarkiska entiteten från slutpunkten JSON-svar för frågan. Försök hierarkiska entiteten [quickstart](luis-quickstart-intent-and-hier-entity.md) vill veta mer om hur du använder en hierarkisk entitet.


## <a name="remove-entity-labels-from-utterances"></a>Ta bort entiteten etiketter från utterances
Du kan ta bort datorn lärt dig entitet etiketter från en utterance på sidan Avsiktshantering. Om enheten inte är datorn lärt dig kan tas inte bort från en utterance. Om du behöver ta bort en entitet som icke-machine-lärt dig från utterance måste du ta bort entiteten från hela appen. 

Välj enheten om du vill ta bort en etikett för datorn lärt dig entitet från en utterance i utterance. Välj sedan **ta bort etikett** i den nedrullningsbara listrutan för entitet som visas.

![Skärmbild av Intents på sidan med ta bort etiketten markerat](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Lägg till fördefinierade entitet etikett
Om du lägger till de fördefinierade entiteterna i appen THOMAS behöver du inte etikett utterances med dessa enheter. Läs mer om färdiga entiteter och hur du lägger till dem i [lägga till enheter](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Lägg till reguljära uttryck entitet etikett
Om du lägger till reguljärt uttryck för entiteter i appen THOMAS behöver du inte etikett utterances med dessa enheter. Läs mer om reguljära entiteter och hur du lägger till dem i [lägga till enheter](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från ett utterance
Se [Lägg till mönster från befintliga utterance på avsikten eller entiteten](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Lägg till pattern.any entitet etikett
Om du lägger till pattern.any entiteter i appen THOMAS kan du sätta etiketter utterances med dessa enheter. De är endast giltiga i mönster. Läs mer om pattern.any entiteter och hur du lägger till dem i [lägga till enheter](luis-how-to-add-entities.md#add-patternany-entities).

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>Träna din app när du har ändrat modellen med utterances
När du lägger till, redigera eller ta bort utterances, [träna](luis-how-to-train.md) och [publicera](PublishApp.md) din app för att ändringarna ska påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

Efter etikettering utterances i dina avsikter, kan du nu skapa en [sammansatt entitet](luis-how-to-add-entities.md).
