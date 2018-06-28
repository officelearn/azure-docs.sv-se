---
title: Självstudie om att skapa en LUIS-app för att få exakt textmatchning i listdata – Azure | Microsoft Docs
description: I den här självstudien skapar du en enkel LUIS-app med hjälp av avsikter och listentiteter för att extrahera data i den här snabbstarten.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264835"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>Självstudie: skapa en app med hjälp av en listentitet
I den här självstudien skapar du en app som visar hur det går till att hämta data som matchar en fördefinierad lista. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå listentiteter 
> * Skapa ny LUIS-app för dryckesdomänen med avsikten OrderDrinks
> * Lägga till avsikten _None_ (Ingen) och lägga till exempelyttranden
> * Lägga till listentitet för att extrahera dryckesobjekt från yttrande
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="purpose-of-the-list-entity"></a>Syftet med listentiteten
Den här appen tar emot dryckesbeställningar som `1 coke and 1 milk please` och returnerar data såsom typen av dryck. En **listentitet** för drycker letar efter exakta textmatchningar och returnerar matchningarna. 

En listentitet är ett bra alternativ för den här typen av data när datavärdena är en känd datamängd. Namnen på dryckerna kan variera på grund av slang och förkortningar, men de ändras inte särskilt ofta. 

## <a name="app-intents"></a>App-avsikter
Avsikterna är de kategorier av saker som användaren vill ha. Den här appen har två avsikter: OrderDrink och None. Avsikten [None](luis-concept-intent.md#none-intent-is-fallback-for-app) (Ingen) har syftet att indikera allt som faller utanför appen.  

## <a name="list-entity-is-an-exact-text-match"></a>Listentitet är en exakt textmatchning
Syftet med entiteten är att hitta och kategorisera delar av texten i yttrandet. En [listentitet](luis-concept-entity-types.md) möjliggör en exakt matchning av ord eller fraser.  

För den här dryckesappen extraherar LUIS dryckesbeställningen på ett sätt som gör att en standardbeställning kan skapas och genomföras. I LUIS kan yttranden ha variationer, förkortningar och slang. 

Enkla exempel på yttranden från användare innefattar:

```
2 glasses of milk
3 bottles of water
2 cokes
```

Förkortade versioner av eller slangvarianter på yttranden innefattar:

```
5 milk
3 h2o
1 pop
```
 
Listentiteten matchar `h2o` till vatten, och `pop` till läsk.  

## <a name="what-luis-does"></a>What LUIS gör
När yttrandets avsikt och entiteter identifierats, [extraherats](luis-concept-data-extraction.md#list-entity-data) och returnerats i JSON från [slutpunkten](https://aka.ms/luis-endpoint-apis) är LUIS klar. Det anropande programmet eller chattroboten använder JSON-svaret och uppfyller begäran på det sätt som appen eller chattroboten har instruerats att göra. 

## <a name="create-a-new-app"></a>Skapa en ny app
1. Logga in på [LUIS-webbplatsen][LUIS]. Se till att logga in på den [region][LUIS-regions] där du behöver få LUIS-slutpunkterna publicerade.

2. På [LUIS-webbplatsen][LUIS] väljer du **Create new app** (Skapa ny app).  

    ![Skapa ny app](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. I popup-dialogrutan anger du namnet `MyDrinklist`. 

    ![Ge appen namnet MyDrinkList](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. När processen är klar visar appen sidan **Intents** (Avsikter) med avsikten **None** (Ingen). 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "Skärmbild på sidan Intents (Avsikter)")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Skapa en ny avsikt

1. På sidan **Intents** (Avsikter) väljer du **Create new intent** (Skapa ny avsikt). 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "Skärmbild på sidan Intents (Avsikter) med knappen ”Create new intent” (Skapa ny avsikt) markerad")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. Ange det nya avsiktsnamnet `OrderDrinks`. Den här avsikten ska väljas när en användare vill beställa en dryck.

    Genom att skapa en avsikt skapar du den primära kategorin för information som du vill identifiera. Tack vare att kategorin får ett namn kan andra program som använder LUIS-frågeresultaten använda det kategorinamnet för att hitta ett lämpligt svar eller utföra lämpliga åtgärder. LUIS svarar inte på de här frågorna, utan identifierar bara vilken typ av information som det frågas om i naturligt språk. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "Skärmbild på skapande av ny OrderDrings-avsikt")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. Lägg till flera yttranden till avsikten `OrderDrinks` som du förväntar dig att en användare begär, till exempel:

    | Exempel på yttranden|
    |--|
    |Skulle jag kunna få 2 cola och en flaska vatten till mitt rum?|
    |2 perrier med en liten limeskruv|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "Skärmbild på yttrande som anges på sidan för OrderDrinks-avsikt")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Lägga till yttranden till avsikten None (Ingen)

LUIS-appen har för närvarande inga yttranden för avsikten **None** (Ingen). Den behöver yttranden som du inte vill att appen svarar på. Därför behöver den ha yttranden i avsikten **None** (Ingen). Lämna den inte tom. 

1. Välj **Intents** (Avsikter) på den vänstra panelen. 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "Skärmbild på Intents-länk (Avsikter) som väljs på den vänstra panelen")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. Välj avsikten **None** (Ingen). Lägg till tre yttranden som din använda kan tänkas ange men som inte är relevanta för appen:

    | Exempel på yttranden|
    |--|
    |Avbryt!|
    |Hej då|
    |Vad är det som händer?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>När yttrandet förväntas för avsikten None (Ingen)
I det LUIS-anropande programmet (till exempel en chattrobot) kan roboten när LUIS returnerar avsikten **None** (Ingen) för ett yttrande fråga om användaren vill avsluta konversationen. Roboten kan även ge fler anvisningar för att fortsätta konversationen om användaren inte vill avsluta den. 

Entiteter fungerar i avsikten **None** (Ingen). Om **None** (Ingen) är avsikten med högst poäng men en entitet som är väsentlig för chattroboten extraheras kan chattroboten följa upp med en fråga som fokuserar användarens avsikt. 

## <a name="create-a-menu-entity-from-the-intent-page"></a>Skapa en menyentitet från sidan Intent (Avsikt)
Nu när de två avsikterna har yttranden behöver LUIS förstå vad en dryck är för något. Gå tillbaka till avsikten `OrderDrinks` och etikettera (märk) dryckerna i ett yttrande genom att följa stegen:

1. Gå tillbaka till avsikten `OrderDrinks` genom att välja **Intents** (Avsikter) på den vänstra panelen.

2. Välj `OrderDrinks` från listan över avsikter.

3. I yttrandet `Please send 2 cokes and a bottle of water to my room` väljer du ordet `water`. En listrutemeny visas med en textruta längst upp att för skapa en ny entitet. Ange entitetsnamnet `Drink` i textrutan och välj sedan **Create new entity** (Skapa ny entitet) i listrutan. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "Skärmbild på ny entitet som skapas genom att ett ord väljs i yttrandet")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. I popup-fönstret väljer du entitetstypen **List** (Lista). Lägg till synonymen `h20`. Välj returtangenten efter varje synonym. Lägg inte till `perrier` i listan över synonymer. Det läggs till i nästa steg som ett exempel. Välj **Done** (Klar).

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "Skärmbild på ny entitet som konfigureras")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. Nu när entiteten har skapats märker du de andra synonymerna för vatten genom att välja synonymen för vatten och sedan välja `Drink` i listrutan. Följ menyn till höger och välj sedan `Set as synonym` följt av `water`.

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "Skärmbild på yttrande som märks med befintlig entitet")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>Ändra listentiteten från sidan Entity (Entitet)
Entiteten för dryckeslistan skapas men har inte många objekt eller synonymer. Om du känner till vissa av termerna, förkortningarna och slanguttrycken går det snabbare att fylla i listan på sidan **Entity** (Entitet). 

1. Välj **Entities** (Entiteter) på den vänstra panelen.

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "Skärmbild på Entities (Entiteter) som väljs på den vänstra panelen")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. Välj `Drink` från listan över entiteter.

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "Skärmbild på entiteten Drink (Dryck) som väljs från listan över entiteter")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. I textrutan anger du `Soda pop` och sedan retur. Det här är en term som används för olika typer av kolsyrehaltiga drycker. Olika kulturer har sina egna namn och slanguttryck för den här typen av dryck.

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "Skärmbild på kanoniskt namn som anges")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. På samma rad som `Soda pop` anger du synonymer som: 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    Synonymerna kan innehålla fraser, skiljetecken, genitiv och plural. Eftersom listentiteten är en exakt matchning (förutom skiftläget), behöver synonymerna ha alla varianter. Du kan expandera listan allteftersom du lär dig fler varianter från frågeloggarna eller granskar slutpunktsträffar. 

    Den här artikeln har bara några synonymer i syfte att hålla exemplet kort. En LUIS-app på produktionsnivå skulle innehålla många synonymer och granskas och expanderas regelbundet. 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "Skärmbild på synonymer som läggs till")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till ändringarna av avsikterna och entiteterna (modellen) förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Träna appen](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Träningen är klar](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL
För att få en LUIS-förutsägelse i en chattrobot eller i ett annat program måste du publicera appen. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera). 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "Skärmbild på publiceringsknapp som väljs")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera). 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Skärmbild på knappen ”Publish to production slot” (Publicera till produktionsplats) som väljs")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande
1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "Skärmbild på slutpunkts-URL på sidan Publish (Publicera)")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. Gå till slutet av URL:en i adressen och ange `2 cokes and 3 waters`. Den sista frågesträngsparametern är `q`, yttrande**f**rågan. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `OrderDrinks` med de två dryckestyperna `cokes` och `waters`.

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Var är bearbetningen av naturligt språk i listentiteten? 
Eftersom listentiteten är en exakt matchning förlitar den sig inte på bearbetning av naturligt språk (eller maskininlärning). LUIS använder bearbetning av naturligt språk (eller maskininlärning) för att välja rätt avsikt med högst poäng. Dessutom kan ett yttrande vara en blandning av mer än en entitet eller mer än en typ av entitet. Varje yttrande bearbetas för alla entiteter i appen, inklusive entiteter för bearbetning av naturligt språk (eller maskininlärning), till exempel entiteten **Simple** (Enkel).

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Med hjälp av endast två avsikter och en listentitet har den här appen identifierat en frågeavsikt i naturligt språk och returnerat extraherade data. 

Din chattrobot har nu tillräckligt med information för att bestämma den primära åtgärden, `OrderDrinks`, och vilka typer av drycker som beställdes från listentiteten Drink (Dryck). 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och data från entiteten för att gå vidare. LUIS utför inte detta programmässiga arbete för roboten eller det anropande programmet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. För att göra det väljer du menyn med tre punkter (...) till höger om appnamnet i applistan och väljer **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du lägger till en entitet för reguljärt uttryck](luis-quickstart-intents-regex-entity.md)

Lägg till **numret** [fördefinierad entitet](luis-how-to-add-entities.md#add-prebuilt-entity) för att extrahera numret. 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
