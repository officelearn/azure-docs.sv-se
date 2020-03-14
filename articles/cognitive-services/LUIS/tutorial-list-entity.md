---
title: 'Självstudie: lista entitet – LUIS'
description: Hämta data som matchar en fördefinierad lista med objekt. Varje objekt i listan kan ha synonymer som också matchar exakt
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 1cfeccbd54e8ef8ec315d53fc7a766760c92a0d1
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297415"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Självstudie: Hämta exakta text matchade data från en uttryck med list-entitet

I den här självstudien får du lära dig hur du hämtar data som exakt matchar en fördefinierad lista med objekt.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera app och Använd befintlig avsikt
> * Lägg till listentitet
> * Utbilda, publicera och fråga appen för att hämta extraherade data

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Vad är en listentitet?

En listentitet är en exakt textmatchning av orden i ett uttryck. Varje objekt i listan kan innehålla en lista med synonymer. Använd en lista entitet när du vill ha en exakt matchning.

För det här importerade pizza-programmet skapar du en List-entitet för de olika typerna av Pizza-Crust.

En listentitet är ett bra alternativ för den här typen av data när:

* Datavärdena är en känd uppsättning.
* Uppsättningen inte överskrider de högsta [gränserna](luis-boundaries.md) för LUIS för den här entitetstypen.
* Texten i uttryck är inte Skift läges känslig med en synonym eller ett kanoniskt namn. LUIS använder inte listan bortom matchningen. Ordstamsigenkänning, plural och andra variationer kan inte lösas enbart med en listentitet. Om du vill hantera variationer bör du överväga att använda ett [mönster](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) med valfri textsyntax.

> [!CAUTION]
> Om du inte är säker på om du vill att en lista entitet eller en enhet med en fras lista ska vara en beskrivare, är den bästa och mest flexibla metoden att använda en enhet som har lärts med en fras lista som en beskrivning. Med den här metoden kan LUIS lära sig och utöka värdena för de data som ska extraheras.

## <a name="import-example-json-and-add-utterances"></a>Importera exempel. JSON och Lägg till yttranden

1.  Ladda ned och spara [app-JSON-filen](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. Den importerade appen har en `OrderPizza` avsikt. Välj den avsikten och Lägg till några yttranden med nya crust-typer:

    |Ny yttranden|
    |--|--|
    |Beställ en pan crust Small pepperoni pizza|
    |3 tunn crust Hawaii,-pizzor|
    |leverera 2 fyllda crust pizzor med bröd käppar|
    |en tjocka crust-pizza för upphämtning|
    |en djup skål pepperoni pizza|

## <a name="crust-list-entity"></a>Crust lista entitet

Nu när **OrderPizza** -avsikten har exempel yttranden med crust-typer, måste Luis förstå vilka ord som representerar de crust typerna.

Exempel på primärt namn och synonymer är:

|Kanoniskt namn|Synonymer|
|--|--|
|Djup skål|djup<br>djup skål crust<br>tjock<br>tjocka crust|
|Alleuropeiska|Reguljära<br>originalspråket<br>gängse<br>vanliga crust<br>Ursprunglig crust<br>normal crust|
|Loc|fyllda crust|
|Tunn|tunn crust<br>skalning<br>skalnings crust|

1. Välj **Entities** (Entiteter) på den vänstra panelen.

1. Välj **+ skapa**.

1. I dialogrutan för entiteter anger du `CrustList` som entitetsnamn och **List** (Lista) som entitetstyp. Välj **Nästa**.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av dialog rutan skapa ny entitets-popup](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. På sidan **skapa en lista entitet** anger du kanoniska namn och synonymer för varje kanoniskt namn och väljer sedan **skapa**.

    > [!div class="mx-imgBorder"]
    > ![skärm bild för att lägga till objekt i listan entitet](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    När du lägger till en lista entitet i en LUIS-app, behöver du inte [märka](label-entity-example-utterance.md) texten med list-entiteten. Den används för alla yttranden i alla syften.

## <a name="train-the-app-before-testing-or-publishing"></a>Träna appen innan du testar eller publicerar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicera appen att fråga från slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Gå till slutet av URL: en i adressen och ange följande uttryck:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Den sista frågesträngsparametern är `query`, yttrande**frågan**.


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

    Typerna av crust hittades som exakta text matchningar och returnerades i JSON-svaret. Den här informationen används av klient programmet för att bearbeta beställningen.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* Konceptuell information om [listentitet](luis-concept-entity-types.md#list-entity)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)
* [Begrepp – entiteter](luis-concept-entity-types.md)
* [JSON-referens för entiteten för reguljärt uttryck](reference-entity-regular-expression.md?tabs=V3)
* [Så här lägger du till entiteter för att extrahera data](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Nästa steg
Den här själv studie kursen lade till exempel yttranden och skapade sedan en List-entitet för att extrahera exakt text matchningar från yttranden. När appen har tränats upp och publicerats identifierade en fråga till slutpunkten aktuell avsikt och extraherade data returnerades.

> [!div class="nextstepaction"]
> [Lägg till en fördefinierad entitet med en roll](tutorial-entity-roles.md)

