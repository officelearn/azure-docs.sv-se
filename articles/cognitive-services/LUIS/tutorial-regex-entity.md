---
title: 'Självstudiekurs: Entiteten för reguljäruttryck - LUIS'
description: Extrahera konsekvent formaterade data från ett uttryck med hjälp av entiteten Reguljärt uttryck.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 5b585ee52880c474d3f2736b34a267012b390aad
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545842"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Självstudiekurs: Få välformaterade data från yttrandet
I den här självstudien skapar du en entitet för reguljära uttryck för att extrahera konsekvent formaterade data från ett uttryck.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera app
> * Lägga till avsikt
> * Lägg till entitet för reguljära uttryck
> * Träna, publicera och fråga app för att få extraherade data

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Lägga till entiteter för reguljära uttryck

Använd entiteten för reguljära uttryck för att dra ut välformaterad text från ett uttryck. Även om avsikten med yttrandet alltid fastställs med maskininlärning är just den här entitetstypen inte maskininlärd. En bra användning för reguljära uttryck entiteten är en text som konsekvent kan representeras av ett [reguljärt uttryck](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

I det här exemplet används en _kort kod_ för att skicka textmeddelanden. Den här korta koden är en 5- eller 6-siffrig numerisk kod, som `x\d{5,6}`är föregåd med ett x och kan beskrivas med det reguljära uttrycket .

När du lägger till en reguljär uttrycksentitet i en LUIS-app behöver du inte [märka](label-entity-example-utterance.md) texten med den vanliga expressentiteten. Den tillämpas på alla yttranden i alla avseenden.

## <a name="import-example-json-to-begin-app"></a>Importera exempel .json för att starta appen

1.  Ladda ner och spara [appen JSON-fil](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Skapa avsikt för att skicka bekräftelse textmeddelanden

1. Välj **+ Skapa** om du vill skapa en ny avsikt att klassificera ett uttrycks avsikt att skicka en bekräftelsetext.

1. Ange `ConfirmationText` i popup-dialogrutan och välj sedan **Done** (Klar).

1. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Skicka pizza leveranstid till x123432|
    |Txt x234567 för tid|
    |x23987 för meddelandet|

    Om du vill extrahera datorinlärda entiteter bör du ge exempel som innehåller entiteten i en mängd olika yttranden, men med den här icke-maskininlärda entiteten är variationen inte viktig. Så länge texten matchar det reguljära uttrycket extraheras den.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Använda entiteten för reguljära uttryck för välformaterade data
Skapa en entitet för reguljära uttryck som matchar textnumret. Det här reguljära uttrycket matchar text men ignorerar fall- och kulturvarianter.

1. Välj **Entities** (Entiteter) på den vänstra panelen.

1. Välj **+ Skapa** på listsidan Entiteter.

1. I popup-dialogrutan anger du det `ConfirmationTextRegEx`nya entitetsnamnet , väljer **RegEx** som entitetstyp och väljer sedan **Nästa**.

    > [!div class="mx-imgBorder"]
    > ![Börja skapa entitetssteg för reguljära uttrycksentiteter](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. På skapa **en regex-entitet**anger du `x\d{5,6}` som **Regex-värde** och väljer sedan **Skapa**.

    > [!div class="mx-imgBorder"]
    > ![Ange reguljärt uttryck för att extrahera data från exempelyttrande](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Välj **Avsikter** på den vänstra menyn och sedan **bekräftelsetextens** avsikt att se det reguljära uttrycket som är märkt i yttrandena.

    > [!div class="mx-imgBorder"]
    > ![Visa reguljärt uttryck märkt i exempelyttranden](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Eftersom entiteten inte är en datorinlärd entitet tillämpas entiteten på yttrandena och visas i LUIS-portalen så snart den har skapats.

## <a name="train-the-app-before-testing-or-publishing"></a>Träna appen innan du testar eller publicerar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicera appen att fråga från slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av webbadressen i adressfältet och ersätt _YOUR_QUERY_HERE_ med:

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

* [Koncept - enheter](luis-concept-entity-types.md)
* [JSON-referens för reguljär uttrycksenhet](reference-entity-regular-expression.md?tabs=V3)
* [Så här lägger du till entiteter för att extrahera data](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en ny avsikt, lagt till exempelyttranden och sedan skapat en entitet för ett reguljärt uttryck för att extrahera välformade data från yttranden. När appen har tränats upp och publicerats identifierade en fråga till slutpunkten aktuell avsikt och extraherade data returnerades.

> [!div class="nextstepaction"]
> [Lär dig mer om listentiteten](tutorial-list-entity.md)

