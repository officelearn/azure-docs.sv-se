---
title: Lägg till entiteter
titleSuffix: Language Understanding - Azure Cognitive Services
description: Lägg till entiteter (viktiga data i ditt programs domän) i appar för Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.openlocfilehash: 8a6f0a69f0159a31d9fefcc54a104ebc1ed47ec4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100352"
---
# <a name="create-entities-without-utterances"></a>Skapa entiteter utan yttranden

Entiteten representerar ett ord eller en fras i uttryck som du vill extrahera. Ett uttryck kan innehålla många entiteter eller inga alls. En entitet representerar en klass, inklusive en samling liknande objekt (platser, saker, personer, händelser eller begrepp). Entiteter beskriver information som är relevanta för avsikten och ibland de är viktiga för din app för att utföra sitt uppdrag. 

Du kan skapa entiteter före eller efter skapar avsikter.

Du kan lägga till, redigera eller ta bort entiteter i din LUIS-app via den **entitetslistan** på den **entiteter** sidan. LUIS erbjuder två typer av entiteter: [förskapade entiteter](luis-reference-prebuilt-entities.md), och dina egna [anpassade entiteter](luis-concept-entity-types.md#types-of-entities).

När en entitet har skapats kan den taggas i ett intent exempel uttryck från den **avsikt** informationssida. 

## <a name="add-prebuilt-entity"></a>Lägg till fördefinierade entitet

Vanliga förskapade entiteter som lagts till i ett program är *nummer* och *datetimeV2*. 

1. I din app från den **skapa** väljer **entiteter** på den vänstra panelen.
 
1. På den **entiteter** väljer **Lägg till fördefinierade entiteter**.

1. I **Lägg till fördefinierade entiteter** dialogrutan den **nummer** och **datetimeV2** förskapade entiteter. Välj sedan **Done** (Klar).

    ![Skärmbild av Lägg till fördefinierade entitet dialogrutan](./media/add-entities/list-of-prebuilt-entities.png)

## <a name="add-simple-entities"></a>Lägg till enkel entiteter

En enkel enhet beskriver ett enda koncept. Använd följande procedur för att skapa en entitet som extraherar avdelning företagsnamn som *personalfrågor* eller *Operations*.   

1. I din app, väljer du den **skapa** avsnittet och välj sedan **entiteter** i den vänstra rutan och markera **Skapa ny entitet**.

1. I popup-rutan, skriver `Location` i den **entitetsnamn** väljer **enkel** från den **entitetstypen** och sedan välja **klar**.

    När den här entiteten har skapats går du till alla avsikter som har exempel yttranden som innehåller entiteten. Markera texten i exempel-uttryck och markera text som entiteten. 

    En [frasen lista](luis-concept-feature.md) ofta används för att öka signalen från en enkel enhet.

## <a name="add-regular-expression-entities"></a>Lägg till entiteter för reguljärt uttryck

Ett reguljärt uttryck entiteten används för att hämta data från uttryck baserat på ett reguljärt uttryck som du anger. 

1. I din app, väljer **entiteter** vänstra navigeringsfönstret och välj sedan **Skapa ny entitet**.

1. I popup-rutan, ange `Human resources form name` i den **entitetsnamn** väljer **reguljärt uttryck** från den **entitetstypen** listan, ange det reguljära uttrycket `hrf-[0-9]{6}`, och välj sedan **klar**. 

    Den här literal tecken i reguljära uttryck `hrf-`, sedan 6 siffror som representerar ett formulär nummer för ett formulär.

## <a name="add-hierarchical-entities"></a>Lägg till hierarkiska entiteter

En hierarkisk entitet är en viss kategori av sammanhangsmässigt inlärda och begreppsmässigt relaterade entiteter. I följande exempel innehåller entiteten ursprung- och målplatserna. 

I uttryck `Move John Smith from Seattle to Cairo`, Seattle är ursprungsplatsen och Kairo är målplatsen. Varje plats är sammanhangsmässigt annan, inlärda från ordföljden och word valet i uttryck.

Om du vill lägga till hierarkiska entiteter, gör du följande: 

1. I din app, väljer **entiteter** vänstra navigeringsfönstret och välj sedan **Skapa ny entitet**.

1. I popup-rutan, skriver `Location` i den **entitetsnamn** och väljer sedan **hierarkiska** från den **entitetstypen** lista.

    ![Lägg till hierarkisk entitet](./media/add-entities/hier-location-entity-creation.png)

1. Välj **Lägg till underordnad**, och ange sedan `Origin` i **underordnade nr 1** box. 

1. Välj **Lägg till underordnad**, och ange sedan `Destination` i **underordnade nr 2** box. Välj **Done** (Klar).

    >[!CAUTION]
    >Namn på underordnade entiteter måste vara unikt inom alla entiteter i samma app. Två olika hierarkiska entiteter får inte innehålla underordnade entiteter med samma namn. 

    När den här entiteten har skapats går du till alla avsikter som har exempel yttranden som innehåller entiteten. Markera texten i exempel-uttryck och markera text som entiteten. 

## <a name="add-composite-entities"></a>Lägg till sammansatta entiteter

Du kan definiera relationer mellan entiteter av olika typer av en sammansatt entitet skapas. I följande exempel innehåller entiteten ett reguljärt uttryck och en fördefinierade entitet med namn.  

I uttryck `Send hrf-123456 to John Smith`, texten `hrf-123456` matchas mot ett HR [reguljärt uttryck](#add-regular-expression-entities) och `John Smith` extraheras med färdiga entitet personName. Varje entitet är en del av en större, överordnade entiteten. 

1. I din app, väljer **entiteter** från den vänstra navigeringen i den **skapa** avsnittet och välj sedan **Lägg till fördefinierade entitet**.

1. Lägg till fördefinierade entitet **PersonName**. Anvisningar finns i [Lägg till fördefinierade entiteter](#add-prebuilt-entity). 

1. Välj **entiteter** vänstra navigeringsfönstret och välj sedan **Skapa ny entitet**.

1. I popup-rutan, ange `SendHrForm` i den **entitetsnamn** rutan och välj sedan **sammansatta** från den **entitetstypen** lista.

1. Välj **Lägg till underordnad** att lägga till en ny underordnad.

1. I **underordnade nr 1**, väljer du entitet **nummer** i listan.

1. I **underordnade nr 2**, väljer du entitet **personalfrågor utgör namnet** i listan. 

1. Välj **Done** (Klar).

## <a name="add-patternany-entities"></a>Lägg till Pattern.any entiteter

[Pattern.ANY](luis-concept-entity-types.md) entiteter är endast giltiga i [mönster](luis-how-to-model-intent-pattern.md), inte avsikter. Den här typen av entiteten kan LUIS hitta slutet av entiteter av olika längd och word val. Eftersom den här entiteten används i ett mönster, vet LUIS där slutet av entiteten är i mallen uttryck.

Om en app har en `FindHumanResourcesForm` avsikt, extraherade formulärrubrik kan störa avsikt förutsägelser. Använda en Pattern.any inom ett mönster för att förtydliga vilka ord är i formulärets rubrik. LUIS-förutsägelse börjar med uttryck. Först uttryck kontrolleras och matchade för entiteter, när entiteterna hittas, och sedan mönstret kontrolleras och matchas. 

I uttryck `Where is Request relocation from employee new to the company on the server?`, formulärets rubrik är svårt att eftersom den inte är sammanhangsmässigt uppenbara där titeln slutar och där resten av uttryck börjar. Rubriker kan vara valfri ordning av ord, inklusive ett enstaka ord komplexa fraser med skiljetecken och meningslöst sorteringen av orden. Ett mönster kan du skapa en entitet där fullständiga och exakta entiteten kan extraheras. När rubriken har hittats, den `FindHumanResourcesForm` avsikt förväntas eftersom det är avsikten för mönstret.

1. Från den **skapa** väljer **entiteter** i den vänstra panelen och välj sedan **Skapa ny entitet**.

1. I den **Lägg till entitet** dialogrutan anger `HumanResourcesFormTitle` i den **entitetsnamn** och väljer **Pattern.any** som den **entitetstypen**.

    Om du vill använda entiteten pattern.any, lägga till ett mönster på den **mönster** sidan den **förbättra apprestanda** avsnittet med rätt klammerparentesen syntax, till exempel `Where is **{HumanResourcesFormTitle}** on the server?`.

    Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](luis-concept-patterns.md#explicit-lists). 

## <a name="add-a-role-to-pattern-based-entity"></a>Lägga till en roll i mönstret-baserade entitet

En roll är en namngiven undertyp till en entitet baserat på kontext. Det kan jämföras med en [hierarkiska](#add-hierarchical-entities) entiteten men roller som endast används i [mönster](luis-how-to-model-intent-pattern.md). 

Om du använder samma exempel som hierarkisk entiteten av ursprung och mål städer, skillnaden är att en roll har namnet ursprung i stället för en hierarkisk underordnad. 

Syntaxen för en roll är **{Entityname:Rolename}** där enhetens namn följt av ett kolon och sedan namnet på rollen. Till exempel `Move {personName} from {LocationUsingRoles:Origin} to {LocationUsingRoles:Destination}`.

1. Från den **skapa** väljer **entiteter** på den vänstra panelen.

1. Välj **Create new entity** (Skapa ny entitet). Ange namnet på `LocationUsingRoles`. Välj typ **enkel** och välj **klar**. 

1. Välj **entiteter** i vänster panel, välj sedan den nya entiteten **LocationUsingRoles** skapade i föregående steg.

1. I den **rollnamn** textrutan anger du namnet på rollen `Origin` och ange. Lägg till ett andra rollnamn för `Destination`. 

    ![Skärmbild av Administrationscenter ursprung rollen till platsen entitet](./media/add-entities/roles-enter-role-name-text.png)

## <a name="add-list-entities"></a>Lägg till listan över entiteter

Lista över entiteter representerar en fast, stängda uppsättning närstående ord. 

Du kan ha en lista över alla avdelningar tillsammans med eventuella synonymer för avdelningarna för en personalapp. Du behöver veta alla värden när du har skapat entiteten. Du kan lägga till fler när du har granskat hur användarna yttranden med synonymer.

1. Från den **skapa** väljer **entiteter** i den vänstra panelen och välj sedan **Skapa ny entitet**.

1. I den **Lägg till entitet** skriver `Department` i den **entitetsnamn** rutan och välj **lista** som den **entitetstypen**. Välj **Done** (Klar).
  
1. Sidan med listan över entitet kan du lägga till normaliserade namn. I den **värden** textrutan, ange ett avdelningsnamn lista, till exempel `HumanResources` tryck sedan på RETUR på tangentbordet. 

1. Ange synonymer, tryck på RETUR på tangentbordet efter varje objekt till höger om normaliserat värde.

1. Om du vill mer normaliserade objekt lista väljer **rekommenderar** att se alternativ från den [semantiska ordlista](luis-glossary.md#semantic-dictionary).

    ![Skärmbild av Administrationscenter ursprung rollen till platsen entitet](./media/add-entities/hr-list-2.png)


1. Markera ett objekt i listan med rekommenderade att lägga till det som ett normaliserat värde eller välja **Lägg till alla** att lägga till alla objekt. 
    Du kan importera värden till en befintlig lista entitet med följande JSON-format:

    ```
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


## <a name="change-entity-type"></a>Ändra entitetstyp

LUIS kan du inte ändra typ av entiteten eftersom den inte vet vad du ska lägga till eller ta bort för att skapa entiteten. För att ändra typen, är det bättre att skapa en ny entitet av rätt typ. med ett något annorlunda namn. När enheten har skapats i varje uttryck att ta bort gamla taggade entitetsnamnet och lägga till det nya namnet. När alla yttranden har varit relabeled, kan du ta bort den gamla entiteten. 

## <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från ett uttryck

Se [Lägg till mönster från befintliga uttryck på avsikten eller entitet](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Träna din app när du har ändrat modell med entiteter

När du lägger till, redigera eller ta bort entiteter, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för ändringarna att påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

Mer information om fördefinierade entiteter finns i den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text) projekt. 

Information om hur entiteten visas i frågesvaret JSON-slutpunkt finns [extrahering av Data](luis-concept-data-extraction.md)

Nu när du har lagt till avsikter, yttranden och entiteter, har du en grundläggande LUIS-app. Lär dig hur du [träna](luis-how-to-train.md), [testa](luis-interactive-test.md), och [publicera](luis-how-to-publish-app.md) din app.
 
