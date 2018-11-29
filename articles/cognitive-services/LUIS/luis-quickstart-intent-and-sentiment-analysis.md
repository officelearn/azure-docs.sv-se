---
title: 'Självstudie 9: Sentimentanalys med positiva, negativa och neutrala sentiment i LUIS'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en app som visar hur det går till att extrahera positiva, negativa och neutrala sentiment från yttranden. Sentimenten bestäms utifrån hela yttrandet.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 60c4b280033e110f6b8b2a3ce720934e118c8479
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424858"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>Självstudie 9: Extrahera sentiment från ett helt yttrande
I den här självstudien skapar du en app som visar hur det går till att extrahera positiva, negativa och neutrala sentiment från yttranden. Sentimenten bestäms utifrån hela yttrandet.

Sentimentanalys är förmågan att avgöra om en användares yttrande är positivt, negativt eller neutralt. 

Följande yttranden visar exempel på sentiment:

|Sentiment|Poäng|Yttrande|
|:--|:--|:--|
|positivt|0,91 |John W. Smith gjorde ett riktigt bra jobb med presentationen i Paris.|
|positivt|0,84 |jill-jones@mycompany.com gjorde ett fantastiskt jobb med säljpresentationen till Parker.|

Sentimentanalys är en publiceringsinställning som tillämpas på alla yttranden. Du behöver inte hitta orden som beskriver sentiment i yttrandet och märka dem eftersom attitydanalysen omfattar hela yttrandet. 

Eftersom det är en publiceringsinställning ser du den inte på sidor för avsikter eller entiteter. Du kan se den i fönstret med [interaktiva test](luis-interactive-test.md#view-sentiment-results) och när du testar vid slutpunktens webbadress. 

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Använda en befintlig självstudieapp 
> * Lägga till sentimentanalys som en publiceringsinställning
> * Träna
> * Publicera
> * Extrahera sentiment från yttranden vid en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använda en befintlig app

Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Om du inte har appen HumanResources från föregående självstudie gör du så här:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `sentiment`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="employeefeedback-intent"></a>Avsikter i EmployeeFeedback 
Lägg till en ny avsikt för att samla in feedback om medarbetare från kollegor inom företaget. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Create new intent** (Skapa ny avsikt).

3. Ge den nya avsikten namnet `EmployeeFeedback`.

    ![Dialogrutan Create new intent (Skapa ny avsikt) med EmployeeFeedback som namn](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Lägg till flera yttranden som beskriver att en medarbetare gjort något bra eller att något behöver förbättras:

    Kom ihåg att medarbetare i den här appen Human Resources (Personalfrågor) definieras i listentiteten `Employee` med namn, e-postadress, telefonanknytning, mobilnummer och amerikanskt socialförsäkringsnummer. 

    |Yttranden|
    |--|
    |425-555-1212 gjorde ett bra jobb med att välkomna tillbaka en medarbetare som haft mammaledigt.|
    |234-56-7891 tröstade en medarbetare i sorg.|
    |jill-jones@mycompany.com hade inte alla fakturorna som krävdes för pappersarbetet.|
    |john.w.smith@mycompany.com lämnade in de obligatoriska formulären en månad för sent, och utan signaturer.|
    |x23456 kom inte till det viktiga externa marknadsföringsmötet.|
    |x12345 missade mötet för juni-genomgången.|
    |Jill Jones gjorde ett fantastiskt jobb med säljpresentationen på Harvard.|
    |John W. Smith gjorde ett riktigt bra jobb med presentationen på Stanford.|

    [ ![Skärmbild på LUIS-appen med exempelyttranden i avsikten EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurera appen för att inkludera sentimentanalys
1. Välj **Hantera** i det övre högra navigeringsfältet och välj sedan **Publiceringsinställningar** i den vänstra menyn.

2. Ändra reglaget **Attitydanalys** för att aktivera den här inställningen. 

    ![](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>Extrahera sentiment från yttranden vid en slutpunkt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `Jill Jones work with the media team on the public portal was amazing`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `EmployeeFeedback` med sentimentanalysen extraherad.
    
    ```JSON
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.4983256
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.4983256
        },
        {
          "intent": "MoveEmployee",
          "score": 0.06617523
        },
        {
          "intent": "GetJobInformation",
          "score": 0.04631853
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0103248553
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.007531875
        },
        {
          "intent": "FindForm",
          "score": 0.00344597152
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00337914471
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0026357458
        },
        {
          "intent": "None",
          "score": 0.00214573368
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00157622492
        },
        {
          "intent": "Utilities.Confirm",
          "score": 7.379545E-05
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 0,
          "endIndex": 9,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "media team",
          "type": "builtin.keyPhrase",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "public portal",
          "type": "builtin.keyPhrase",
          "startIndex": 43,
          "endIndex": 55
        },
        {
          "entity": "jill jones",
          "type": "builtin.keyPhrase",
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

    sentimentAnalysis är positiv med ett poängresultat på 0,86. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg
I den här självstudien lägger du till sentimentanalys som en publiceringsinställning för att extrahera sentimentvärden från ett helt uttryck.

> [!div class="nextstepaction"] 
> [Granska slutpunktsyttranden i HR-appen](luis-tutorial-review-endpoint-utterances.md) 

