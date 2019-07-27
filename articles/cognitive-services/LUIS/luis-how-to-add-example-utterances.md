---
title: Lägg till exempel yttranden-LUIS
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: f02f50eef971e774236f534c0d664dfcf090dcae
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563733"
---
# <a name="add-an-entity-to-example-utterances"></a>Lägga till en entitet till exempel yttranden 

Exempel yttranden är text-exempel på användarnas frågor eller kommandon. Om du vill hålla Språkförståelse (LUIS), måste du lägga till [exempel yttranden](luis-concept-utterance.md) till en [avsikt](luis-concept-intent.md).

Normalt lägger du till ett exempel uttryck i ett avsikts syfte och sedan skapar du entiteter och etiketten yttranden på sidan **avsikter** . Om du skulle i stället först skapa entiteter, se [Lägg till entiteter](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Markera entiteter i exempel yttranden

När du väljer text i exempel-uttryck för att markera för en entitet, visas ett popup-menyn på plats. Använd den här menyn för att skapa eller välj en entitet. 

Vara det går inte att taggade vissa entitetstyper, till exempel förskapade entiteter och entiteter för reguljärt uttryck, i exempel-uttryck eftersom de är märkta automatiskt. 

## <a name="add-a-simple-entity"></a>Lägg till en enkel enhet

I följande procedur skapar du och taggar en anpassad entitet i följande uttryck på sidan avsikter:

```text
Are there any SQL server jobs?
```

1. Välj `SQL server` i uttryck till att det är en enkel enhet. I entiteten listrutan rutan som visas kan du väljer en befintlig entitet eller lägga till en ny entitet. Lägg till en ny entitet genom att skriva dess `Job` namn i text rutan och sedan välja **Skapa ny entitet**.

    ![Skärmbild av hur du anger entitetsnamn](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > När du väljer ord att taggen som enheter:
    > * För ett enstaka ord bara välja den. 
    > * För en uppsättning med två eller flera ord väljer du det första ordet och sedan det sista ordet.

1. I den **vilken typ av enhet vill du skapa?** popup-rutan, verifiera enhetens namn och välj den **enkel** entitetstypen och välj sedan **klar**.

    En [frasen lista](luis-concept-feature.md) ofta används för att öka signalen från en enkel enhet.

## <a name="add-a-list-entity"></a>Lägg till en entitet i listan

Lista entiteter representerar en uppsättning exakta text matchningar av relaterade ord i systemet. 

Ett företag avdelning lista du kan ha normalized värden: `Accounting` och `Human Resources`. Varje normaliserade namn har synonymer. För en avdelning, kan dessa synonymer innehåller alla avdelning förkortningar, tal eller slang. Du behöver veta alla värden när du har skapat entiteten. Du kan lägga till fler när du har granskat hur användarna yttranden med synonymer.

1. I ett exempel på uttryck på  sidan avsikter väljer du ordet eller frasen som du vill ha i den nya listan. När List rutan entitet visas anger du namnet på den nya List-entiteten i den översta text rutan och väljer sedan **Skapa ny entitet**.   

1. I den **vilken typ av enhet vill du skapa?** popup-rutan, namn på entiteten och väljer **lista** som typ av. Lägga till synonymer för det här listobjektet och välj sedan **klar**. 

    ![Skärmbild av hur du anger lista entitet synonymer](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Du kan lägga till fler listobjekt eller flera objekt synonymer genom märkning andra uttryck eller genom att redigera entiteten från den **entiteter** i det vänstra navigeringsfönstret. [Redigera](luis-how-to-add-entities.md#add-list-entities) entiteterna ger dig alternativ för att ange ytterligare objekt med motsvarande synonymer eller importera en lista. 

## <a name="add-a-composite-entity"></a>Lägg till en sammansatt entitet

Sammansatta entiteter skapas  från befintliga entiteter för att bilda en överordnad entitet. 

Vid antagandet av uttryck `Does John Smith work in Seattle?`kan en sammansatt uttryck returnera enhets information om medarbetarens namn `John Smith`och platsen `Seattle` i en sammansatt entitet. De underordnade entiteterna måste redan finnas i appen och markeras i exemplet uttryck innan den sammansatta entiteten skapas.

1. Om du vill omsluta underordnade entiteter till en sammansatt entitet väljer du den **första** märkta entiteten (längst till vänster) i uttryck för den sammansatta entiteten. En nedrullningsbar listruta visas för att Visa valen för det här valet.

1. Välj **Radbryt i sammansatt entitet i** den nedrullningsbara listan. 

1. Välj det sista ordet sammansatta entitetens (höger – de flesta). Observera att en grön linje följer sammansatta entiteten. Detta är den visuella indikatorn för en sammansatt entitet och bör vara under alla ord i den sammansatta entiteten från den vänstra underordnade entiteten till den högra underordnade entiteten.

1. Ange sammansatta enhetens namn i den nedrullningsbara listan.

    När du omsluter entiteterna korrekt är en grön linje under hela frasen.

1. Verifiera de sammansatta entitetsinformation på den **vilken typ av enhet vill du skapa?** popup-fönster och sedan markerar du kryssrutan **klar**.

    ![Skärmbild av entitetsinformation popup](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Sammansatta entiteten visar med båda blå markeringar för enskilda enheter och en grön linje för hela sammansatta entiteten. 

    ![Skärmbild av avsikter detaljsidan med sammansatta entitet](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Lägg till entitetens roll i uttryck

En roll är en namngiven undertyp till en entitet, som bestäms av uttryck kontext. Du kan markera en entitet i en uttryck som entitet eller välja en roll i entiteten. Alla entiteter kan ha roller, inklusive anpassade entiteter som har registrerats av datorn (enkla entiteter och sammansatta entiteter), är inte maskin inlärt (fördefinierade entiteter, reguljära uttrycks enheter, list enheter). 

Lär dig [hur du markerar en uttryck med enhets roller](tutorial-entity-roles.md) från en praktisk självstudie. 

## <a name="entity-status-predictions"></a>Entiteten status förutsägelser

När du anger en ny uttryck i LUIS-portalen kan uttryck innehåller entiteten förutsägelse fel. Felet förutsägelse är någon skillnad mellan hur en entitet är märkt jämfört med hur LUIS har förutse entiteten. 

Den här skillnaden representeras visuellt i LUIS-portalen med en röd understrykning i uttryck. Understrykningen får visas inom hakparentes entitet eller utanför hakparenteser. 

![Skärmbild av entiteten status förutsägelse avvikelse](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Välj de ord som är understruket i rött i uttryck. 

Rutan entiteten visar den **status för enheter** med ett rött utropstecken om det finns en diskrepans förutsägelse. Om du vill se status för enheter med information om skillnaden mellan taggade och förväntade entiteter **status för enheter** Välj ett objekt till höger.

![Skärm bild av val av enhets status](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

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

Du kan ta bort enhets etiketter som har registrerats av enheten från en uttryck på sidan avsikter. Om enheten inte är datorn lärt dig, kan inte tas bort från ett uttryck. Om du vill ta bort en entitet som icke-machine-lärt dig från uttryck kan behöva du ta bort enheten från hela appen. 

Om du vill ta bort en etikett för datorn lärt dig entitet från ett uttryck, väljer du entitet i uttryck. Välj sedan **ta bort etiketten** i den nedrullningsbara listrutan för entiteten som visas.

### <a name="add-a-prebuilt-entity-label"></a>Lägg till en fördefinierad enhets etikett

När du lägger till de fördefinierade entiteterna LUIS-appen, behöver du inte taggen yttranden med dessa entiteter. Läs mer om fördefinierade entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Lägg till en enhets etikett för reguljära uttryck

Om du lägger till entiteterna reguljärt uttryck LUIS-appen, behöver du inte att tagga yttranden med dessa entiteter. Läs mer om reguljärt uttryck entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från ett uttryck

Se [Lägg till mönster från befintliga uttryck på avsikten eller entitet](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Lägg till ett mönster. valfri entitet

Om du lägger till entiteterna pattern.any LUIS-appen kan inte du märka yttranden med dessa entiteter. De är endast giltiga i ett mönster. Läs mer om pattern.any entiteter och hur du lägger till dem i [Lägg till entiteter](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Träna din app när du har ändrat modell med yttranden

När du lägger till, redigera eller ta bort yttranden, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för ändringarna att påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

När du har märkt yttranden i dina avsikter kan du nu skapa en [sammansatt entitet](luis-how-to-add-entities.md).
