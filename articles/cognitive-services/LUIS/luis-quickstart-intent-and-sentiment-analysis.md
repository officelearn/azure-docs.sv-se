---
title: Sentimentanalys
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en app som visar hur det går till att hämta positiva, negativa och neutrala attityder från yttranden. Sentimenten bestäms utifrån hela yttrandet.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0e61f6a914c33842f4f42b2e1e4206b370a11dd4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099043"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Självstudier:  Hämta attityder för yttranden

I den här självstudien skapar du en app som visar hur det går till att fastställa positiva, negativa och neutrala attityder från yttranden. Sentimenten bestäms utifrån hela yttrandet.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa en ny app
> * Lägga till sentimentanalys som en publiceringsinställning
> * Träna appen
> * Publicera app
> * Extrahera sentiment från yttranden vid en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Attitydanalys är en publiceringsinställning

Följande yttranden visar exempel på sentiment:

|Sentiment|Poäng|Yttrande|
|:--|:--|:--|
|positivt|0,91 |John W. Smith gjorde ett riktigt bra jobb med presentationen i Paris.|
|positivt|0,84 |Seattle-teknikerna gjorde ett fantastiskt jobb på Parker-säljpresentationen.|

Sentimentanalys är en publiceringsinställning som tillämpas på alla yttranden. Du behöver inte hitta de ord som indikerar attityden i yttrandet och märka dem. 

Eftersom det är en publiceringsinställning ser du den inte på sidor för avsikter eller entiteter. Du kan se den i fönstret med [interaktiva test](luis-interactive-test.md#view-sentiment-results) och när du testar vid slutpunktens webbadress. 


## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Lägg till den fördefinierade entiteten PersonName 


1. Välj **Entities** (Entiteter) på den vänstra navigeringsmenyn.

1. Välj knappen för att **lägga till en fördefinierad entitet**.

1. Välj följande entitet från listan över fördefinierade entiteter och välj sedan **Klart**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Skärmbild på dialogrutan för fördefinierade entiteter med nummer markerat](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Skapa en avsikt för att fastställa feedback om medarbetare

Lägg till en ny avsikt för att samla in feedback om medarbetare från kollegor inom företaget. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Create new intent** (Skapa ny avsikt).

3. Ge den nya avsikten namnet `EmployeeFeedback`.

    ![Dialogrutan Create new intent (Skapa ny avsikt) med EmployeeFeedback som namn](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Lägg till flera yttranden som beskriver att en medarbetare gjort något bra eller att något behöver förbättras:

    |Yttranden|
    |--|
    |John Smith gjorde ett bra jobb med att välkomna tillbaka en medarbetare som haft mammaledigt|
    |Jill Jones tröstade en medarbetare i sorg.|
    |Bob Barnes hade inte alla fakturorna som krävdes för pappersarbetet.|
    |Todd Thomas lämnade in de obligatoriska formulären en månad för sent, och utan signaturer|
    |Katherine Kelly kom inte till det viktiga externa marknadsföringsmötet.|
    |Denise Dillard missade mötet för junigenomgången.|
    |Mark Mathews gjorde ett fantastiskt jobb med säljpresentationen på Harvard|
    |Walter Williams gjorde ett riktigt bra jobb med presentationen på Stanford|

    [![Skärmbild på LUIS-appen med exempelyttranden i avsikten EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Lägga till exempelyttranden i avsikten Ingen 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att ändringar av avsikten kan testas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurera appen för att inkludera sentimentanalys

1. Välj **Hantera** i det övre högra navigeringsfältet och välj sedan **Publiceringsinställningar** i den vänstra menyn.

1. Ändra reglaget **Attitydanalys** för att aktivera den här inställningen. 

    ![Aktivera Attitydanalys som en publiceringsinställning](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras på den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Hämta attityden för ett yttrandet från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Gå till slutet av URL:en i adressen och ange `Jill Jones work with the media team on the public portal was amazing`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `EmployeeFeedback` med sentimentanalysen extraherad.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    sentimentAnalysis är positiv med ett poängresultat på 86 %. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* Attitydanalys tillhandahålls av Cognitive Service [Textanalys](../Text-Analytics/index.yml). Funktionen är begränsad till [språk som stöds](luis-language-support.md##languages-supported) av Textanalys.
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)


## <a name="next-steps"></a>Nästa steg
I den här självstudien lägger du till sentimentanalys som en publiceringsinställning för att extrahera sentimentvärden från ett helt uttryck.

> [!div class="nextstepaction"] 
> [Granska slutpunktsyttranden i HR-appen](luis-tutorial-review-endpoint-utterances.md) 

