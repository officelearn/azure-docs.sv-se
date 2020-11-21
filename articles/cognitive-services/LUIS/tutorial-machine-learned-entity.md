---
title: 'Självstudie: extrahera strukturerade data med Machine Learning-entitet – LUIS'
description: Extrahera strukturerade data från en uttryck med hjälp av enheten för maskin inlärning. Om du vill öka extraherings precisionen lägger du till underentiteter med funktioner.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: aa2783abe86b90a907510dec8d7eb40162820ad5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025267"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Självstudie: extrahera strukturerade data från User uttryck med Machine Learning-entiteter i Language Understanding (LUIS)

I den här självstudien extraherar du strukturerade data från en uttryck med hjälp av enheten för maskin inlärning.

Enheten för maskin inlärning stöder modellen för [modell nedbrytning](luis-concept-model.md#v3-authoring-model-decomposition) genom att tillhandahålla entiteter för entiteter med [funktioner](luis-concept-feature.md).

**I den här guiden får du lära dig att:**

> [!div class="checklist"]
> * Importera exempelappen
> * Lägg till enhet för maskin inlärning
> * Lägg till underentitet och funktion
> * Träna, testa, publicera app
> * Hämta enhets förutsägelse från slut punkt

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Varför ska jag använda en enhet för maskin inlärning?

Den här självstudien lägger till en enhet för maskin inlärning för att extrahera data från en användares uttryck.

Entiteten definierar de data som ska extraheras i uttryck. Detta innefattar att ge data ett namn, en typ (om möjligt), eventuell lösning av data om det förekommer tvetydigheter och den exakta text som utgör data.

Du måste göra följande för att kunna definiera data:
* Skapa entiteten
* Namnge texten, i exempel yttranden, som representerar entiteten. De här märkta exemplen lär LUIS vad entiteten är och var den finns i en uttryck.

## <a name="entity-decomposability-is-important"></a>Datasammansättning av enheter är viktigt

Datasammansättning av enheter är viktigt för både avsikts förutsägelse och data extrahering med entiteten.

Börja med en enhet för maskin inlärning, som är den första entiteten och den översta nivån för data extrahering. Dela sedan upp entiteten i underentiteter.

Även om du kanske inte vet hur detaljerad du vill ha din entitet när du startar din app, är det bästa sättet att börja med en enhet för maskin inlärning och sedan dela upp med underentiteter som din app vuxen.

I den här självstudien skapar du en enhet för maskin inlärning som representerar en order för en pizza-app. Entiteten extraherar beställnings besläktad text, hämtar storlek och kvantitet.

En uttryck av `Please deliver one large cheese pizza to me` ska extraheras `one large cheese pizza` som order och sedan extraheras `1` för kvantitet och `large` storlek.

## <a name="download-json-file-for-app"></a>Ladda ned JSON-fil för appen

Ladda ned och spara [app-JSON-filen](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

## <a name="import-json-file-for-app"></a>Importera JSON-fil för appen

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Skapa den enhet som har lärts

Om du vill extrahera information om en pizza-beställning skapar du en enhet på högsta nivån för maskin inlärning `Order` .

1. På sidan **avsikter** väljer du **OrderPizza** avsikt.

1. I exempel listan yttranden väljer du följande uttryck.

    |Beställ exempel uttryck|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Klicka och dra markören över texten för det första exempel avsikten. i menyn som visas anger du namnet på entiteten som `Order` . Välj sedan `Order Create new entity` i listan.

    ![Etikettens början och slutet på texten för den fullständiga ordningen](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > En entitet är inte alltid hela uttryck. I det här fallet `pickup` anger hur ordningen ska tas emot. Från ett konceptuellt perspektiv `pickup` ska du vara en del av den märkta entiteten för ordern.

1. I rutan **Välj typ av enhet** väljer du **Lägg till struktur** och sedan **Nästa**. Det krävs en struktur för att lägga till underentiteter som storlek och kvantitet.

    ![Skärm bild som visar fönstret Välj typ av enhet med alternativet Lägg till struktur markerat.](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. I rutan **Lägg till underentiteter (valfritt)** väljer **+** du på `Order` raden och sedan Lägg till `Size` och `Quantity` som underentiteter och väljer sedan **skapa**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar fönstret Lägg till underentiteter (valfritt) med underentiteter markerade.](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Redigera underentiteter för att förbättra extraheringen

Föregående steg skapar entiteten och underentiteten. Lägg till funktioner i underentiteterna för att förbättra extraheringen.

### <a name="improve-size-extraction-with-phrase-list"></a>Förbättra storleks extrahering med fras lista

1. Välj **entiteter** i den vänstra menyn och välj sedan **order** enhet.

1. På fliken **schema och funktioner** väljer du underentiteten **storlek** och väljer sedan **+ Lägg till funktion**.

1. Välj **Skapa ny fras lista** på den nedrullningsbara menyn.

1. I **list rutan skapa ny fras** anger du namnet `SizePhraselist` och anger värden för: `small` , `medium` , och `large` . När **förslags** rutan fylls i, Välj `extra large` och `xl` . Välj **skapa** för att skapa den nya fras listan.

    Den här fras List funktionen hjälper `Size` underentiteten att hitta ord som är relaterade till storlek genom att tillhandahålla exempel ord. Den här fras listan behöver inte inkludera varje storlek, men ska innehålla ord som förväntas indikera storlek.

### <a name="add-sizelist-entity"></a>Lägg till SizeList-entitet

Att lägga till en lista med kända storlekar som klient programmet känner av kan också hjälpa till att extrahera.

1. Välj **entiteter** i den vänstra menyn och välj sedan **+ skapa**.

1. Ange entitetsnamnet som `SizeListentity` och ange typen som **lista** så att det är enkelt att identifiera jämfört med det som `SizePhraselist` skapades i föregående avsnitt.

1. Lägg till de storlekar som klient programmet förväntar sig: `Small` ,, `Medium` `Large` och `XLarge` Lägg sedan till synonymer för var och en. Synonymerna ska vara de termer som en användare anger i Chat-roboten. Entiteten extraheras med en List-entitet när den matchade exakt det normaliserade värdet eller synonymerna.

    |Normaliserat värde|Synonymer|
    |--|--|
    |Liten|SM, SML, liten, minst|
    |Medium|MD, MDM, Regular, Average, mitten|
    |Stor|LG, LRG, Big|
    |XLarge|XL, störst, Giant|


    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar SizeList-fönstret och list objekt med XLarge valt.](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Lägg till funktion i SizeList-entiteten

1. Gå tillbaka till listan över entiteter genom att välja **entiteter** från vänster meny.

1. Välj **ordning** i listan över entiteter.

1. På fliken **schema och funktioner** väljer du entiteten **storlek** och väljer sedan **+ Lägg till funktion**.

1. Välj **@ SizeListentity** i list rutan.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Lägg till fördefinierad nummer enhet

Att lägga till en fördefinierad Number-entitet kommer också att hjälpa till att extrahera.

1. Välj **entiteter** i den vänstra menyn och välj sedan **Lägg till fördefinierad entitet**.

1. Välj **nummer** i listan och välj sedan **färdig**.

1. Gå tillbaka till listan över entiteter genom att välja **entiteter** från vänster meny.

### <a name="add-feature-of-prebuilt-number-entity"></a>Lägg till funktion i fördefinierad nummer entitet

1. Välj **ordning** i listan över entiteter.

1. På fliken **schema och funktioner** väljer du entiteten **kvantitet** och väljer sedan **+ Lägg till funktion**.

1. Välj **@-nummer** i list rutan.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Konfigurera nödvändiga funktioner

På sidan entitets **detalj för entiteten entitet väljer** du asterisken, `*` för både funktionen **@ SizeList** och funktionen **@ Number** . Asterisken visas i samma etikett som funktions namnet.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar @SizeList funktionen med asterisken och kräver varning.](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Etikett exempel yttranden

Den inlärda enheten skapas och underentiteterna har funktioner. För att slutföra extraherings förbättringen måste yttranden-exemplet märkas med underentiteterna.

1. Välj **avsikter** från det vänstra navigerings fältet och välj sedan **OrderPizza** avsikt.

1. Öppna **entiteten entitet** genom att välja **@** symbolen i kontext verktygsfältet.

1. Välj varje enhets rad i paletten och Använd sedan paletten för att välja entiteten i varje exempel uttryck. När du är färdig bör entitets listan se ut som följande bild.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av en nödvändig funktion för konfigurering](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>Träna appen

Välj **träna** för att träna appen. Träningen tillämpar ändringarna, till exempel nya entiteter och den märkta yttranden, i den aktiva modellen.

## <a name="add-a-new-example-utterance"></a>Lägg till ett nytt exempel uttryck

1. Efter utbildningen lägger du till ett nytt exempel uttryck i `OrderPizza` avsikten för att se hur väl Luis förstår enheten för maskin inlärning.

    |Beställ exempel uttryck|
    |--|
    |`I need a large pepperoni pizza`|

    Den övergripande översta entiteten `Order` är märkt och `Size` underentiteten märks också med prickade linjer.

    > [!div class="mx-imgBorder"]
    > ![En del skärm bild av den nya exempel uttryck som förutsägs med entitet](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Den streckade linjen visar förutsägelsen baserat på den aktuella utbildade appen.

1. Om du vill ändra förutsägelsen till en etikettad entitet markerar du kryss rutan på samma rad.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar ett exempel på uttryck med bock markeringen markerat.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    I det här läget fungerar enheten för maskin inlärning eftersom den kan hitta entiteten i ett nytt exempel uttryck. När du lägger till exempel yttranden, om entiteten inte är korrekt förväntad, kan du namnge entiteten och underentiteterna. Om entiteten förutsägs korrekt, se till att bekräfta förutsägelserna.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Träna appen att tillämpa enhets ändringarna på appen

Välj **träna** för att träna appen med den nya uttryck.

I det här läget har ordern viss information som kan extraheras (storlek, kvantitet och total order text). Det finns ytterligare raffinering av `Order` entiteten, till exempel pizza toppings, typ av crust och sid beställningar. Var och en av dem ska skapas som underentiteter till `Order` entiteten.

## <a name="test-the-app-to-validate-the-changes"></a>Testa appen för att verifiera ändringarna

Testa appen med hjälp av den interaktiva **test** panelen. Med den här processen kan du ange en ny uttryck och sedan Visa förutsägelse resultatet för att se hur väl den aktiva, tränade appen fungerar. Avsikten med avsikten bör vara ganska trygg (över 60%) och enhets extraheringen bör minst Hämta `Order` entiteten. Information om entiteten order kan saknas eftersom dessa yttranden inte räcker för att hantera varje fall.

1. Välj **Test** i det övre navigeringsfältet.
1. Ange uttryck `2 small cheese pizzas for pickup` och välj RETUR. Den aktiva modellen förutsäger rätt avsikt med över 60% konfidensgrad.


1. Välj **kontrol lera** om du vill se enhets förutsägelserna.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av Visa enhets förutsägelser i den interaktiva test panelen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicera appen för att få åtkomst till den från HTTP-slutpunkten

För att få en LUIS-förutsägelse i en chattrobot eller i ett annat klientprogram, måste du publicera appen på slutpunkten.

1. Välj **publicera** i det övre högra navigerings fältet.

    ![Skärm bild av LUIS publicera på slut punkts knapp i den övre högra menyn](./media/howto-publish/publish-button.png)

1. Välj **produktions** platsen och välj sedan **ändra inställningar**, Välj **Attitydanalys** och välj sedan **Slutför**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av LUIS publicera till slut punkt](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Välj länken **åtkomst till slut punkts-URL: er** i meddelandet för att gå till sidan **Azure-resurser** . Slut punkts-URL: erna visas som **exempel fråga**.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Hämta avsikts-och enhets förutsägelse från HTTP-slutpunkt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av webb adressen i adress fältet och Ersätt _YOUR_QUERY_HERE_ med samma fråga som du angav i den interaktiva test panelen.

    `2 small cheese pizzas for pickup`

    Den senaste QueryString-parametern är `query` , uttryck- **frågan**.

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
       }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Självstudie – avsikter](./tutorial-intents-only.md)
* [Begrepp – entiteter](luis-concept-entity-types.md) konceptuell information
* [Begrepp – funktioner](luis-concept-feature.md) konceptuell information
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använder appen en enhet för maskin inlärning för att hitta avsikten med användarens uttryck och extrahera information från den uttryck. Med hjälp av enheten för maskin inlärning kan du dela upp information om entiteten.

> [!div class="nextstepaction"]
> [Lägg till en fördefinierad keyPhrase-entitet](./luis-reference-prebuilt-keyphrase.md)