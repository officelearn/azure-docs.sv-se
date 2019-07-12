---
title: Lägga till exempelyttranden
titleSuffix: Language Understanding - Azure Cognitive Services
description: Exempel yttranden är text-exempel på användarnas frågor eller kommandon. Om du vill hålla Språkförståelse (LUIS), som du behöver lägga till exempel yttranden till en avsikt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: af43697dc864019c6fd86b041e231a54d8aa8b40
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827477"
---
# <a name="add-an-entity-to-example-utterances"></a>Lägga till en entitet till exempel yttranden 

Exempel yttranden är text-exempel på användarnas frågor eller kommandon. Om du vill hålla Språkförståelse (LUIS), måste du lägga till [exempel yttranden](luis-concept-utterance.md) till en [avsikt](luis-concept-intent.md).

Vanligtvis du lägger till en exempel-uttryck i en avsiktlig första, och sedan skapa entiteter och märka yttranden på den **avsikter** sidan. Om du skulle i stället först skapa entiteter, se [Lägg till entiteter](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Markera entiteter i exempel yttranden

När du väljer text i exempel-uttryck för att markera för en entitet, visas ett popup-menyn på plats. Använd den här menyn för att skapa eller välj en entitet. 

Vara det går inte att taggade vissa entitetstyper, till exempel förskapade entiteter och entiteter för reguljärt uttryck, i exempel-uttryck eftersom de är märkta automatiskt. 

## <a name="add-a-simple-entity"></a>Lägg till en enkel enhet

I följande procedur skapar du och tagga en anpassad entitet i följande uttryck den **avsikter** sidan:

```text
Are there any SQL server jobs?
```

1. Välj `SQL server` i uttryck till att det är en enkel enhet. I entiteten listrutan rutan som visas kan du väljer en befintlig entitet eller lägga till en ny entitet. Om du vill lägga till en ny entitet, skriver du namnet `Job` i textrutan och välj sedan **Skapa ny entitet**.

    ![Skärmbild av hur du anger entitetsnamn](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > När du väljer ord att taggen som enheter:
    > * För ett enstaka ord bara välja den. 
    > * För en uppsättning med två eller flera ord, väljer du det första ordet och sedan på det sista ordet.

1. I den **vilken typ av enhet vill du skapa?** popup-rutan, verifiera enhetens namn och välj den **enkel** entitetstypen och välj sedan **klar**.

    En [frasen lista](luis-concept-feature.md) ofta används för att öka signalen från en enkel enhet.

## <a name="add-a-list-entity"></a>Lägg till en entitet i listan

Lista över entiteter representerar en uppsättning exakt denna matchningar av närstående ord i systemet. 

Ett företag avdelning lista du kan ha normalized värden: `Accounting` och `Human Resources`. Varje normaliserade namn har synonymer. För en avdelning, kan dessa synonymer innehåller alla avdelning förkortningar, tal eller slang. Du behöver veta alla värden när du har skapat entiteten. Du kan lägga till fler när du har granskat hur användarna yttranden med synonymer.

1. I en exempel-uttryck på den **avsikter** väljer ord eller fraser som du vill i den nya listan. När entiteten listrutan visas anger du namnet för den nya entiteten i listan i textrutan längst upp och välj sedan **Skapa ny entitet**.   

1. I den **vilken typ av enhet vill du skapa?** popup-rutan, namn på entiteten och väljer **lista** som typ av. Lägga till synonymer för det här listobjektet och välj sedan **klar**. 

    ![Skärmbild av hur du anger lista entitet synonymer](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Du kan lägga till fler listobjekt eller flera objekt synonymer genom märkning andra uttryck eller genom att redigera entiteten från den **entiteter** i det vänstra navigeringsfönstret. [Redigera](luis-how-to-add-entities.md#add-list-entities) entiteterna ger dig alternativ för att ange ytterligare objekt med motsvarande synonymer eller importera en lista. 

## <a name="add-a-composite-entity"></a>Lägg till en sammansatt entitet

Sammansatta entiteter skapas från befintliga **entiteter** för att bilda en överordnad enhet. 

Om vi antar att uttryck, `Does John Smith work in Seattle?`, ett sammansatt uttryck kan returnera entitetsinformation av medarbetarnamn `John Smith`, och platsen `Seattle` i en sammansatt entitet. Underordnade entiteter måste redan finnas i appen och markeras i exempel-uttryck innan du skapar sammansatta entiteten.

1. Om du vill radbryta underordnade entiteter i en sammansatt entitet, Välj den **första** märkta entitet (vänster) i uttryck för sammansatt entiteten. En listrutan visas alternativen för det här alternativet.

1. Välj **omsluta i sammansatt entitet** från den nedrullningsbara listan. 

1. Välj det sista ordet sammansatta entitetens (höger – de flesta). Observera att en grön linje följer sammansatta entiteten. Detta är en visuell indikering för en sammansatt entitet och bör vara under alla ord i sammansatt entiteten från vänster underordnade entiteten till höger underordnad entitet.

1. Ange sammansatta enhetens namn i den nedrullningsbara listan.

    När du omsluter entiteterna korrekt är en grön linje under hela frasen.

1. Verifiera de sammansatta entitetsinformation på den **vilken typ av enhet vill du skapa?** popup-fönster och sedan markerar du kryssrutan **klar**.

    ![Skärmbild av entitetsinformation popup](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Sammansatta entiteten visar med båda blå markeringar för enskilda enheter och en grön linje för hela sammansatta entiteten. 

    ![Skärmbild av avsikter detaljsidan med sammansatta entitet](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Lägga till enhetens roll till uttryck

En roll är en namngiven undertyp till en entitet, bestäms av kontexten för uttryck. Du kan markera en entitet i ett uttryck som entiteten eller välj en roll i entiteten. En person kan ha roller, inklusive anpassade entiteter som är datorn lärt dig (enkla enheter och sammansatta entiteter), är inte datorn lärt dig (fördefinierade entiteter, reguljärt uttryck entiteter, lista över entiteter). 

Lär dig [så Markera ett uttryck med entiteten roller](tutorial-entity-roles.md) från en praktisk vägledning. 

## <a name="entity-status-predictions"></a>Entiteten status förutsägelser

När du anger en ny uttryck i LUIS-portalen kan uttryck innehåller entiteten förutsägelse fel. Felet förutsägelse är någon skillnad mellan hur en entitet är märkt jämfört med hur LUIS har förutse entiteten. 

Den här skillnaden representeras visuellt i LUIS-portalen med en röd understrykning i uttryck. Understrykningen får visas inom hakparentes entitet eller utanför hakparenteser. 

![Skärmbild av entiteten status förutsägelse avvikelse](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Välj de ord som är understruket i rött i uttryck. 

Rutan entiteten visar den **status för enheter** med ett rött utropstecken om det finns en diskrepans förutsägelse. Om du vill se status för enheter med information om skillnaden mellan taggade och förväntade entiteter **status för enheter** Välj ett objekt till höger.

![Skärmbild av entiteten statusvalet](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

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

Du kan ta bort datorn lärt dig entitet etiketter från ett uttryck på sidan avsikter. Om enheten inte är datorn lärt dig, kan inte tas bort från ett uttryck. Om du vill ta bort en entitet som icke-machine-lärt dig från uttryck kan behöva du ta bort enheten från hela appen. 

Om du vill ta bort en etikett för datorn lärt dig entitet från ett uttryck, väljer du entitet i uttryck. Välj sedan **ta bort etiketten** i den nedrullningsbara listrutan för entiteten som visas.

### <a name="add-a-prebuilt-entity-label"></a>Lägg till en etikett med färdiga entitet

När du lägger till de fördefinierade entiteterna LUIS-appen, behöver du inte taggen yttranden med dessa entiteter. Läs mer om fördefinierade entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Lägg till en etikett för reguljärt uttryck entitet

Om du lägger till entiteterna reguljärt uttryck LUIS-appen, behöver du inte att tagga yttranden med dessa entiteter. Läs mer om reguljärt uttryck entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från ett uttryck

Se [Lägg till mönster från befintliga uttryck på avsikten eller entitet](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Lägg till en pattern.any-entitet

Om du lägger till entiteterna pattern.any LUIS-appen kan inte du märka yttranden med dessa entiteter. De är endast giltiga i ett mönster. Läs mer om pattern.any entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Träna din app när du har ändrat modell med yttranden

När du lägger till, redigera eller ta bort yttranden, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för ändringarna att påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

Efter märkning yttranden i din **avsikter**, nu kan du skapa en [sammansatt entitet](luis-how-to-add-entities.md).
