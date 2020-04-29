---
title: 'Självstudie: reguljär – uttrycks enhet-LUIS'
description: Extrahera konsekventa formaterade data från en uttryck med hjälp av entiteten för reguljära uttryck.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 5b585ee52880c474d3f2736b34a267012b390aad
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545842"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Självstudie: Hämta välformaterade data från uttryck
I den här självstudien skapar du en entitet för reguljära uttryck för att extrahera konsekventa formaterade data från en uttryck.

**I den här guiden får du lära dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera app
> * Lägga till avsikt
> * Lägg till entitet för reguljära uttryck
> * Utbilda, publicera och fråga appen för att hämta extraherade data

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Lägga till entiteter för reguljära uttryck

Använd entiteten reguljära uttryck för att hämta välformaterad text från en uttryck. Även om avsikten med yttrandet alltid fastställs med maskininlärning är just den här entitetstypen inte maskininlärd. En lämplig användning för entiteten för reguljära uttryck är all text som konsekvent kan representeras av ett [reguljärt uttryck](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

I det här exemplet används en _kort kod_ för att skicka SMS-meddelanden. Den här korta koden är en numerisk kod på 5 eller 6, som föregås av ett x, och som kan beskrivas med `x\d{5,6}`det reguljära uttrycket.

När du lägger till en entitet för reguljära uttryck i en LUIS-app, behöver du inte [märka](label-entity-example-utterance.md) texten med den vanliga Express entiteten. Den används för alla yttranden i alla syften.

## <a name="import-example-json-to-begin-app"></a>Importera exempel. JSON för att starta appen

1.  Ladda ned och spara [app-JSON-filen](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Skapa avsikt för att skicka bekräftelse meddelande meddelanden

1. Välj **+ skapa** för att skapa en ny avsikt att klassificera en uttryck för att skicka en bekräftelse text.

1. Ange `ConfirmationText` i popup-dialogrutan och välj sedan **Done** (Klar).

1. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Skicka pizza leverans tid till x123432|
    |Txt-x234567 för tid|
    |x23987 för meddelandet|

    Om du vill extrahera enheter som har registrerats av enheten ska du tillhandahålla exempel som inkluderar entiteten i en mängd olika yttranden, men med den här entiteten som inte är en enhet, är variationen inte viktig. Så länge texten stämmer överens med det reguljära uttrycket kommer den att extraheras.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Använda entiteten för reguljära uttryck för välformaterade data
Skapa en entitet för reguljära uttryck som matchar text numret. Det reguljära uttrycket matchar text men ignorerar Case-och Culture-varianter.

1. Välj **Entities** (Entiteter) på den vänstra panelen.

1. Välj **+ skapa** på sidan entiteter-lista.

1. I popup-dialog rutan anger du det nya entitetsnamnet `ConfirmationTextRegEx`, väljer **regex** som entitetstyp och väljer sedan **Nästa**.

    > [!div class="mx-imgBorder"]
    > ![Skapa entitets-skapande steg för entiteten för reguljära uttryck](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. På sidan **skapa en regex-entitet**anger `x\d{5,6}` du som **regex** -värdet och väljer sedan **skapa**.

    > [!div class="mx-imgBorder"]
    > ![Ange reguljärt uttryck för att extrahera data från exempel uttryck](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Välj **avsikter** på den vänstra menyn och sedan **ConfirmationText** avsikt att se det reguljära uttrycket som är etiketterat i yttranden.

    > [!div class="mx-imgBorder"]
    > ![Visa reguljärt uttryck som är märkt i exemplet yttranden](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Eftersom entiteten inte är en enhet som har registrerats av enheten används entiteten i yttranden och visas i LUIS-portalen så snart den skapas.

## <a name="train-the-app-before-testing-or-publishing"></a>Träna appen innan du testar eller publicerar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicera appen att fråga från slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av webb adressen i adress fältet och Ersätt _YOUR_QUERY_HERE_ med:

    `Text my pizza delivery to x23456 x234567 x12345`

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
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

    När en entitet för reguljära uttryck används extraherar LUIS namngivna data, vilket är programmässigt mer användbart för det klientprogram som tar emot JSON-svaret.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Begrepp – entiteter](luis-concept-entity-types.md)
* [JSON-referens för entiteten för reguljärt uttryck](reference-entity-regular-expression.md?tabs=V3)
* [Så här lägger du till entiteter för att extrahera data](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en ny avsikt, lagt till exempelyttranden och sedan skapat en entitet för ett reguljärt uttryck för att extrahera välformade data från yttranden. När appen har tränats upp och publicerats identifierade en fråga till slutpunkten aktuell avsikt och extraherade data returnerades.

> [!div class="nextstepaction"]
> [Lär dig mer om listentiteten](tutorial-list-entity.md)

