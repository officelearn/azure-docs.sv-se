---
title: Lägga till exempelyttranden
titleSuffix: Language Understanding - Azure Cognitive Services
description: Exempel yttranden är text-exempel på användarnas frågor eller kommandon. Om du vill hålla Språkförståelse (LUIS), som du behöver lägga till exempel yttranden till en avsikt.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 2da704f4b0d7ed0d7038b5ef9b183d9b60f5e639
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223176"
---
# <a name="add-an-entity-to-example-utterances"></a>Lägga till en entitet till exempel yttranden 

Exempel yttranden är text-exempel på användarnas frågor eller kommandon. Om du vill hålla Språkförståelse (LUIS), måste du lägga till [exempel yttranden](luis-concept-utterance.md) till en [avsikt](luis-concept-intent.md).

Vanligtvis lägga till en exempel-uttryck i ett intent först och sedan skapa entiteter och etiketten yttranden på sidan avsikt. Om du skulle i stället först skapa entiteter, se [Lägg till entiteter](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Markera entiteter i exempel yttranden

När du väljer text i exempel-uttryck för att markera för en entitet, visas ett popup-menyn på plats. Använd den här menyn för att skapa eller välj en entitet. 

Vara det går inte att taggade vissa entitetstyper, till exempel förskapade entiteter och entiteter för reguljärt uttryck, i exempel-uttryck eftersom de är märkta automatiskt. 

## <a name="add-a-simple-entity"></a>Lägg till en enkel enhet

I följande procedur skapar du och tagga en anpassad entitet i följande uttryck på sidan avsikt:

```text
Does John Smith work in Seattle?
```

1. Välj `Seattle` i uttryck till att det är en enkel enhet.

    [![Skärmbild över att välja text i uttryck för enkel enhet](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)

    > [!NOTE]
    > När du väljer ord att taggen som enheter:
    > * För ett enstaka ord bara välja den. 
    > * För en uppsättning med två eller flera ord, väljer du i början och slutet av uppsättningen.

1. I entiteten listrutan rutan som visas kan du väljer en befintlig entitet eller lägga till en ny entitet. Om du vill lägga till en ny entitet, skriver du namnet i textrutan och välj sedan **Skapa ny entitet**. 

    ![Skärmbild av hur du anger entitetsnamn](./media/luis-how-to-add-example-utterances/hr-create-simple-2.png)

1. I den **vilken typ av enhet vill du skapa?** popup-rutan, verifiera enhetens namn och välj den **enkel** entitetstypen och välj sedan **klar**.

    En [frasen lista](luis-concept-feature.md) ofta används för att öka signalen från en enkel enhet.

## <a name="add-a-list-entity"></a>Lägg till en entitet i listan

Lista över entiteter representerar en fast, stängda uppsättning (exakt denna matchar) närstående ord i systemet. 

Ett företag avdelning lista du kan ha normalized värden: `Accounting` och `Human Resources`. Varje normaliserade namn har synonymer. För en avdelning, kan dessa synonymer innehåller alla avdelning förkortningar, tal eller slang. Du behöver veta alla värden när du har skapat entiteten. Du kan lägga till fler när du har granskat hur användarna yttranden med synonymer.

1. Välj i listan exempel uttryck för en specifik uttryck ordet eller frasen som du vill ha i den nya listan. Sedan anger du namnet på listan i textrutan längst upp och välj sedan **Skapa ny entitet**.   

    ![Skärmbild av hur du anger lista entitetsnamn](./media/luis-how-to-add-example-utterances/hr-create-list-1.png)


1. I den **vilken typ av enhet vill du skapa?** popup-rutan, namn på entiteten och väljer **lista** som typ av. Lägga till synonymer för det här listobjektet och välj sedan **klar**. 

    ![Skärmbild av hur du anger lista entitet synonymer](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Du kan lägga till fler listobjekt eller flera objekt synonymer genom märkning andra uttryck eller genom att redigera entiteten från den **entiteter** i det vänstra navigeringsfönstret. [Redigera](luis-how-to-add-entities.md#add-list-entities) entiteterna ger dig alternativ för att ange ytterligare objekt med motsvarande synonymer eller importera en lista. 

## <a name="add-composite-entity"></a>Lägg till sammansatt entitet

Sammansatta entiteter skapas från befintliga **entiteter** till den överordnade-enheten. 

Om vi antar att uttryck, `Does John Smith work in Seattle?`, ett sammansatt uttryck kan returnera entitetsinformation medarbetarnamn och plats i ett enda överordnat objekt. 

Medarbetarnamn, John Smith, är en fördefinierade [personName](luis-reference-prebuilt-person.md) entitet. Plats, Seattle, är en enkel anpassad entitet. När dessa två entiteter har skapats och taggas i en exempel-uttryck, kan dessa entiteter inneslutas i en sammansatt entitet. 

1. Om du vill radbryta enskilda enheter till en sammansatta, Välj den **första** märkta entitet (vänster) i uttryck för sammansatt entiteten. En listrutan visas alternativen för det här alternativet.

1. Välj **omsluta sammansatt entitet** från den nedrullningsbara listan. 

    ![Skärmbild av Välj ”radbyte i sammansatt entitet”](./media/luis-how-to-add-example-utterances/hr-create-composite-1.png)

1. Välj det sista ordet sammansatta entitetens (höger – de flesta). Observera att en grön linje följer sammansatta entiteten.

1. Ange sammansatta enhetens namn i den nedrullningsbara listan.

    ![Skärmbild av ange sammansatta enhetens namn i den nedrullningsbara listan](./media/luis-how-to-add-example-utterances/hr-create-composite-2.png)

    När du omsluter entiteterna korrekt är en grön linje under hela frasen.

1. Verifiera de sammansatta entitetsinformation på den **vilken typ av enhet vill du skapa?** popup-fönster och sedan markerar du kryssrutan **klar**.

    ![Skärmbild av entitetsinformation popup](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Sammansatta entiteten visar med båda blå markeringar för enskilda enheter och en grön linje för hela sammansatta entiteten. 

    ![Skärmbild av avsikter detaljsidan med sammansatta entitet](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>Lägg till hierarkisk entitet

En hierarkisk entitet är en viss kategori av sammanhangsmässigt inlärda och begreppsmässigt relaterade entiteter. I följande exempel innehåller entiteten ursprung- och målplatserna. 

I uttryck `Move John Smith from Seattle to Cairo`, Seattle är ursprungsplatsen och Kairo är målplatsen. Varje plats är sammanhangsmässigt annan, inlärda från ordföljden och word valet i uttryck.

1. På sidan avsikt i uttryck, Välj `Seattle`, ange entitetsnamnet `Location`, och tryck sedan på RETUR på tangentbordet.

    ![Skärmbild av skapa hierarkiska entitet märkning dialogrutan](./media/luis-how-to-add-example-utterances/hr-hier-1.png)

1. I den **vilken typ av enhet vill du skapa?** popup-rutan, väljer _hierarkiska_ för **entitetstypen**, Lägg sedan till `Origin` och `Destination` som barn, Välj sedan **klar**.

    ![Skärmbild av avsikter detaljsidan med ToLocation entitet markerat](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. Ordet i uttryck etiketterades med den överordnade hierarkiska entiteten. Du måste tilldela ordet till en underordnad entitet. Gå tillbaka till uttryck på sidan med avsikt. Markera ord, och sedan välja entitetsnamn som du skapade från den nedrullningsbara listan och följ på menyn till höger för att välja rätt underordnad entitet.

    ![Skärmbild av avsikter informationssidan där du måste tilldela ordet till en underordnad entitet](./media/luis-how-to-add-example-utterances/hr-hier-3.png)

    >[!CAUTION]
    >Namn på underordnade entiteter måste vara unikt inom alla entiteter i samma app. Två olika hierarkiska entiteter får inte innehålla underordnade entiteter med samma namn. 

## <a name="entity-status-predictions"></a>Entiteten status förutsägelser

När du anger en ny uttryck i LUIS-portalen kan uttryck innehåller entiteten förutsägelse fel. Felet förutsägelse är någon skillnad mellan hur en entitet är märkt jämfört med hur LUIS har förutse entiteten. 

Den här skillnaden representeras visuellt i LUIS-portalen med en röd understrykning i uttryck. Understrykningen får visas inom hakparentes entitet eller utanför hakparenteser. 

![Skärmbild av entiteten status förutsägelse avvikelse](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Välj de ord som är understruket i rött i uttryck. 

Rutan entiteten visar den **status för enheter** med ett rött utropstecken om det finns en diskrepans förutsägelse. Om du vill se status för enheter med information om skillnaden mellan taggade och förväntade entiteter **status för enheter** Välj ett objekt till höger.

![Skärmbild över att välja rätt objekt för att åtgärda förutsägelse avvikelse](./media/luis-how-to-add-example-utterances/entity-status.png)

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

## <a name="other-actions"></a>Andra åtgärder

Du kan utföra åtgärder på exempel yttranden som en vald grupp eller som ett enskilt objekt. Grupper med valda exempel yttranden ändra popup-menyn ovanför listan. Enstaka objekt kan använda både popup-menyn ovanför listan och enskilda sammanhangsberoende ellipsen i slutet av varje rad i uttryck. 

### <a name="remove-entity-labels-from-utterances"></a>Ta bort entitet etiketter yttranden

Du kan ta bort datorn lärt dig entitet etiketter från ett uttryck på sidan avsikt. Om enheten inte är datorn lärt dig, kan inte tas bort från ett uttryck. Om du vill ta bort en entitet som icke-machine-lärt dig från uttryck kan behöva du ta bort enheten från hela appen. 

Om du vill ta bort en etikett för datorn lärt dig entitet från ett uttryck, väljer du entitet i uttryck. Välj sedan **ta bort etiketten** i den nedrullningsbara listrutan för entiteten som visas.

![Skärmbild av avsikter detaljsidan med ta bort etiketten markerat](./media/luis-how-to-add-example-utterances/remove-label.png) 

### <a name="add-prebuilt-entity-label"></a>Lägg till fördefinierade entitet etikett

När du lägger till de fördefinierade entiteterna LUIS-appen, behöver du inte taggen yttranden med dessa entiteter. Läs mer om fördefinierade entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-prebuilt-entity).

### <a name="add-regular-expression-entity-label"></a>Lägg till reguljärt uttryck entitet etikett

Om du lägger till entiteterna reguljärt uttryck LUIS-appen, behöver du inte att tagga yttranden med dessa entiteter. Läs mer om reguljärt uttryck entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-regular-expression-entities).


### <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från ett uttryck

Se [Lägg till mönster från befintliga uttryck på avsikten eller entitet](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Lägg till pattern.any entitet

Om du lägger till entiteterna pattern.any LUIS-appen kan inte du märka yttranden med dessa entiteter. De är endast giltiga i ett mönster. Läs mer om pattern.any entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-patternany-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Träna din app när du har ändrat modell med yttranden

När du lägger till, redigera eller ta bort yttranden, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för ändringarna att påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

När märkning yttranden i dina avsikter, kan du nu skapa en [sammansatt entitet](luis-how-to-add-entities.md).
