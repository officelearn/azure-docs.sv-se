---
title: 'Självstudiekurs: Lista entitet - LUIS'
description: Hämta data som matchar en fördefinierad lista med objekt. Varje objekt i listan kan ha synonymer som också matchar exakt
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 1cfeccbd54e8ef8ec315d53fc7a766760c92a0d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79297415"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Självstudiekurs: Hämta exakta textmatchade data från ett uttryck med listentiteten

I den här självstudien förstår du hur du hämtar data som exakt matchar en fördefinierad lista med objekt.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera app och använda befintlig avsikt
> * Lägg till listentitet
> * Träna, publicera och fråga app för att få extraherade data

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Vad är en listentitet?

En listentitet är en exakt textmatchning av orden i ett uttryck. Varje objekt i listan kan innehålla en lista med synonymer. Använd en listentitet när du vill ha en exakt matchning.

För det importerade pizzaprogrammet skapar du en listenhet för de olika typerna av pizzaskorpa.

En listentitet är ett bra alternativ för den här typen av data när:

* Datavärdena är en känd uppsättning.
* Uppsättningen inte överskrider de högsta [gränserna](luis-boundaries.md) för LUIS för den här entitetstypen.
* Texten i uttryck är en skiftlägesokänslig matchning med en synonym eller det kanoniska namnet. LUIS använder inte listan utöver matchningen. Ordstamsigenkänning, plural och andra variationer kan inte lösas enbart med en listentitet. Om du vill hantera variationer bör du överväga att använda ett [mönster](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) med valfri textsyntax.

> [!CAUTION]
> Om du inte är säker på om du vill ha en listentitet eller en maskininlärd entitet med en fraslista som deskriptor är den bästa och mest flexibla metoden att använda en maskininlärd entitet med en fraslista som deskriptor. Med den här metoden kan LUIS lära sig och utöka värdena för data som ska extraheras.

## <a name="import-example-json-and-add-utterances"></a>Importera exempel .json och lägga till yttranden

1.  Ladda ner och spara [appen JSON-fil](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. Den importerade appen `OrderPizza` har en avsikt. Välj den avsikten och lägg till några yttranden med nya skorpatyper:

    |Nya yttranden|
    |--|--|
    |beställ en pan skorpa liten pepperoni pizza|
    |3 tunna skorpa hawaiianska pizzor|
    |leverera 2 fyllda skorpa pizzor med brödpinnar|
    |en tjock skorpa pizza för pickup|
    |en djup maträtt pepperoni pizza|

## <a name="crust-list-entity"></a>Enhet för skorpalista

Nu när **OrderPizza** avsikten har exempel yttranden med skorpa typer, LUIS måste förstå vilka ord som representerar skorpa typer.

Exempel på det primära namnet och synonymerna är:

|Kanoniskt namn|Synonymer|
|--|--|
|Djup maträtt|Djup<br>djup skål skorpa<br>Tjock<br>tjock skorpa|
|Pan|Regelbundna<br>Ursprungliga<br>Normal<br>vanlig skorpa<br>original skorpa<br>normal skorpa|
|Fyllda|fyllda skorpa|
|Tunn|tunn skorpa<br>Mager<br>mager skorpa|

1. Välj **Entities** (Entiteter) på den vänstra panelen.

1. Välj **+ Skapa**.

1. I dialogrutan för entiteter anger du `CrustList` som entitetsnamn och **List** (Lista) som entitetstyp. Välj **Nästa**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild på dialogrutan för att skapa en ny entitet](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. På sidan **Skapa en lista** anger du de kanoniska namnen och synonymerna för varje kanoniskt namn och väljer sedan **Skapa**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av hur du lägger till objekt i listentiteten](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    När du lägger till en listentitet i en LUIS-app behöver du inte [märka](label-entity-example-utterance.md) texten med listentiteten. Den tillämpas på alla yttranden i alla avseenden.

## <a name="train-the-app-before-testing-or-publishing"></a>Träna appen innan du testar eller publicerar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicera appen att fråga från slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Gå till slutet av webbadressen i adressen och ange följande uttryck:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Den senaste frågesträngparametern är `query`, uttrycksfrågan . **query**


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    Typerna av skorpa hittades som exakta textmatchningar och returnerades i JSON-svaret. Den här informationen används av klientprogrammet för att bearbeta ordern.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* Konceptuell information om [listentitet](luis-concept-entity-types.md#list-entity)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)
* [Koncept - enheter](luis-concept-entity-types.md)
* [JSON-referens för reguljär uttrycksenhet](reference-entity-regular-expression.md?tabs=V3)
* [Så här lägger du till entiteter för att extrahera data](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Nästa steg
Den här självstudien lade till exempelyttranden och skapade sedan en listentitet för att extrahera exakta textmatchningar från yttrandena. När appen har tränats upp och publicerats identifierade en fråga till slutpunkten aktuell avsikt och extraherade data returnerades.

> [!div class="nextstepaction"]
> [Lägga till fördefinierad entitet med en roll](tutorial-entity-roles.md)

