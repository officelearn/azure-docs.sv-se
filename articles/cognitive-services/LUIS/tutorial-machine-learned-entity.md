---
title: 'Självstudie: extrahera strukturerade data med enhets medveten entitet – LUIS'
titleSuffix: Azure Cognitive Services
description: Extrahera strukturerade data från en uttryck med hjälp av enheten som registrerats av enheten. Om du vill öka extraherings precisionen lägger du till under komponenter med beskrivningar och begränsningar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 1d05df454e3d7a4f54e7451b81dd2a6470a8200b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806428"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Självstudie: extrahera strukturerade data från användare uttryck med enheter som har lärts i Language Understanding (LUIS)

I den här självstudien extraherar du strukturerade data från en uttryck med hjälp av den enhet som sparats av datorn

Den enhet som har registrerats av enheten har stöd för [begreppet modell disposition](luis-concept-model.md#v3-authoring-model-decomposition) genom att tillhandahålla del komponent enheter med sina beskrivningar och begränsningar.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Importera exempelappen
> * Lägg till enhets enheten har lästs in
> * Lägg till del komponent
> * Lägg till del komponentens Beskrivning
> * Lägg till del komponents begränsning
> * Träna appen
> * Testa appen
> * Publicera app
> * Hämta enhets förutsägelse från slut punkt

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Varför ska jag använda en enhet som har lärts från enheten?

Den här självstudien lägger till en enhet som har lärts fram för att extrahera data från en uttryck.

Syftet med en entitet är att definiera de data som ska extraheras. Detta innefattar att ge data ett namn, en typ (om möjligt), eventuell lösning av data om det förekommer tvetydigheter och den exakta text som utgör data.

För att kunna definiera entiteten måste du skapa entiteten och sedan märka texten som representerar entiteten i exemplet uttryck. De här märkta exemplen lär LUIS vad entiteten är och var den finns i en uttryck.

## <a name="entity-decomposability-is-important"></a>Datasammansättning av enheter är viktigt

Datasammansättning av enheter är viktigt för både avsikts förutsägelse och data extrahering.

Börja med en enhet som har lärts in, vilket är den första och den översta nivån för data extrahering. Dela sedan upp entiteten i de delar som krävs av klient programmet.

Även om du kanske inte vet hur detaljerad du vill ha din entitet när du startar din app, är det bästa sättet att börja med en enhet som har lärts in och sedan dela med del komponenter som din app vuxen.

I praktiska termer kommer du att skapa en enhets medveten entitet som representerar en order för en pizza-app. Ordningen bör ha alla delar som krävs för att fullfil ordern. För att komma igång extraherar entiteten beställnings besläktad text, hämtar storlek och kvantitet.

En uttryck för `Please deliver one large cheese pizza to me` bör extrahera `one large cheese pizza` som order och sedan extrahera `1` och `large`.

Det finns ytterligare diskompositioner som du kan lägga till, till exempel skapa under komponenter för toppings eller crust. Efter den här självstudien bör du känna till att lägga till dessa del komponenter till din befintliga `Order` entitet.

## <a name="import-example-json-to-begin-app"></a>Importera exempel. JSON för att starta appen

1.  Ladda ned och spara [app-JSON-filen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Etikettext som entiteter i exempel yttranden

Om du vill extrahera information om en pizza-beställning skapar du en `Order`-enhet på översta nivån.

1. På sidan **avsikter** väljer du **OrderPizza** avsikt.

1. I exempel listan yttranden väljer du följande uttryck.

    |Beställ exempel uttryck|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Börja markera precis innan den vänstra texten i `pickup` (#1) och gå sedan precis bortom den högra texten `anchovies` (#2 – Detta avslutar etikett processen). En popup-meny visas. I popup-rutan anger du namnet på entiteten som `Order` (#3). Välj sedan `Order - Create new entity` i listan (#4).

    ![Etikettens början och slutet på texten för den fullständiga ordningen](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > En entitet är inte alltid hela uttryck. I det här fallet anger `pickup` hur ordningen ska tas emot. Från ett konceptuellt perspektiv bör `pickup` vara en del av den märkta entiteten för ordern.

1. I rutan **Välj typ av enhet** väljer du **Lägg till struktur** och sedan **Nästa**. Strukturen är nödvändig för att lägga till del komponenter, till exempel storlek och kvantitet.

    ![Lägg till struktur i entitet](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Lägg till `Size` i rutan **struktur** i rutan **skapa en dator med förhandlad enhet** och välj sedan Retur.
1. Om du vill lägga till en **Beskrivning**väljer du `+` i avsnittet **Descriptor för storlek** och väljer sedan **Skapa ny fras lista**.

1. I rutan **Skapa ny fras lista** anger du namnet `SizeDescriptor` anger sedan värden för: `small`, `medium`och `large`. När **förslags** rutan fylls i väljer du `extra large`och `xl`. Välj **färdig** för att skapa den nya fras listan.

    Den här fras List beskrivningen hjälper `Size`-underkomponenten att hitta ord som är relaterade till storlek genom att tillhandahålla exempel ord. Den här listan behöver inte inkludera varje storlek i Word men ska innehålla ord som förväntas indikera storlek.

    ![Skapa en beskrivning för del komponenten storlek](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. I fönstret **skapa en dator med förhandlad enhet** väljer du **skapa** för att slutföra skapandet av komponenten `Size`.

    `Order` entiteten med en `Size` komponent skapas, men endast `Order` entiteten har tillämpats på uttryck. Du måste märka `Size` entitetens text i exemplet uttryck.

1. I samma exempel uttryck kan du namnge del komponenten **storlek** i `large` genom att markera ordet och sedan välja entiteten **storlek** i list rutan.

    ![Namnge entiteten storlek för text i uttryck.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    Raden är solid under texten, eftersom både etikettering och förutsägelse matchning, eftersom du uttryckligen har namngett texten.

1. Märk `Order` entiteten i återstående yttranden tillsammans med entiteten storlek. Hakparenteserna i texten visar etiketten `Order` entiteten och `Size` entiteten i.

    |Beställ exempel yttranden|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Skapa entitet och under komponenter i alla återstående exempel yttranden.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hur behandlar du underförstådda data, till exempel bokstavs `a` som inbegriper en enda pizza? Eller om det saknas `pickup` och `delivery` för att indikera var pizza förväntas? Eller om det saknas en storlek för att ange din standard storlek på liten eller stor? Överväg att behandla underförstådd data hantering som en del av dina affärs regler i-klient programmet i stället för eller utöver LUIS.

1. Välj **träna**för att träna appen. Träningen tillämpar ändringarna, till exempel nya entiteter och den märkta yttranden, i den aktiva modellen.

1. Efter utbildningen lägger du till ett nytt exempel uttryck i avsikten för att se hur väl LUIS förstår den enhet som har lärts ur enheten.

    |Beställ exempel uttryck|
    |--|
    |`pickup XL meat lovers pizza`|

    Den övergripande översta entiteten, `Order` är märkt och del komponenten `Size` är också märkt med streckade linjer. Detta är en lyckad förutsägelse.

    ![Nytt exempel uttryck förväntat med entitet](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Den streckade linjen visar förutsägelsen.

1. Om du vill ändra förutsägelsen till en etikettad entitet markerar du raden och väljer **Bekräfta enhets förutsägelser**.

    ![Godkänn förutsägelse genom att välja Bekräfta enhets förutsägelse.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    I det här läget fungerar enheten som sparats av datorn eftersom den kan hitta entiteten i ett nytt exempel uttryck. När du lägger till exempel yttranden, om entiteten inte är korrekt förväntad, kan du namnge entiteten och del komponenterna. Om entiteten förutsägs korrekt, se till att bekräfta förutsägelserna.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Lägg till ett fördefinierat nummer för att extrahera data

Orderinformationen bör också innehålla hur många av ett objekt som finns i ordningen, till exempel hur många pizzor. Om du vill extrahera dessa data måste en ny dator som har lärts läggas till i `Order` och komponenten måste ha en begränsning av ett fördefinierat nummer. Genom att begränsa entiteten till ett fördefinierat nummer kommer entiteten att hitta och extrahera siffror om texten är en siffra, `2`eller text `two`.

Börja med att lägga till den fördefinierade nummer enheten i appen.

1. Välj **entiteter** i den vänstra menyn och välj sedan **+ Lägg till fördefinierad entitet**.

1. I rutan **Lägg till fördefinierade entiteter** söker du efter och väljer **nummer** och väljer sedan **Slutför**.

    ![Lägg till fördefinierade entitet](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Den fördefinierade entiteten läggs till i appen, men är inte ett villkor ännu.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Skapa del komponent entitet med begränsning för att hjälpa till att extrahera data

`Order` entiteten ska ha en `Quantity` del komponent för att fastställa hur många av ett objekt som finns i ordern. Antalet bör begränsas till ett tal så att de extraherade data omedelbart kan användas av klient programmet.

En begränsning används som en text matchning, antingen med en exakt matchning (till exempel en List-entitet) eller via reguljära uttryck (till exempel en reguljär uttrycks enhet eller en fördefinierad entitet).

Genom att använda en begränsning extraheras endast text som matchar den begränsningen.

1. Välj **entiteter** och välj sedan `Order` entiteten.
1. Välj **+ Lägg till komponent** och ange sedan namnet `Quantity` Välj sedan Retur för att lägga till den nya entiteten i appen.
1. När aviseringen är klar väljer du del komponenten `Quantity` och väljer sedan villkoret blyertspenna.
1. Välj det fördefinierade numret i list rutan.

    ![Skapa entiteten kvantitet med fördefinierat tal som begränsning.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    `Quantity` entiteten tillämpas om och endast om text som matchar den fördefinierade nummer entiteten hittas.

    Entiteten med begränsningen skapas men har ännu inte tillämpats på exemplet yttranden.

    > [!NOTE]
    > En del komponent kan kapslas i en del komponent upp till 5 nivåer. Det här visas inte i den här artikeln, men det är tillgängligt från portalen och API: et.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Label-exempel uttryck för att lära LUIS om entiteten

1. Välj **avsikter** från navigeringen till vänster och välj sedan **OrderPizza** avsikten. De tre talen i följande yttranden är märkta men visas visuellt under `Order` enhets linjen. Den här lägre nivån innebär att entiteterna hittas men inte räknas av `Order` entiteten.

    ![Ett fördefinierat nummer påträffades men betraktas inte som en affär senhet.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Namnge talen med `Quantity` entitet genom att markera `2` i exemplet uttryck och sedan välja `Quantity` i listan. Märk `6` och `1` i samma exempel uttryck.

    ![Etikettext med entiteten kvantitet.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Träna appen att tillämpa enhets ändringarna på appen

Välj **träna** för att träna appen med de nya yttranden.

![Träna appen och granska sedan exemplet yttranden.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

I det här läget har ordern viss information som kan extraheras (storlek, kvantitet och total order text). Det finns ytterligare raffinering av `Order` entiteten, till exempel pizza toppings, typ av crust och sid beställningar. Var och en av dem ska skapas som del komponenter i `Order` entiteten.

## <a name="test-the-app-to-validate-the-changes"></a>Testa appen för att verifiera ändringarna

Testa appen med hjälp av den interaktiva **test** panelen. Med den här processen kan du ange en ny uttryck och sedan Visa förutsägelse resultatet för att se hur väl den aktiva och utbildade appen fungerar. Avsikten med avsikten bör vara ganska trygg (över 70%) och enhets extraheringen bör minst Hämta `Order` entiteten. Information om entiteten order kan saknas eftersom 5 yttranden inte räcker för att hantera varje fall.

1. Välj **Test** i det övre navigeringsfältet.
1. Ange uttryck-`deliver a medium veggie pizza` och välj RETUR. Den aktiva modellen förutsäger rätt avsikt med över 70% konfidensgrad.

    ![Ange en ny uttryck för att testa avsikten.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Välj **kontrol lera** om du vill se enhets förutsägelserna.

    ![Visa enhets förutsägelserna i den interaktiva test panelen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    Storleken identifierades korrekt. Kom ihåg att exemplet yttranden i `OrderPizza` avsikt inte har något exempel på `medium` som en storlek, men använder en beskrivning av en `SizeDescriptor` fras lista som innehåller medel.

    Kvantiteten är inte korrekt förväntad. Du kan åtgärda detta genom att lägga till fler exempel yttranden med det ordet för att ange antal och etikett som Word som en `Quantity` entitet.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicera appen för att få åtkomst till den från HTTP-slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction--from-http-endpoint"></a>Hämta avsikts-och enhets förutsägelse från HTTP-slutpunkt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av URL: en i adressen och ange samma fråga som du angav i den interaktiva test panelen.

    `deliver a medium veggie pizza`

    Den sista frågesträngsparametern är `query`, yttrande**frågan**.

    ```json
    {
        "query": "deliver a medium veggie pizza",
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
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Självstudie – avsikter](luis-quickstart-intents-only.md)
* [Begrepp – entiteter](luis-concept-entity-types.md) konceptuell information
* [Begrepp – funktioner](luis-concept-feature.md) konceptuell information
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använder appen en enhet som har identifierats av enheten för att hitta avsikten med användarens uttryck och extrahera information från den uttryck. Genom att använda den enhet som du har lärt dig kan du dela upp informationen om entiteten.

> [!div class="nextstepaction"]
> [Lägg till en fördefinierad keyPhrase-entitet](luis-quickstart-intent-and-key-phrase.md)
