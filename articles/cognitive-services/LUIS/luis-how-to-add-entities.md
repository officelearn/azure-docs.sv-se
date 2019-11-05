---
title: Lägg till entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Skapa entiteter för att extrahera nyckel data från User yttranden i Language Understanding-appar (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 54c9d79c62052daeee76de5dffb1099dc7d75180
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467714"
---
# <a name="create-entities-without-utterances"></a>Skapa entiteter utan yttranden

Entiteten representerar ett ord eller en fras i uttryck som du vill extrahera. En entitet representerar en klass inklusive en samling liknande objekt (platser, saker, personer, händelser eller koncept). Entiteter beskriver information som är relevant för avsikten och de är ibland nödvändiga för att din app ska kunna utföra sitt uppdrag. Du kan skapa entiteter när du lägger till en uttryck i ett avsikts syfte eller förutom (före eller efter) lägger till en uttryck till ett avsikts sätt.

Du kan lägga till, redigera eller ta bort entiteter i LUIS-appen via **listan entiteter** på sidan **entiteter** . LUIS erbjuder två huvud typer av entiteter: [fördefinierade entiteter](luis-reference-prebuilt-entities.md)och dina egna [anpassade entiteter](luis-concept-entity-types.md#types-of-entities).

När en enhet som har registrerats av enheten har skapats måste du markera entiteten i alla exempel-uttryck för alla syften som den finns i.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Lägg till en fördefinierad entitet i din app

Vanliga fördefinierade entiteter som läggs till i ett program är *antal* och *datetimeV2*. 

1. I din app går du till avsnittet **version** och väljer **entiteter** i den vänstra panelen.
 
1. På sidan **entiteter** väljer du **Lägg till fördefinierade entiteter**.

1. I dialog rutan **Lägg till fördefinierade entiteter** väljer du de förbyggda entiteterna **Number** och **datetimeV2** . Välj sedan **Done** (Klar).

    ![Skärm bild av dialog rutan Lägg till fördefinierad entitet](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Lägg till enkla entiteter för enkla koncept

En enkel entitet beskriver ett enda begrepp. Använd följande procedur för att skapa en entitet som extraherar företags avdelnings namn, till exempel *mänskliga resurser* eller *åtgärder*.   

1. I din app väljer du avsnittet **build** och väljer sedan **entiteter** i den vänstra panelen. Välj sedan **Skapa ny entitet**.

1. I dialog rutan popup skriver du `Location` i rutan **entitetsnamn** , väljer **enkelt** från listan **entitetstyp** och väljer sedan **färdig**.

    När entiteten har skapats går du till alla exempel som har yttranden som innehåller entiteten. Markera texten i exemplet uttryck och markera texten som entitet. 

    En [fras lista](luis-concept-feature.md) används ofta för att öka signalen för en enkel enhet.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Lägg till entiteter med reguljära uttryck för mycket strukturerade koncept

En entitet för reguljära uttryck används för att hämta data från uttryck baserat på ett reguljärt uttryck som du anger. 

1. I din app väljer du **entiteter** i det vänstra navigerings fältet och väljer sedan **Skapa ny entitet**.

1. I dialog rutan popup skriver du `Human resources form name` i rutan **entitetsnamn** , väljer **reguljärt uttryck** i listan **entitetstyp** , anger det reguljära uttrycket `hrf-[0-9]{6}`och väljer sedan **slutfört**. 

    Det reguljära uttrycket matchar literala tecken `hrf-`och sedan 6 siffror som representerar ett formulär nummer för ett personal formulär.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Lägg till sammansatta entiteter i en grupp i en över-underordnad relation

Du kan definiera relationer mellan entiteter av olika typer genom att skapa en sammansatt entitet. I följande exempel innehåller entiteten ett reguljärt uttryck och en fördefinierad entitet med namn.  

I uttryck `Send hrf-123456 to John Smith`matchas text `hrf-123456` till ett [reguljärt uttryck](#add-regular-expression-entities) för personal och `John Smith` extraheras med den fördefinierade entiteten personName. Varje entitet är en del av en större, överordnad entitet. 

1. I din app väljer du **entiteter** i den vänstra navigeringen i avsnittet **skapa** och väljer sedan **Lägg till fördefinierad entitet**.

1. Lägg till den fördefinierade entiteten **PersonName**. Instruktioner finns i [lägga till fördefinierade entiteter](#add-prebuilt-entity). 

1. Välj **entiteter** i det vänstra navigerings fältet och välj sedan **Skapa ny entitet**.

1. I dialog rutan popup skriver du `SendHrForm` i rutan **entitetsnamn** och väljer sedan **sammansatt** i listan **entitetstyp** .

1. Välj **Lägg till underordnad** för att lägga till en ny underordnad.

1. I **Underordnat #1**väljer du enhets **numret** i listan.

1. I **Underordnat #2**väljer du **formulär namnet personal** i listan. 

1. Välj **Done** (Klar).

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Lägg till mönster. alla entiteter för att avbilda fria formulär enheter

[Pattern. alla](luis-concept-entity-types.md) entiteter är bara giltiga i [mönster](luis-how-to-model-intent-pattern.md), inte avsikter. Den här typen av entitet hjälper LUIS att hitta slutet av entiteter med varierande längd och val av ord. Eftersom entiteten används i ett mönster vet LUIS var slutet på entiteten finns i uttryck-mallen.

Om en app har ett `FindHumanResourcesForm` avsikt kan den extraherade formulär titeln störa avsikten med avsikten. För att klargör vilka ord som är i formulär rubriken, använder du ett mönster. alla inom ett mönster. Den LUIS förutsägelsen börjar med uttryck. Först är uttryck markerad och matchad för entiteter, när entiteterna hittas, kontrol leras och matchas mönstret. 

I uttryck-`Where is Request relocation from employee new to the company on the server?`är formulär titeln svårt eftersom den inte är Sammanhangs känslig där rubriken slutar och var resten av uttryck börjar. Titlar kan vara vilken ordning som helst av orden, t. ex. ett enda ord, komplexa fraser med skiljetecken och nonsensical sortering av ord. Med ett mönster kan du skapa en entitet där den fullständiga och exakta entiteten kan extraheras. När rubriken har hittats förväntas `FindHumanResourcesForm` avsikten, eftersom det är syftet med mönstret.

1. I avsnittet **skapa** väljer du **entiteter** i den vänstra panelen och väljer sedan **Skapa ny entitet**.

1. I dialog rutan **Lägg till entitet** anger du `HumanResourcesFormTitle` i rutan **entitetsnamn** och väljer **mönster. alla** **typer av enheter**.

    Om du vill använda mönstret. valfri entitet, Lägg till ett mönster på sidan **mönster** i avsnittet **förbättra appens prestanda** med rätt klammerparentes, till exempel `Where is **{HumanResourcesFormTitle}** on the server?`.

    Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](luis-concept-patterns.md#explicit-lists). 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Lägg till en roll för att skilja på olika kontexter

En roll är en namngiven under typ baserat på kontext. Den är tillgänglig i alla entiteter, inklusive förbyggda och icke-inlärda entiteter. 

Syntaxen för en roll är **`{Entityname:Rolename}`** där entitetsnamnet följs av ett kolon, sedan roll namnet. Till exempel `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. I avsnittet **build** väljer du **entiteter** i den vänstra panelen.

1. Välj **Create new entity** (Skapa ny entitet). Ange namnet på `Location`. Välj **enkel** typ och välj **färdig**. 

1. Välj **entiteter** i den vänstra panelen och välj sedan den nya entitets **plats** som skapades i föregående steg.

1. I text rutan **rollnamn** anger du namnet på rollen `Origin` och retur. Lägg till ett andra roll namn för `Destination`. 

    ![Skärm bild som visar hur du lägger till ursprungs rollen till plats entiteten](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Lägg till List enheter för exakta matchningar

List entiteter representerar en fast, avslutad uppsättning relaterade ord. 

För en personal-app kan du ha en lista över alla avdelningar tillsammans med alla synonymer för avdelningarna. Du behöver inte känna till alla värden när du skapar entiteten. Du kan lägga till fler när du har granskat yttranden av verkliga användare med synonymer.

1. I avsnittet **skapa** väljer du **entiteter** i den vänstra panelen och väljer sedan **Skapa ny entitet**.

1. I dialog rutan **Lägg till entitet** skriver du `Department` i **namn rutan entitet** och väljer **lista** som **entitetstyp**. Välj **Done** (Klar).
  
1. På sidan lista entitet kan du lägga till normaliserade namn. I text rutan **värden** anger du ett avdelnings namn för listan, till exempel `HumanResources` och trycker på RETUR på tangent bordet. 

1. Till höger om det normaliserade värdet anger du synonymer, trycker på RETUR på tangent bordet efter varje objekt.

1. Om du vill ha fler normaliserade objekt i listan väljer du **rekommendera** för att se alternativ från den [semantiska ord listan](luis-glossary.md#semantic-dictionary).

    ![Skärm bild av val av rekommendations funktion för att se alternativ](./media/add-entities/hr-list-2.png)


1. Markera ett objekt i den rekommenderade listan för att lägga till det som ett normaliserat värde eller Välj **Lägg till alla** för att lägga till alla objekt. 
    Du kan importera värden till en befintlig List-entitet med följande JSON-format:

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Ändra inte entitetstyp

Med LUIS kan du inte ändra entitetens typ eftersom den inte vet vad som ska läggas till eller tas bort för att konstruera den entiteten. För att kunna ändra typen är det bättre att skapa en ny entitet av rätt typ med ett något annat namn. När entiteten har skapats tar du bort det gamla etikett namnet och lägger till det nya entitetsnamnet i varje uttryck. Ta bort den gamla entiteten när alla yttranden har bytt namn. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Skapa ett mönster från ett exempel uttryck

Se [Lägg till mönster från befintlig uttryck på sidan för avsikt eller entitet](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Träna din app när du har ändrat modellen med entiteter

När du har lagt till, redigerat eller tagit bort entiteter kan du [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för dina ändringar för att påverka slut punkts frågorna. 

## <a name="next-steps"></a>Nästa steg

Mer information om färdiga entiteter finns i avsnittet om [igenkännings text](https://github.com/Microsoft/Recognizers-Text) . 

Information om hur entiteten visas i svars frågan för JSON-slutpunkten finns i [data extrahering](luis-concept-data-extraction.md)

Nu när du har lagt till avsikter, yttranden och entiteter har du en grundläggande LUIS-app. Lär dig att [träna](luis-how-to-train.md), [testa](luis-interactive-test.md)och [publicera](luis-how-to-publish-app.md) din app.
 
