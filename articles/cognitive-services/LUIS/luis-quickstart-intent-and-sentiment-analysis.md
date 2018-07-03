---
title: Självstudie om att skapa en LUIS-app som returnerar sentimentanalys – Azure | Microsoft Docs
description: I den här självstudien lägger du till sentimentanalys till din LUIS-app för att analysera yttranden för positiva, negativa och neutrala känslor.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: v-geberr
ms.openlocfilehash: ac959989dbe64460025bfba84df7b6f22c3c1c04
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958437"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Självstudie: skapa app som returnerar sentiment tillsammans med avsiktsförutsägelse
I den här självstudien skapar du en app som visar hur det går till att extrahera positiva, negativa och neutrala sentiment från yttranden.

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå en attitydanalys
> * Använda LUIS-appen i HR-domänen (Human Resources) 
> * Lägga till attitydanalys
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar 

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen Human Resources (Personalfrågor) från självstudien om [keyPhrase entities](luis-quickstart-intent-and-key-phrase.md) (keyPhrase-entiteter) ska du [importera](create-new-app.md#import-new-app) JSON till en ny app på [LUIS-webbplatsen](luis-reference-regions.md#luis-website). Importeringsappen finns på [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json)-GitHub-lagringsplatsen.

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
LUIS känner inte till den nya avsikten och dess exempelyttranden förrän den har tränats. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Skärmbild på markerad knapp Train (Träna)](./media/luis-quickstart-intent-and-sentiment-analysis/train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Skärmbild på meddelandefält om att Training (Träning) är klar ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-trained-inline.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurera appen för att inkludera sentimentanalys
Konfigurera attitydanalys på sidan **Publish** (Publicera). 

1. Välj **Publish** (Publicera) i det övre högra navigeringsfältet.

    ![Skärmbild på sidan Intent (Avsikt) med knappen Publish (Publicera) expanderad ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. Välj **Enable Sentiment Analysis** (Aktivera sentimentanalys). Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png "Skärmbild på sidan Publish (Publicera) med knappen Publish to production slot (Publicera till produktionsplats) markerad")](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png#lightbox)

4. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-an-utterance"></a>Skicka fråga till slutpunkten med ett yttrande

1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    ![Skärmbild på sidan Publish (Publicera) med slutpunkts-URL markerad](media/luis-quickstart-intent-and-sentiment-analysis/hr-endpoint-url-inline.png)

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
Ta bort LUIS-appen när den inte längre behövs. För att göra det väljer du menyn med tre punkter (...) till höger om appnamnet i applistan och väljer **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Anropa LUIS-endpoint-API med C#](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
