---
title: Lägg till exempel yttranden-LUIS
titleSuffix: Azure Cognitive Services
description: Exempel på yttranden är text exempel på användar frågor eller kommandon. Om du vill lära dig Language Understanding (LUIS) måste du lägga till exempel yttranden i ett avsikts syfte.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: ed6321963422cf17c858f43593092f8ffa4a1119
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467743"
---
# <a name="add-an-entity-to-example-utterances"></a>Lägg till en entitet till exempel yttranden 

Exempel på yttranden är text exempel på användar frågor eller kommandon. Om du vill lära dig Language Understanding (LUIS) måste du lägga till [exempel yttranden](luis-concept-utterance.md) i ett [avsikts syfte](luis-concept-intent.md).


Normalt lägger du till ett exempel uttryck i ett avsikts syfte och sedan skapar du entiteter och etiketten yttranden på sidan **avsikter** . Om du hellre vill skapa entiteter först, se [Lägg till entiteter](luis-how-to-add-entities.md).

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="marking-entities-in-example-utterances"></a>Markera entiteter i exempel yttranden

När du väljer text i exemplet uttryck om du vill markera en entitet visas en snabb meny på plats. Med den här menyn kan du antingen skapa eller välja en entitet. 

Vissa entitetstyper, till exempel förbyggda entiteter och entiteter med reguljära uttryck, kan inte taggas i exemplet uttryck eftersom de taggats automatiskt. 

## <a name="add-a-simple-entity"></a>Lägg till en enkel entitet

I följande procedur skapar du och taggar en anpassad entitet i följande uttryck på sidan **avsikter** :

```text
Are there any SQL server jobs?
```

1. Välj `SQL server` i uttryck för att märka den som en enkel entitet. I list rutan entitet som visas kan du antingen välja en befintlig entitet eller lägga till en ny entitet. Om du vill lägga till en ny entitet skriver du namnet `Job` i text rutan och väljer sedan **Skapa ny entitet**.

    ![Skärm bild av att ange enhetens namn](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > När du väljer ord att tagga som entiteter:
    > * För ett enstaka ord markerar du det bara. 
    > * För en uppsättning med två eller flera ord väljer du det första ordet och sedan det sista ordet.

1. I popup-rutan **vilken typ av entitet vill du skapa?** kontrollerar du enhetens namn och väljer den **enkla** entitetstypen och väljer sedan **Slutför**.

    En [fras lista](luis-concept-feature.md) används ofta för att öka signalen för en enkel enhet.

## <a name="add-a-list-entity"></a>Lägg till en lista entitet

Lista entiteter representerar en uppsättning exakta text matchningar av relaterade ord i systemet. 

Du kan använda normaliserade värden för ett företags avdelnings lista: `Accounting` och `Human Resources`. Varje normaliserat namn har synonymer. För en avdelning kan dessa synonymer omfatta alla avdelnings akronymer, siffror eller slang. Du behöver inte känna till alla värden när du skapar entiteten. Du kan lägga till fler när du har granskat yttranden av verkliga användare med synonymer.

1. I ett exempel på uttryck på sidan **avsikter** väljer du ordet eller frasen som du vill ha i den nya listan. När List rutan entitet visas anger du namnet på den nya List-entiteten i den översta text rutan och väljer sedan **Skapa ny entitet**.   

1. I popup-rutan **vilken typ av entitet vill du skapa?** namnger du entiteten och väljer **lista** som typ. Lägg till synonymer för det här listobjektet och välj sedan **Slutför**. 

    ![Skärm bild av att ange en lista med enhets synonymer](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Du kan lägga till fler List objekt eller flera objekt synonymer genom att märka andra yttranden, eller genom att redigera entiteten från **entiteterna** i det vänstra navigerings fältet. Genom att [Redigera](luis-how-to-add-entities.md#add-list-entities) entiteter får du alternativ för att ange ytterligare objekt med motsvarande synonymer eller importera en lista. 

## <a name="add-a-composite-entity"></a>Lägg till en sammansatt entitet

Sammansatta entiteter skapas från befintliga **entiteter** för att bilda en överordnad entitet. 

Under förutsättning att uttryck `Does John Smith work in Seattle?`, kan en sammansatt uttryck returnera enhets information för medarbetarnas namn `John Smith`och platsen `Seattle` i en sammansatt entitet. De underordnade entiteterna måste redan finnas i appen och markeras i exemplet uttryck innan den sammansatta entiteten skapas.

1. Om du vill omsluta underordnade entiteter till en sammansatt entitet väljer du den **första** märkta entiteten (längst till vänster) i uttryck för den sammansatta entiteten. En nedrullningsbar listruta visas för att Visa valen för det här valet.

1. Välj **Radbryt i sammansatt entitet i** den nedrullningsbara listan. 

1. Välj det sista ordet i den sammansatta entiteten (längst till höger). Observera att en grön linje följer den sammansatta entiteten. Detta är den visuella indikatorn för en sammansatt entitet och bör vara under alla ord i den sammansatta entiteten från den vänstra underordnade entiteten till den högra underordnade entiteten.

1. Ange namnet på den sammansatta entiteten i den nedrullningsbara listan.

    När du omsluter entiteterna på rätt sätt är en grön linje under hela frasen.

1. Verifiera informationen om sammansatta entiteter i popup-rutan **vilken typ av entitet vill du skapa?** och välj sedan **klar**.

    ![Skärm bild av popup-fönster med information om entitet](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Den sammansatta entiteten visas med både blå högdagrar för enskilda entiteter och en grön understrykning för hela den sammansatta entiteten. 

    ![Skärm bild av informations sidan om avsikter med sammansatt entitet](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Lägg till entitetens roll i uttryck

En roll är en namngiven undertyp till en entitet, som bestäms av uttryck kontext. Du kan markera en entitet i en uttryck som entitet eller välja en roll i entiteten. Alla entiteter kan ha roller, inklusive anpassade entiteter som har registrerats av datorn (enkla entiteter och sammansatta entiteter), är inte maskin inlärt (fördefinierade entiteter, reguljära uttrycks enheter, list enheter). 

Lär dig [hur du markerar en uttryck med enhets roller](tutorial-entity-roles.md) från en praktisk självstudie. 

## <a name="entity-status-predictions"></a>Enhets status förutsägelser

När du anger en ny uttryck i LUIS-portalen kan uttryck ha enhets förutsägelse fel. Förutsägelse felet är en skillnad mellan hur en entitet märks jämfört med hur LUIS har förväntat entiteten. 

Den här skillnaden representeras visuellt i LUIS-portalen med en röd understrykning i uttryck. Den röda understrykningen kan visas i hakparenteser eller utanför hakparenteser. 

![Skärm bild av avvikelse i enhets status förutsägelse](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Markera de ord som är understrukna i rött i uttryck. 

I rutan entitet visas **enhets status** med ett rött utrops tecken om det förekommer en förutsägelse avvikelse. Om du vill se enhetens status med information om skillnaden mellan etiketterade och förväntade entiteter väljer du **enhets status** och väljer sedan objektet till höger.

![Skärm bild av val av enhets status](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

Den röda linjen kan visas vid följande tidpunkter:

   * När en uttryck anges men innan entiteten är märkt
   * När enhets etiketten används
   * När enhets etiketten tas bort
   * När mer än en enhets etikett förutsägs för texten 

Följande lösningar hjälper till att lösa enhetens förutsägelse avvikelse:

|Entitet|Visuell indikator|Förutsägelse|Lösning|
|--|--|--|--|
|Uttryck har angetts, entiteten har inte märkts ännu.|röd understrykning|Förutsägelse är korrekt.|Namnge entiteten med det förväntade värdet.|
|Omärkt text|röd understrykning|Felaktig förutsägelse|Den aktuella yttranden som använder den här Felaktiga entiteten måste granskas för alla avsikter. Den aktuella yttranden har LUIS att den här texten är den förväntade entiteten.
|Korrekt märkt text|blå enhets markering, röd understrykning|Felaktig förutsägelse|Ge fler yttranden med den korrekt märkta entiteten på en rad olika platser och användningar. De aktuella yttranden är inte tillräckliga för att lära sig LUIS att det här är entiteten eller liknande entiteter visas i samma kontext. Liknande entitet ska kombineras i en enda entitet, så LUIS är inte förvirrande. En annan lösning är att lägga till en fras lista för att öka betydelsen av orden. |
|Felaktigt märkt text|blå enhets markering, röd understrykning|Korrekt förutsägelse| Ge fler yttranden med den korrekt märkta entiteten på en rad olika platser och användningar. 

## <a name="other-actions"></a>Andra åtgärder

Du kan utföra åtgärder på exempel yttranden som en vald grupp eller som ett enskilt objekt. Grupper med valt exempel yttranden ändra snabb menyn ovanför listan. Enstaka objekt kan använda både snabb menyn ovanför listan och den enskilda sammanhangsbaserade ellipsen i slutet av varje uttryck-rad. 

### <a name="remove-entity-labels-from-utterances"></a>Ta bort enhets etiketter från yttranden

Du kan ta bort enhets etiketter som har registrerats av enheten från en uttryck på sidan avsikter. Om enheten inte är känd på datorn kan den inte tas bort från en uttryck. Om du behöver ta bort en icke-uttryck entitet från måste du ta bort entiteten från hela appen. 

Om du vill ta bort en enhets etikett som har registrerats av enheten från en uttryck väljer du entiteten i uttryck. Välj sedan **ta bort etikett** i list rutan entitet som visas.

### <a name="add-a-prebuilt-entity-label"></a>Lägg till en fördefinierad enhets etikett

När du lägger till fördefinierade entiteter i LUIS-appen behöver du inte tagga yttranden med dessa entiteter. Mer information om färdiga entiteter och hur du lägger till dem finns i [lägga till entiteter](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Lägg till en enhets etikett för reguljära uttryck

Om du lägger till entiteter med reguljära uttryck i LUIS-appen behöver du inte tagga yttranden med dessa entiteter. Mer information om reguljära uttrycks enheter och hur du lägger till dem finns i [lägga till entiteter](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från en uttryck

Se [Lägg till mönster från befintlig uttryck på sidan för avsikt eller entitet](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Lägg till ett mönster. valfri entitet

Om du lägger till mönstret. alla entiteter i LUIS-appen kan du inte märka yttranden med dessa entiteter. De är bara giltiga i mönster. Mer information om mönster. alla entiteter och hur du lägger till dem finns i [lägga till entiteter](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Träna din app när du har ändrat modell med yttranden

När du har lagt till, redigerat eller tagit bort yttranden, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för dina ändringar för att påverka slut punkts frågorna. 

## <a name="next-steps"></a>Nästa steg

När du har märkt yttranden i dina **avsikter**kan du nu skapa en [sammansatt entitet](luis-how-to-add-entities.md).
