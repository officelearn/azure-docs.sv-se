---
title: Entitet med reguljärt uttryck
titleSuffix: Azure Cognitive Services
description: Extrahera data med ett enhetligt format från ett yttrande med entiteten för reguljära uttryck.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 012d77227e7c0283b565361dda32cb77e12fc62b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867277"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Självstudier: Hämta välformaterade data från yttrandet
I den här självstudien skapar du en app för att extrahera konsekvent formaterade data från ett yttrande med hjälp av entiteten **Reguljärt uttryck**.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa en ny app 
> * Lägga till avsikt
> * Lägg till entitet för reguljära uttryck 
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Lägga till entiteter för reguljära uttryck

I den här appen används entiteten för reguljära uttryck till att hämta välformaterade HR-formulärnummer (Human Resources) från ett yttrande. Även om avsikten med yttrandet alltid fastställs med maskininlärning är just den här entitetstypen inte maskininlärd. 

**Här är några exempelyttranden:**

|Exempel på yttranden|
|--|
|Var finns HRF-123456?|
|Vem skapade HRF-123234?|
|Har HRF-456098 publicerats på franska?|
|HRF-456098|
|Datum för HRF-456098?|
 
Ett reguljärt uttryck passar bra för den här typen av data i följande fall:

* data är välformade.


## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Skapa avsikt för att hitta formulär

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Välj **Create new intent** (Skapa ny avsikt). 

1. Ange `FindForm` i popup-dialogrutan och välj sedan **Done** (Klar). 

    ![Skärmbild på dialogrutan Create new intent (Skapa ny avsikt) med Utilities (Verktyg) i sökrutan](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Vad är URL för hrf-123456?|
    |Var är hrf-345678?|
    |När uppdaterades hrf-456098?|
    |Uppdaterade Johan Svensson hrf-234639 förra veckan?|
    |Hur många versioner av hrf-345123 finns det?|
    |Vem behöver godkänna formuläret hrf-123456?|
    |Hur många personer måste signera hrf-345678?|
    |datum för hrf-234123?|
    |författare av hrf-546234?|
    |rubrik för hrf-456234?|

    [![Skärmbild på sidan Intent (Avsikt) med nya talindata markerade](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Använda entiteten för reguljära uttryck för välformaterade data
Entiteten för reguljära uttryck för att matcha formulärnumret är `hrf-[0-9]{6}`. Den här reguljära uttrycket matchar de exakta tecknen `hrf-` men ignorerar konjugationer och kulturella varianter. Det matchar siffrorna 0–9 för exakt 6 siffror.

HRF står för `human resources form`.

LUIS tokeniserar yttrandet när det läggs till i en avsikt. Tokeniseringen för de här yttrandena lägger till blanksteg före och efter bindestrecket, `Where is HRF - 123456?`  Det reguljära uttrycket tillämpas på yttrandet i obearbetat format, innan det tokeniseras. Eftersom det tillämpas i _obearbetat_ format behöver det reguljära uttrycket inte hantera ordgränser. 

Skapa en entitet för reguljära uttryck för att ange för LUIS vad ett HRF-nummerformat är med följande steg:

1. Välj **Entities** (Entiteter) på den vänstra panelen.

1. Välj knappen **Create new entity** (Skapa ny entitet) på sidan Entities (Entiteter). 

1. Ange det nya entitetsnamnet `HRF-number` i dialogrutan, välj **RegEx** som entitetstyp, ange `hrf-[0-9]{6}` som **Regex**-värde och välj sedan **Klar**.

    ![Skärmbild på inställning för popup-dialogruta med egenskaper för ny entitet](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Välj **Avsikter** i menyn till vänster och sedan avsikten **FindForm** för att se det reguljära uttrycket uppmärkt i yttrandena. 

    [![Skärmbild på yttrandet Label (Etikett) med befintlig entitet och regexmönster](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Eftersom entiteten inte är en maskininlärd entitet tillämpas entiteten på yttrandena och visas på LUIS-webbplatsen så snart den skapas.

## <a name="add-example-utterances-to-the-none-intent"></a>Lägg till exempelyttranden till avsikten Ingen 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Träna appen innan du testar eller publicerar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicera appen att fråga från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `When were HRF-123456 and hrf-234567 published in the last year?`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `FindForm` med de två formulärnumren `HRF-123456` och `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    När en entitet för reguljära uttryck används extraherar LUIS namngivna data, vilket är programmässigt mer användbart för det klientprogram som tar emot JSON-svaret.


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* Begrepp för entitet för [reguljära uttryck](luis-concept-entity-types.md#regular-expression-entity)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en ny avsikt, lagt till exempelyttranden och sedan skapat en entitet för ett reguljärt uttryck för att extrahera välformade data från yttranden. När appen har tränats upp och publicerats identifierade en fråga till slutpunkten aktuell avsikt och extraherade data returnerades.

> [!div class="nextstepaction"]
> [Lär dig mer om listentiteten](luis-quickstart-intent-and-list-entity.md)

