---
title: Lägg till entiteter - LUIS
titleSuffix: Azure Cognitive Services
description: Skapa entiteter för att extrahera viktiga data från användaryttranden i LUIS-appar (Language Understanding). Extraherade entitetsdata används av klientprogrammet till fullfilkundförfrågningar.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220912"
---
# <a name="add-entities-to-extract-data"></a>Lägga till entiteter för att extrahera data 

Skapa entiteter för att extrahera viktiga data från användaryttranden i LUIS-appar (Language Understanding). Extraherade entitetsdata används av klientprogrammet för att fullständigtfilera kundförfrågningar.

Entiteten representerar ett ord eller en fras i det uttryck som du vill extrahera. Entiteter beskriver information som är relevant för avsikten, och ibland är de viktiga för att appen ska kunna utföra sin uppgift. Du kan skapa entiteter när du lägger till ett exempelutseende i en avsikt eller bortsett från (före eller efter) lägga till ett exempel yttrande till en avsikt.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Planera entiteter och skapa och sedan

Maskininlärda entiteter kan skapas från exempelyttrandena eller skapas från sidan **Entiteter.** 

I allmänhet är en metod för bästa praxis att spendera tid på att planera entiteterna innan du skapar en datorinlärd entitet i portalen. Skapa sedan den maskininlärda entiteten från exempeluttrycket med så mycket information i underkomponenterna och beskrivningarna och begränsningarna som du vet vid den tidpunkten. [Självstudiekursen för den nedjänvärda entiteten](tutorial-machine-learned-entity.md) visar hur du använder den här metoden. 

Som en del av planeringen av entiteterna kanske du vet att du behöver textmatchningsentiteter (till exempel fördefinierade entiteter, reguljära uttrycksentiteter eller listentiteter). Du kan skapa dessa från sidan **Entiteter** innan de är märkta i exempelyttranden. 

Vid märkning kan du antingen märka enskilda entiteter och sedan bygga upp till en överordnad datorinlärd entitet. Du kan också börja med en överordnad datorinlärd entitet och delas upp i underordnade entiteter. 

> [!TIP] 
>Märk alla ord som kan indikera en entitet, även om orden inte används när de extraheras i klientprogrammet. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Skapa en entitet före eller med märkning

Använd följande tabell för att förstå vilka entiteter där du kan skapa eller lägga till varje entitet i appen. 

|Entitetstyp|Var du ska skapa entitet i LUIS-portalen|
|--|--|
|Maskininlärd enhet|Entiteter eller avsiktsinformation|
|Lista entitet|Entiteter eller avsiktsinformation|
|Entitet för reguljära uttryck|Entiteter|
|Entiteten Pattern.any|Entiteter|
|Fördefinierad enhet|Entiteter|
|Fördefinierad domänentitet|Entiteter|

Du kan skapa alla entiteter från sidan **Entiteter,** eller så kan du skapa ett par av entiteterna som en del av att märka entiteten i exempelyttrandet på sidan **Avsiktsinformation.** Du kan bara _märka_ en entitet i ett exempelutseende från sidan **Avsiktsinformation.** 

## <a name="create-a-machine-learned-entity"></a>Skapa en maskininlärd entitet

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Skapa en textmatchningsenhet

Använd textmatchningsentiteter som innehåller flera sätt att extrahera data:

|Textmatchande entiteter|Syfte|
|--|--|
|[Lista entitet](#add-list-entities-for-exact-matches)|lista över kanoniska namn tillsammans med synonymer som alternativa former|
|Entitet för reguljära uttryck|matcha text med hjälp av en enhet för reguljärt uttryck|
|[Fördefinierad enhet](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|matcha vanliga datatyper som nummer, e-post, datum|
|Fördefinierad domänentitet|matcha med valda ämnesdomäner|
|[Pattern.any](#add-a-patternany-entity)| för att matcha entiteter som lätt kan förväxlas med den omgivande texten|  

Fördefinierade entiteter fungerar utan att tillhandahålla anpassade träningsdata. De andra entiteterna måste du ange antingen kundutbildningsdata (till exempel Listentitetens objekt) eller ett uttryck (till exempel ett reguljärt uttryck eller ett mönster.any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Så här skapar du en ny anpassad entitet

1. Gå till avsnittet **Hantera** i LUIS-portalen och sedan sidan **Entiteter.** 
1. Välj **+ Skapa**och välj sedan entitetstypen. 
1. Fortsätt konfigurera entiteten och välj sedan **Skapa** när du är klar. 

### <a name="add-list-entities-for-exact-matches"></a>Lägg till listentiteter för exakta matchningar

Listentiteter representerar en fast, sluten uppsättning relaterade ord. Medan du som författare kan ändra listan växer eller förminsar LUIS inte listan. Du kan också importera till en befintlig listentitet med hjälp av en [listentitet .json-format(reference-entity-list.md#example-json-to-import-into-list-entity). 

Följande lista visar det kanoniska namnet och synonymerna. 

|Färg - listobjektnamn|Färg - synonymer|
|--|--|
|Röd|crimson, blod, äpple, brandmotor|
|Blå|himmel, azurblå, kobolt|
|Grön|kelly, lime|

Använd proceduren för att skapa en listentitet. När listentiteten har skapats behöver du inte märka exempelyttranden i en avsikt. Listobjekt och synonymer matchas med exakt text. 

1. Välj **Entiteter** på den vänstra panelen i avsnittet **Skapa** och välj sedan **+ Skapa**.

1. I dialogrutan **Skapa en entitetstyp** anger du `Colors` namnet på entiteten, till exempel och väljer **Lista**.
1. I dialogrutan **Skapa en lista** anger du listobjektnamnet `Green`i dialogrutan Lägg till en ny **underlista....** och lägger sedan till synonymer.

    > [!div class="mx-imgBorder"]
    > ![Skapa en lista med färger som en listentitet på detaljsidan för entitet.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. När du har lagt till listobjekt och synonymer väljer du **Skapa**.

    När du är klar med en grupp ändringar i appen, kom ihåg att **Träna** appen. Träna inte appen efter en enda ändring. 

    > [!NOTE]
    > Den här proceduren visar hur du skapar och märker en listentitet från ett exempelyttrande på sidan **Avsiktsinformation.** Du kan också skapa samma entitet från sidan **Entiteter.**

## <a name="add-a-role-for-an-entity"></a>Lägga till en roll för en entitet

En roll är en namngiven undertyp till en entitet, baserat på kontext. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Lägga till en roll för att skilja mellan olika sammanhang

I följande uttryck finns det två platser, och var och en anges semantically av orden runt den, till exempel `to` och `from`: 

`Pick up the package from Seattle and deliver to New York City.`

I den här `origin` `destination` proceduren lägger du till och roller i en fördefinierad geografiV2-entitet.

1. Välj **Entiteter** på den vänstra panelen i avsnittet **Skapa.**

1. Välj **+ Lägg till fördefinierad entitet**. Välj **geografiV2** och välj sedan **Klar**. Detta lägger till en fördefinierad entitet i appen.
    
    Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](reference-pattern-syntax.md#explicit-lists). 

1. Välj den nyligen tillagda fördefinierade geographyV2-entiteten i listan **Entiteter** med entiteter. 
1. Om du vill lägga **+** till en ny roll väljer du bredvid **Inga roller tillagda**.
1. I **textrutan Typ...** anger du namnet `Origin` på rollen och anger sedan. Lägg till ett `Destination` andra rollnamn för ange sedan. 

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av hur origin-rollen ska lägga till i entiteten Plats](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Rollen läggs till i den fördefinierade entiteten men läggs inte till i några yttranden med den entiteten. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Etiketttext med en roll i ett exempelutseende

1. Gå till sidan Avsiktsinformation, som har exempelyttranden som använder rollen. 
1. Om du vill märka med rollen markerar du entitetsetiketten (heldragen linje under text) i exempelutsiktigheten och väljer sedan **Visa i entitetspaletten i** listrutan. 

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av att välja Visa i entitetspalett](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Entitetspaletten öppnas till höger. 

1. Välj entiteten och gå sedan till palettens underkant och välj rollen. 

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av att välja Visa i entitetspalett](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Lägga till ett mönster.alla entiteter

[Pattern.any](luis-concept-entity-types.md) entiteter är endast giltiga i [mönster](luis-how-to-model-intent-pattern.md), inte avsikters exempelyttranden. Den här typen av entitet hjälper LUIS att hitta slutet av entiteter med varierande längd och ordval. Eftersom den här entiteten används i ett mönster vet LUIS var slutet av entiteten finns i uttrycksmallen.

### <a name="steps-to-create-a-patternany-entity"></a>Steg för att skapa ett mönster.alla entiteter

1. Välj **Entiteter** på den vänstra panelen i avsnittet **Skapa** och välj sedan **+ Skapa**.

1. I dialogrutan **Välj en entitetstyp** anger du entitetsnamnet i rutan **Namn** och väljer **Pattern.Any** som **typ** och väljer sedan **Skapa**.

    När du [har skapat ett mönsterutseende](luis-how-to-model-intent-pattern.md) med den här entiteten extraheras entiteten med en kombinerad maskininlärd och textmatchningsalgoritm. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Skapa ett mönstermallutseende om du vill använda pattern.any entitet

Om du vill använda mönstren.någon entitet lägger du till ett mönster på sidan `Where is **{HumanResourcesFormTitle}** on the server?`Mönster i avsnittet Förbättra **appens** **prestanda** med rätt klammerparentessyntax, till exempel .

Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](reference-pattern-syntax.md#explicit-lists). 

## <a name="do-not-change-entity-type"></a>Ändra inte entitetstyp

LUIS tillåter inte att du ändrar typen av entitet eftersom den inte vet vad du ska lägga till eller ta bort för att konstruera den entiteten. För att ändra typen är det bättre att skapa en ny entitet av rätt typ med ett något annorlunda namn. När entiteten har skapats, i varje uttryck, ta bort det gamla etiketten entitetsnamn och lägg till det nya entitetsnamnet. När alla yttranden har ändrats tar du bort den gamla entiteten. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Använd fördefinierade modeller](howto-add-prebuilt-models.md) 

Läs mer om:
* Hur man [tränar](luis-how-to-train.md)
* Så här [testar du](luis-interactive-test.md)
* Så här [publicerar du](luis-how-to-publish-app.md)
* Mönster:
    * [Begrepp](luis-concept-patterns.md)
    * [Syntax](reference-pattern-syntax.md)
* [Fördefinierade entiteter GitHub-databas](https://github.com/Microsoft/Recognizers-Text)
* [Begrepp för datautvinning](luis-concept-data-extraction.md)


 
