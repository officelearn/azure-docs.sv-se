---
title: Lägg till entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Skapa entiteter för att extrahera nyckel data från User yttranden i Language Understanding-appar (LUIS). Extraherade enhets data används av klient programmet för att fullfil kund förfrågningar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383689"
---
# <a name="add-entities-to-extract-data"></a>Lägga till entiteter för att extrahera data 

Skapa entiteter för att extrahera nyckel data från User yttranden i Language Understanding-appar (LUIS). Extraherade enhets data används av klient programmet för att fullfil kund förfrågningar.

Entiteten representerar ett ord eller en fras i uttryck som du vill extrahera. Entiteter beskriver information som är relevanta för avsikten och ibland de är viktiga för din app för att utföra sitt uppdrag. Du kan skapa entiteter när du lägger till ett exempel-uttryck till ett avsikts sätt eller förutom (före eller efter) lägger till ett exempel uttryck till ett avsikts sätt.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Planera entiteter, sedan skapa och etiketter

Enhets inlärda entiteter kan skapas från exemplet yttranden eller skapas från sidan **entiteter** . 

I allmänhet är det en bra idé att ägna tid åt att planera entiteter innan du skapar en enhet som har lärts i portalen. Skapa sedan den enhets identifierade entiteten från exemplet uttryck med så mycket information som möjligt i del komponenterna och beskrivarna och begränsningarna som du vet vid tidpunkten. [Självstudien om desammansättnings bara entiteter](tutorial-machine-learned-entity.md) visar hur du använder den här metoden. 

Som en del av planeringen av entiteterna kanske du behöver text matchnings bara entiteter (till exempel färdiga entiteter, reguljära uttrycks enheter eller List enheter). Du kan skapa dessa från sidan **entiteter** innan de märks i exemplet yttranden. 

När du markerar etiketter kan du antingen etikettera enskilda entiteter och sedan bygga upp till en överordnad enhet som har lärts upp. Eller så kan du starta med en överordnad enhet som har lärts in och dela upp underordnade entiteter. 

> [!TIP] 
>Märk alla ord som kan indikera en entitet, även om orden inte används när de extraheras i klient programmet. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Skapa en entitet före eller med etiketter

Använd följande tabell för att lära dig vilka entiteter som du kan använda för att skapa eller lägga till varje entitet i appen. 

|Entitetstyp|Var du ska skapa entiteten i LUIS-portalen|
|--|--|
|Maskininlärd enhet|Information om entiteter eller avsikter|
|Lista entitet|Information om entiteter eller avsikter|
|Entitet för reguljära uttryck|Entiteter|
|Entiteten Pattern.any|Entiteter|
|Fördefinierade entitet|Entiteter|
|Fördefinierad domän entitet|Entiteter|

Du kan skapa alla entiteter från sidan **entiteter** , eller så kan du skapa ett par entiteter som en del av etiketten på entiteten i exemplet uttryck på sidan **information om avsikt** . Du kan bara _namnge_ en entitet i ett exempel på en uttryck på sidan för **avsikts information** . 

## <a name="create-a-machine-learned-entity"></a>Skapa en enhet som har lärts in

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Skapa en text matchnings enhet

Använda text matchnings entiteter ger flera sätt att extrahera data:

|Text matchnings enheter|Syfte|
|--|--|
|[Lista entitet](#add-list-entities-for-exact-matches)|lista över kanoniska namn tillsammans med synonymer som alternativa formulär|
|Entitet för reguljära uttryck|matcha text med en entitet för reguljära uttryck|
|[Fördefinierad entitet](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|matcha vanliga data typer, till exempel nummer, e-post, datum|
|Fördefinierad domän entitet|matcha med hjälp av valda ämnes domäner|
|[Mönster. alla](#add-a-patternany-entity)| matcha entiteter som lätt kan förväxlas med den omgivande texten|  

Färdiga entiteter fungerar utan att tillhandahålla några anpassade tränings data. De andra entiteterna behöver du ange antingen kund utbildnings data (t. ex. lista entitetens objekt) eller ett uttryck (till exempel ett reguljärt uttryck eller mönster. valfritt).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Så här skapar du en ny anpassad entitet

1. Gå till LUIS-portalen och gå till avsnittet **Hantera** och sedan sidan **entiteter** . 
1. Välj **+ skapa**och välj sedan enhets typ. 
1. Fortsätt att konfigurera entiteten och välj sedan **skapa** när du är färdig. 

### <a name="add-list-entities-for-exact-matches"></a>Lägg till List enheter för exakta matchningar

Lista över entiteter representerar en fast, stängda uppsättning närstående ord. Medan du, som författare, kan ändra listan, kommer LUIS inte att utöka eller krympa listan. Du kan också importera till en befintlig List-entitet med hjälp av en [List Entity. JSON-format (referens-Entity-List. MD # exempel-JSON-to-import-List-Entity). 

I följande lista visas kanoniskt namn och synonymer. 

|Objekt namn för färg lista|Färg synonymer|
|--|--|
|Röd|Crimson, blod, äpple, eld-motor|
|Blå|Himmelsblå, Azure, kobolt|
|Grön|Kelly, kalk|

Använd proceduren för att skapa en lista med entiteter. När entiteten lista har skapats behöver du inte märka exempel yttranden i ett avsikts syfte. List objekt och synonymer matchas med hjälp av exakt text. 

1. I avsnittet **skapa** väljer du **entiteter** i den vänstra panelen och väljer sedan **+ skapa**.

1. I dialog rutan **skapa en entitetstyp** anger du namnet på entiteten, till exempel `Colors` och välj **lista**.
1. I dialog rutan **skapa en lista entitet** i rutan **Lägg till ny under lista...** , anger du List objekt namnet, till exempel `Green`och lägger till synonymer.

    > [!div class="mx-imgBorder"]
    > ![skapa en lista med färger som en lista entitet på sidan entitets information.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. När du är färdig med att lägga till List objekt och synonymer väljer du **skapa**.

    Kom ihåg att **träna** appen när du är färdig med en grupp ändringar i appen. Träna inte appen efter en enda ändring. 

    > [!NOTE]
    > Den här proceduren visar hur du skapar och etiketterar en List-entitet från ett exempel på en uttryck på sidan **information om avsikt** . Du kan också skapa samma entitet från sidan **entiteter** .

## <a name="add-a-role-for-an-entity"></a>Lägg till en roll för en entitet

En roll är en namngiven undertyp till en entitet, baserat på kontext. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Lägg till en roll för att skilja på olika kontexter

I följande uttryck finns det två platser, och varje anges semantiskt av orden runt den, till exempel `to` och `from`: 

`Pick up the package from Seattle and deliver to New York City.`

I den här proceduren lägger du till `origin` och `destination` roller i en fördefinierad geographyV2-entitet.

1. I avsnittet **build** väljer du **entiteter** i den vänstra panelen.

1. Välj **+ Lägg till en fördefinierad entitet**. Välj **geographyV2** och välj sedan **färdig**. Detta lägger till en fördefinierad entitet i appen.
    
    Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](reference-pattern-syntax.md#explicit-lists). 

1. Välj den nyligen tillagda förskapade geographyV2-entiteten från listan **entiteter i entiteter** . 
1. Om du vill lägga till en ny roll väljer du **+** bredvid **inga roller tillagda**.
1. I text rutan **Skriv roll...** anger du namnet på rollen `Origin` och anger sedan. Lägg till ett andra roll namn för `Destination` ange sedan. 

    > [!div class="mx-imgBorder"]
    > ![skärm bild som visar hur du lägger till ursprungs rollen till plats entiteten](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Rollen läggs till i den fördefinierade entiteten men läggs inte till i någon yttranden som använder den entiteten. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Etikettext med en roll i ett exempel uttryck

1. Gå till sidan information om avsikt, som har exempel yttranden som använder rollen. 
1. Om du vill namnge med rollen väljer du enhets etiketten (heldragen linje under text) i exemplet uttryck och väljer sedan **Visa i entitetens palett** i list rutan. 

    > [!div class="mx-imgBorder"]
    > ![skärm bild som visar hur du väljer Visa i entiteten entitet](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Paletten entitet öppnas till höger. 

1. Välj entiteten och gå sedan till slutet av paletten och välj rollen. 

    > [!div class="mx-imgBorder"]
    > ![skärm bild som visar hur du väljer Visa i entiteten entitet](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Lägg till ett mönster. valfri entitet

[Pattern. alla](luis-concept-entity-types.md) entiteter är bara giltiga i [mönster](luis-how-to-model-intent-pattern.md), inte exempel yttranden. Den här typen av entiteten kan LUIS hitta slutet av entiteter av olika längd och word val. Eftersom den här entiteten används i ett mönster, vet LUIS där slutet av entiteten är i mallen uttryck.

### <a name="steps-to-create-a-patternany-entity"></a>Steg för att skapa ett mönster. valfri entitet

1. I avsnittet **skapa** väljer du **entiteter** i den vänstra panelen och väljer sedan **+ skapa**.

1. I dialog rutan **Välj typ av enhet** anger du entitetsnamnet i rutan **namn** och väljer **mönster. vilken** **typ** som helst väljer du **skapa**.

    När du [skapar en Pattern-uttryck](luis-how-to-model-intent-pattern.md) med hjälp av den här entiteten extraheras entiteten med en kombinerad algoritm för maskin inlärt och text matchning. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Skapa en mönster mal len uttryck att använda mönstret. valfri entitet

Om du vill använda mönstret. valfri entitet, Lägg till ett mönster på sidan **mönster** i avsnittet **förbättra appens prestanda** med rätt klammerparentes, till exempel `Where is **{HumanResourcesFormTitle}** on the server?`.

Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](reference-pattern-syntax.md#explicit-lists). 

## <a name="do-not-change-entity-type"></a>Ändra inte entitetstyp

LUIS kan du inte ändra typ av entiteten eftersom den inte vet vad du ska lägga till eller ta bort för att skapa entiteten. För att ändra typen, är det bättre att skapa en ny entitet av rätt typ. med ett något annorlunda namn. När enheten har skapats i varje uttryck att ta bort gamla taggade entitetsnamnet och lägga till det nya namnet. När alla yttranden har varit relabeled, kan du ta bort den gamla entiteten. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Använda färdiga modeller](howto-add-prebuilt-models.md) 

Läs mer om:
* Hur man [tränar](luis-how-to-train.md)
* [Testa](luis-interactive-test.md)
* [Publicera](luis-how-to-publish-app.md)
* Skuggning
    * [Koncept](luis-concept-patterns.md)
    * [Uttryck](reference-pattern-syntax.md)
* [Fördefinierade entiteter GitHub-lagringsplats](https://github.com/Microsoft/Recognizers-Text)
* [Koncept för data extrahering](luis-concept-data-extraction.md)


 
