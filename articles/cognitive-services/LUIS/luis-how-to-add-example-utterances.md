---
title: Lägg till exempel yttranden i LUIS-appar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du lägger till yttranden i Språkförståelse (LUIS) program.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/10/2018
ms.author: diberry
ms.openlocfilehash: 6a3edfd426fcdce83bd60332ba2b1ff6224dae1a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645567"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Lägg till exempel yttranden och en etikett med entiteter

Exempel yttranden är text-exempel på användarnas frågor eller kommandon. Om du vill hålla Språkförståelse (LUIS), måste du lägga till [exempel yttranden](luis-concept-utterance.md) till en [avsikt](luis-concept-intent.md).

I allmänhet kan lägga till en exempel-uttryck i ett intent först och sedan skapa entiteter och etiketten yttranden på sidan avsikt. Om du skulle i stället först skapa entiteter, se [Lägg till entiteter](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Lägg till ett uttryck
Ange ett uttryck för relevanta exempel som du förväntar dig från dina användare, till exempel på en avsiktlig sida `book 2 adult business tickets to Paris tomorrow on Air France` i textrutan nedan avsikt namn och tryck på RETUR. 
 
>[!NOTE]
>LUIS konverterar alla yttranden till gemener.

![Skärmbild av avsikter detaljsidan med uttryck markerat](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Yttranden läggs till i listan yttranden för den aktuella avsikten. 

## <a name="ignoring-words-and-punctuation"></a>Ignorerar ord och skiljetecken
Om du vill ignorera vissa specifika ord eller skiljetecken i exempel-uttryck kan använda en [mönstret](luis-concept-patterns.md#pattern-syntax) med den _Ignorera_ syntax. 

## <a name="add-simple-entity-label"></a>Lägg till enkel enhet etikett
I följande procedur skapar du och märka anpassade entiteter i följande uttryck på sidan avsikt:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Välj ”Air France” i uttryck till att det är en enkel enhet.

    > [!NOTE]
    > När du väljer ord att märka dem som enheter:
    > * För ett enstaka ord bara välja den. 
    > * För en uppsättning med två eller flera ord, väljer du i början och slutet av uppsättningen.

2. I entiteten listrutan rutan som visas kan du väljer en befintlig entitet eller lägga till en ny entitet. Om du vill lägga till en ny entitet, skriver du namnet i textrutan och välj sedan **Skapa ny entitet**. 
 
    ![Skärmbild av avsikter sidan, med enkel enhet Märkning alternativet markerat](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. I den **vilken typ av enhet vill du skapa?** popup-dialogrutan Kontrollera enhetens namn och välj typ av enkel enhet och välj sedan **klar**.

    ![Bild av bekräftelsedialogrutan](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Se [Dataextrahering](luis-concept-data-extraction.md#simple-entity-data) mer information om hur du extraherar den enkla enheten från slutpunkten JSON frågesvaret. Testa enkel enhet [snabbstarten](luis-quickstart-primary-and-secondary-data.md) mer information om hur du använder en enkel enhet.


## <a name="add-list-entity-and-label"></a>Lägg till listan entitets- och etikett
Lista över entiteter representerar en fast, stängda uppsättning (exakt denna matchar) närstående ord i systemet. 

Du kan ha två normaliserade värden för en lista över entitet drycker: vatten och soda pop. Varje normaliserade namn har synonymer. För vatten är synonymer H20, gas, fast. Synonymer är frukt, cola, ingefära för soda pop. Du behöver veta alla värden när du har skapat entiteten. Du kan lägga till fler när du har granskat hur användarna yttranden med synonymer.

|Normaliserade namn|Synonymer|
|--|--|
|Vatten|H20 gas, fast|
|Soda pop|Frukt, cola, ingefära|

När du skapar en ny entitet i listan från sidan avsikt, gör du två saker som inte kanske tydligt. Först skapar du en ny lista genom att lägga till det första objektet i listan. Andra heter det första objektet i listan med ordet eller frasen som du har valt från uttryck. Medan du kan ändra dem senare från sidan entiteten, kan det vara snabbare att välja ett uttryck som innehåller ordet för namnet på listobjektet.

Till exempel om du vill skapa en lista över typer av dryck och du har valt ordet `h2o` från uttryck att skapa entiteten listan skulle ha ett objekt, vars namn har h20. Om du vill ha ett mer allmänt namn bör du välja ett uttryck som använder mer allmänt namn. 

1. I uttryck, väljer du det ord som är det första objektet i listan och sedan anger du namnet på listan i textrutan och välj sedan **Skapa ny entitet**.   

    ![Skärmbild av avsikter detaljsidan med Skapa ny entitet markerat](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. I den **vilken typ av enhet vill du skapa?** dialogrutan lägger du till synonymer för det här listobjektet. För vatten-objekt i en dryck-lista, lägger du till `h20`, `perrier`, och `waters`, och välj **klar**. Observera att ”vatten” har lagts till eftersom listan synonymer matchas på nivån token. I den engelska kulturen att nivå är på nivån word så ”vatten” skulle inte matchas ”water” om det inte var i listan. 

    ![Skärmbild av vilken typ av enhet du vill skapa dialogrutan](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Den här listan över drycker har endast en dryck typ water. Du kan lägga till fler typer av dryck genom märkning andra uttryck eller genom att redigera entiteten från den **entiteter** i det vänstra navigeringsfönstret. [Redigera](luis-how-to-add-entities.md#add-list-entities) entiteterna ger dig alternativ för att ange ytterligare objekt med motsvarande synonymer eller [importera](luis-how-to-add-entities.md#import-list-entity-values) en lista. 

    Se [Dataextrahering](luis-concept-data-extraction.md#list-entity-data) mer information om extrahering av listan över entiteter från slutpunkten JSON frågesvaret. Prova den [snabbstarten](luis-quickstart-intent-and-list-entity.md) mer information om hur du använder en entitet i listan.

## <a name="add-synonyms-to-the-list-entity"></a>Lägg till synonymer i entiteten lista 
Lägg till en synonym till entiteten listan genom att välja ord eller fraser i uttryck. Om du har en dryck listan entitet och vill lägga till `agua` som identisk med vatten, följer du stegen:

I uttryck, välja synonyma ord, till exempel `aqua` för vatten, välj sedan namnet på entiteten i listrutan, som **dricka**och välj sedan **som synonymen**, välj sedan listan objekt som det är synonyma med, till exempel **water**.

![Skärmbild av avsikter detaljsidan med att skapa en ny normaliserade vy markerat](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Skapa en ny lista entitet
Skapa ett nytt objekt för en befintlig entitet i listan genom att välja ord eller fraser i uttryck. Om du har en anställd listan och vill lägga till `Bob Smith` som ett nytt objekt, följer du stegen:

I uttryck, välja ordet eller frasen för det nya listobjektet, till exempel `Bob Smith`, välj sedan namnet på entiteten i i listrutan, **medarbetare**och välj sedan **skapar en ny normaliserade vy**. 

![Skärmbild för att lägga till nya listobjekt](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

Ordet markeras nu i blått. Om du hovrar över ordet visas en tagg som visar listan över objektnamn, till exempel te.

![Skärmbild av ny lista objekt tagg](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Omsluta entiteter i sammansatt etikett
Sammansatta entiteter har skapats från **entiteter**. Du kan inte skapa en sammansatt entitet från sidan avsikt. När den sammansatta entitet har skapats kan omsluta du entiteterna i ett uttryck på sidan avsikt. 

Om vi antar att uttryck, `book 2 tickets from Seattle to Cairo`, ett sammansatt uttryck kan returnera entitetsinformation av antal biljetter (2), ursprung (Seattle) och mål (Kairo) platser i en viss överordnade entiteten. 

Följ dessa [steg](luis-how-to-add-entities.md#add-prebuilt-entity) att lägga till den **nummer** fördefinierade entitet. När entiteten har skapats kan den `2` i uttryck är blå, som anger det är en taggade entitet. Fördefinierade entiteter är märkta med LUIS. Du kan inte lägga till eller ta bort etiketten fördefinierade entitet från en enda uttryck. Du kan bara lägga till eller ta bort alla färdiga etiketter genom att lägga till eller ta bort fördefinierade entiteten från programmet.

Följ dessa [steg](#add-hierarchical-entity-and-label) att skapa en **plats** hierarkiska entitet. Etiketten original och beskrivning platser i exempel-uttryck. 

Innan du omsluter entiteter i en sammansatt entitet, kontrollera att alla underordnade entiteter är markerade i blått, vilket innebär att de har fått en etikett i uttryck.

1. Om du vill radbryta enskilda enheter till en sammansatta, väljer du den första taggade entitet i uttryck för entiteten sammansatta. I exempel-uttryck `book 2 tickets from Seattle to Cairo`, den första entiteten är antalet 2. En listrutan visas alternativen för det här alternativet.

    ![Skärmbild av tal som är markerat och listrutan alternativ som markerats](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Välj **omsluta sammansatt entitet** från den nedrullningsbara listan. 

    ![Skärmbild av nedrullningsbara alternativen för att radbryta sammansatt entitet med radbyte i sammansatt entitet markerat](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Välj det sista ordet av sammansatta entiteten. I uttryck i det här exemplet väljer du ”Location::Destination” (som representerar Kairo). Den gröna linjen är nu under ord, inklusive icke-entiteter ord i uttryck som är sammansatt.

    ![Skärmbild av BookFlight avsikt sida med tal som är markerat](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. Välj sammansatta entitetsnamnet från den nedrullningsbara listan. I det här exemplet är **TicketOrder**.

    ![Skärmbild av wrapping ord med sammansatta entitet med sammansatta entitetsnamn som är markerat i listrutan](./media/luis-how-to-add-example-utterances/wrap-4.png)

    När du omsluter entiteterna korrekt är en grön linje under hela frasen.

    ![Skärmbild av uttryck med sammansatta entitet markerat](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Se [Dataextrahering](luis-concept-data-extraction.md#composite-entity-data) mer information om hur du extraherar den sammansatta entiteten från slutpunkten JSON frågesvaret. Försök sammansatta entiteten [självstudien](luis-tutorial-composite-entity.md) mer information om hur du använder en sammansatt entitet.

## <a name="add-hierarchical-entity-and-label"></a>Lägg till hierarkiska entitets- och etikett
En hierarkisk entitet är en viss kategori av sammanhangsmässigt inlärda och begreppsmässigt relaterade entiteter. I följande exempel innehåller entiteten ursprung- och målplatserna. 

I uttryck `Book 2 tickets from Seattle to Cairo`, Seattle är ursprungsplatsen och Kairo är målplatsen. Varje plats är sammanhangsmässigt annan, inlärda från ordföljden och word valet i uttryck.

1. På sidan avsikt i uttryck, Välj ”Seattle” och sedan ange enhetens namn, plats och välj sedan **Skapa ny entitet**.

    ![Skärmbild av skapa hierarkiska entitet märkning dialogrutan](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. I popup-dialogrutan väljer du hierarkiska för **entitetstypen**, Lägg sedan till `Origin` och `Destination` som barn och välj sedan **klar**.

    ![Skärmbild av avsikter detaljsidan med ToLocation entitet markerat](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. Ordet i uttryck etiketterades med den överordnade hierarkiska entiteten. Du måste tilldela ordet till en underordnad entitet. Gå tillbaka till uttryck på sidan avsikt. Markera ord, och sedan välja entitetsnamn som du skapade från den nedrullningsbara listan och följ på menyn till höger för att välja rätt underordnad entitet.

    ![Skärmbild av avsikter detaljsidan med ToLocation entitet markerat](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Namn på underordnade entiteter måste vara unikt inom alla entiteter i samma app. Två olika hierarkiska entiteter får inte innehålla underordnade entiteter med samma namn. 

    Se [Dataextrahering](luis-concept-data-extraction.md#hierarchical-entity-data) mer information om hur du extraherar den hierarkiska entiteten från slutpunkten JSON frågesvaret. Försök hierarkisk entiteten [snabbstarten](luis-quickstart-intent-and-hier-entity.md) mer information om hur du använder en hierarkisk entitet.

## <a name="entity-status-predictions"></a>Entiteten status förutsägelser

När du anger en ny uttryck i LUIS-portalen kan uttryck innehåller entiteten förutsägelse fel. Felet förutsägelse är någon skillnad mellan hur en entitet är märkt jämfört med hur LUIS har förutse entiteten. 

Den här skillnaden representeras visuellt i LUIS-portalen med en röd understrykning i uttryck. Understrykningen får visas inom hakparentes entitet eller utanför hakparenteser. 

![Skärmbild av entiteten status förutsägelse avvikelse](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Välj de ord som är understruket i rött i uttryck. 

Rutan entiteten visar den **status för enheter** med ett rött utropstecken om det finns en diskrepans förutsägelse. Om du vill se status för enheter med information om skillnaden mellan taggade och förväntade entiteter **status för enheter** Välj ett objekt till höger.

![Skärmbild av entiteten status förutsägelse avvikelse](./media/luis-how-to-add-example-utterances/entity-status.png)

Röd-rad visas på någon av följande tidpunkter:

    * När ett uttryck har angetts men innan entiteten är märkt med
    * När entiteten etiketten används
    * När entiteten etiketten tas bort
    * När fler än en entitet etikett förväntas för texten 

På följande sätt att lösa entitet förutsägelse avvikelse:

|Entitet|Visuell indikator|förutsägelse|Lösning|
|--|--|--|--|
|Uttryck som angav entiteten är inte märkta ännu.|röd linje|Förutsägelse är korrekt.|Etiketten entiteten med det förväntade värdet.|
|Utan etikett text|röd linje|Felaktig förutsägelse|De aktuella yttranden som använder den här felaktiga entiteten måste granskas för alla avsikter. De aktuella yttranden har mistaught LUIS att den här texten är den förväntade entiteten.
|Korrekt taggade text|Markera blå entitet, röd understrykning|Felaktig förutsägelse|Ger mer yttranden med entiteten korrekt taggade på olika platser och användningsområden. De aktuella yttranden är inte tillräckliga för att lära LUIS att detta är entiteten är eller liknande entiteterna visas i samma kontext. Liknande entitet ska kombineras till en enda entitet så LUIS är inte blandas ihop. En annan lösning är att lägga till en fras lista för att höja betydelsen av orden. |
|Felaktigt taggade text|Markera blå entitet, röd understrykning|Rätt förutsägelse| Ger mer yttranden med entiteten korrekt taggade på olika platser och användningsområden. 


## <a name="remove-entity-labels-from-utterances"></a>Ta bort entitet etiketter yttranden
Du kan ta bort datorn lärt dig entitet etiketter från ett uttryck på sidan avsikt. Om enheten inte är datorn lärt dig, kan inte tas bort från ett uttryck. Om du vill ta bort en entitet som icke-machine-lärt dig från uttryck kan behöva du ta bort enheten från hela appen. 

Om du vill ta bort en etikett för datorn lärt dig entitet från ett uttryck, väljer du entitet i uttryck. Välj sedan **ta bort etiketten** i den nedrullningsbara listrutan för entiteten som visas.

![Skärmbild av avsikter detaljsidan med ta bort etiketten markerat](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Lägg till fördefinierade entitet etikett
Om du lägger till de fördefinierade entiteterna LUIS-app, behöver du inte att etiketten yttranden med de här entiteterna. Läs mer om fördefinierade entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Lägg till reguljärt uttryck entitet etikett
Om du lägger till entiteterna reguljärt uttryck LUIS-app, behöver du inte att etiketten yttranden med dessa entiteter. Läs mer om reguljärt uttryck entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från ett uttryck
Se [Lägg till mönster från befintliga uttryck på avsikten eller entitet](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Lägg till pattern.any entitet etikett
Om du lägger till entiteterna pattern.any LUIS-appen kan inte du märka yttranden med dessa entiteter. De är endast giltiga i ett mönster. Läs mer om pattern.any entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-patternany-entities).

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
## <a name="train-your-app-after-changing-model-with-utterances"></a>Träna din app när du har ändrat modell med yttranden
När du lägger till, redigera eller ta bort yttranden, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för ändringarna att påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

När märkning yttranden i dina avsikter, kan du nu skapa en [sammansatt entitet](luis-how-to-add-entities.md).
