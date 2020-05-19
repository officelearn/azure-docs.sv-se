---
title: 'Självstudie: publicerings inställningar – LUIS'
description: I den här självstudien ändrar du publicerings inställningarna för att få bättre förutsägelser.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: d976d06fbf20ea0e64fa01bd94658a27d680bc86
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588807"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Självstudie: Lägg till sentiment-analys som en publicerings inställning

I den här självstudien ändrar du publicerings inställningarna för att extrahera sentiment-analys och frågar sedan LUIS-slutpunkten för att se returnerad sentiment för en användare uttryck.

**I de här självstudierna får du lära dig att**

<!-- green checkmark -->
> [!div class="checklist"]
> * Lägg till sentiment-analys som en publicerings inställning
> * Hämta sentiment för en uttryck från den publicerade slut punkten

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Attitydanalys är en publiceringsinställning

Följande yttranden visar exempel på sentiment:

|Sentiment|Poäng|Yttrande|
|:--|:--|:--|
|negativt|0,01 |Pizza var Awful.|
|positivt|0,97 |Ost-pizza var fantastiskt.|

Sentimentanalys är en publiceringsinställning som tillämpas på alla yttranden. När appen har angetts returnerar appen sentiment av en uttryck utan att du behöver ha etiketter på data.

Eftersom det är en publicerings inställning ser du inte att den är märkt på sidorna för avsikter eller entiteter. Du kan se den i fönstret med [interaktiva test](luis-interactive-test.md#view-sentiment-results) och när du testar vid slutpunktens webbadress.

## <a name="download-json-file-for-app"></a>Ladda ned JSON-fil för appen

Ladda ned och spara [app-JSON-filen](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

## <a name="import-json-file-for-app"></a>Importera JSON-fil för appen

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Träna appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurera appen för att inkludera sentimentanalys

1. Välj **publicera** på den översta menyn. Sentiment-analys är en publicerings inställning.

1. Välj **produktions plats** och välj sedan **ändra inställningar**.
1. Ställ in Attitydanalys **på på**.

    ![Aktivera Attitydanalys som en publiceringsinställning](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Hämta attityden för ett yttrandet från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av webb adressen i adress fältet och Ersätt _YOUR_QUERY_HERE_ med:

    `Deliver 2 of the best cheese pizzas ever!!!`

    Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `OrderPizza` med sentimentanalysen extraherad.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
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
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    Sentiment-analysen är positiv med en poäng på 86%.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* Attitydanalys tillhandahålls av Cognitive Service [Textanalys](../Text-Analytics/index.yml). Funktionen är begränsad till [språk som stöds](luis-language-support.md#languages-supported) av Textanalys.
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)


## <a name="next-steps"></a>Nästa steg
I den här självstudien lägger du till sentimentanalys som en publiceringsinställning för att extrahera sentimentvärden från ett helt uttryck.

> [!div class="nextstepaction"]
> [Granska slutpunktsyttranden i HR-appen](luis-tutorial-review-endpoint-utterances.md)

