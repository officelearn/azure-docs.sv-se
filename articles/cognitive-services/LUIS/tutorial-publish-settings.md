---
title: 'Självstudiekurs: Publiceringsinställningar - LUIS'
titleSuffix: Azure Cognitive Services
description: I den här självstudien ändrar du publiceringsinställningarna för att få bättre förutsägelser.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: diberry
ms.openlocfilehash: 2df32c20bebf4243f383a0cccd8f037721533602
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75890394"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Självstudiekurs: Lägga till sentimentanalys som publiceringsinställning

I den här självstudien ändrar du publiceringsinställningarna för att extrahera sentimentanalys och fråga sedan LUIS-slutpunkten för att se den returnerade känslan för ett användarutseende.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Lägga till sentimentanalys som en publiceringsinställning
> * Få sentimentet för ett uttryck från den publicerade slutpunkten

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Attitydanalys är en publiceringsinställning

Följande yttranden visar exempel på sentiment:

|Sentiment|Poäng|Yttrande|
|:--|:--|:--|
|negativt|0,01 |Pizzan var hemsk.|
|positivt|0.97 |Ostpizzan var underbar.|

Sentimentanalys är en publiceringsinställning som tillämpas på alla yttranden. När du har angett det returnerar appen känslan av ett uttryck utan att du behöver märka data.

Eftersom det är en publiceringsinställning visas den inte på avsikter eller entitetssidor. Du kan se den i fönstret med [interaktiva test](luis-interactive-test.md#view-sentiment-results) och när du testar vid slutpunktens webbadress.

## <a name="import-example-json-to-begin-app"></a>Importera exempel .json för att starta appen

1.  Ladda ner och spara [appen JSON-fil](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Träna appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurera appen för att inkludera sentimentanalys

1. Välj **Publicera** på den övre menyn. Sentimentanalys är en publiceringsinställning.

1. Välj **Produktionsplats** och välj sedan **Ändra inställningar**.
1. Ange inställningen Sentimentanalys **på På**.

    ![Aktivera Attitydanalys som en publiceringsinställning](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Hämta attityden för ett yttrandet från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av webbadressen i adressen och ange följande uttryck:

    `Deliver 2 of the best cheese pizzas ever!!!`

    Den senaste frågesträngparametern är `query`, uttrycksfrågan . **query** Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `OrderPizza` med sentimentanalysen extraherad.

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

    Sentimentanalysen är positiv med poängen 86 procent.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* Attitydanalys tillhandahålls av Cognitive Service [Textanalys](../Text-Analytics/index.yml). Funktionen är begränsad till [språk som stöds](luis-language-support.md#languages-supported) av Textanalys.
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)


## <a name="next-steps"></a>Nästa steg
I den här självstudien lägger du till sentimentanalys som en publiceringsinställning för att extrahera sentimentvärden från ett helt uttryck.

> [!div class="nextstepaction"]
> [Granska slutpunktsyttranden i HR-appen](luis-tutorial-review-endpoint-utterances.md)

