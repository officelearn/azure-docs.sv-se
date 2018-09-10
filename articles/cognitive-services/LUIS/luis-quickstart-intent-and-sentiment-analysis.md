---
title: Självstudie om att skapa en LUIS-app som returnerar sentimentanalys – Azure | Microsoft Docs
description: I den här självstudien lägger du till sentimentanalys till din LUIS-app för att analysera yttranden för positiva, negativa och neutrala känslor.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: a89755bcc0ed5ef8bee4ed00b99c73993a57bcb9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163030"
---
# <a name="tutorial-9--add-sentiment-analysis"></a>Självstudie: 9.  Lägga till attitydanalys
I den här självstudien skapar du en app som visar hur det går till att extrahera positiva, negativa och neutrala sentiment från yttranden.

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå en attitydanalys
> * Använda LUIS-appen i HR-domänen (Human Resources) 
> * Lägga till attitydanalys
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar 

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen Human Resources (Personalfrågor) från självstudien om [den fördefinierade keyPhrase-entiteten](luis-quickstart-intent-and-key-phrase.md) ska du [importera](luis-how-to-start-new-app.md#import-new-app) JSON till en ny app på [LUIS-webbplatsen](luis-reference-regions.md#luis-website). Importeringsappen finns på [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json)-GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `sentiment`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 

## <a name="sentiment-analysis"></a>Sentimentanalys
Sentimentanalys är förmågan att avgöra om en användares yttrande är positivt, negativt eller neutralt. 

Följande yttranden visar exempel på sentiment:

|Sentiment|Poäng|Yttrande|
|:--|:--|:--|
|positivt|0,91 |John W. Smith gjorde ett riktigt bra jobb med presentationen i Paris.|
|positivt|0,84 |jill-jones@mycompany.com gjorde ett fantastiskt jobb med säljpresentationen till Parker.|

Attitydanalys är en appinställning som tillämpas på alla yttranden. Du behöver inte hitta orden som beskriver sentiment i yttrandet och märka dem eftersom attitydanalysen omfattar hela yttrandet. 

## <a name="add-employeefeedback-intent"></a>Lägga till avsikten EmployeeFeedback 
Lägg till en ny avsikt för att samla in feedback om medarbetare från kollegor inom företaget. 

1. Kontrollera att Human Resources-appen (Personalfrågor) finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

    [ ![Skärmbild på LUIS-app med Build (Skapa) markerat i navigeringsfältet längst upp till höger](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. Välj **Create new intent** (Skapa ny avsikt).

    [ ![Skärmbild på LUIS-app med Build (Skapa) markerat i navigeringsfältet längst upp till höger](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

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

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurera appen för att inkludera sentimentanalys
Konfigurera attitydanalys på sidan **Publish** (Publicera). 

1. Välj **Publish** (Publicera) i det övre högra navigeringsfältet.

    ![Skärmbild på sidan Intent (Avsikt) med knappen Publish (Publicera) expanderad ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. Välj **Enable Sentiment Analysis** (Aktivera sentimentanalys). 

## <a name="publish-app-to-endpoint"></a>Publicera app till slutpunkt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-an-utterance"></a>Skicka fråga till slutpunkten med ett yttrande

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `Jill Jones work with the media team on the public portal was amazing`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `EmployeeFeedback` med sentimentanalysen extraherad.

```
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

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Med sentimentanalys aktiverat identifierade den här appen en frågeavsikt i naturligt språk och returnerade extraherade data, inklusive det totala sentimentet som poäng. 

Din chattrobot har nu tillräckligt med information för att avgöra nästa steg i konversationen. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och sentimentdata från yttrandet för att gå vidare. LUIS utför inte detta programmässiga arbete för roboten eller det anropande programmet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Granska slutpunktsyttranden i HR-appen](luis-tutorial-review-endpoint-utterances.md) 

