---
title: Självstudie om att skapa en LUIS-app som returnerar nyckelfraser – Azure | Microsoft Docs
description: I den här självstudien lägger du till och returnerar keyPhrase-entiteten i din LUIS-app för att analysera yttranden för nyckelämnen.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: 1cafca2433cd96c0595a6124df82856d0c491a49
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224270"
---
# <a name="tutorial-8-add-keyphrase-entity"></a>Självstudie: 8. Lägga till keyPhrase-entitet 
I den här självstudien använder du en app som visar hur det går till att extrahera nyckelämnen från yttranden.

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå keyPhrase-entiteter 
> * Använda LUIS-appen i HR-domänen (Human Resources) 
> * Lägga till keyPhrase-entitet för att extrahera innehåll från yttrande
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar inklusive nyckelfraser

För den här artikeln kan du använda ett kostnadsfritt [LUIS-konto](luis-reference-regions.md#publishing-regions) för att kunna redigera LUIS-programmet.

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen Human Resources (Personalfrågor) från självstudien om [simple entity](luis-quickstart-primary-and-secondary-data.md) (enkel entitet) ska du [importera](luis-how-to-start-new-app.md#import-new-app) JSON till en ny app på [LUIS-webbplatsen](luis-reference-regions.md#luis-website). Importeringsappen finns på [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json)-GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `keyphrase`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 

## <a name="keyphrase-entity-extraction"></a>Extrahering av keyPhrase-entitet
Nyckelämnen tillhandahålls av den fördefinierade entiteten **keyPhrase**. Den här entiteten returnerar nyckelämnen i yttrandet.

Följande yttranden visar exempel på nyckelfraser:

|Yttrande|keyPhrase-entitetsvärden|
|--|--|
|Kommer det att erbjudas en ny sjukvårdsplan med lägre självrisk nästa år?|”lägre självrisk”<br>”ny sjukvårdsplan”<br>”år”|
|Ingår synbehandling i sjukvårdsplanen med hög självrisk?|”sjukvårdsplan med hög självrisk”<br>”synbehandling”|

Ditt klientprogram kan använda dessa värden tillsammans med andra extraherade entiteter för att avgöra nästa steg i konversationen.

## <a name="add-keyphrase-entity"></a>Lägga till keyPhrase-entitet 
Lägg till den fördefinierade keyPhrase-entiteten för att extrahera ämnesinnehåll från yttranden.

1. Kontrollera att Human Resources-appen (Personalfrågor) finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

    [ ![Skärmbild på LUIS-app med Build (Skapa) markerat i navigeringsfältet längst upp till höger](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png)](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png#lightbox)

2. Välj **Entities** (Entiteter) på den vänstra menyn.

    [ ![Skärmbild på Entities (Entiteter) markerade i vänster navigeringsfält på avsnittet Build (Skapa)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. Välj **Manage prebuilt entities** (Hantera fördefinierade entiteter).

    [ ![Skärmbild på popup-dialogrutan Entities list (Lista över entiteter)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. I popup-dialogrutan väljer du **keyPhrase** och sedan **Done** (Klar). 

    [ ![Skärmbild på popup-dialogrutan Entities list (Lista över entiteter)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. Välj **Intents** (Avsikter) på menyn till vänster och välj avsikten **Utilities.Confirm**. Entiteten keyPhrase är märkt med flera yttranden. 

    [ ![Skärmbild på avsikten Utilities.Confirm med keyPhrases märkta i yttranden](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Träna LUIS-appen
Den nya `keyphrase`-versionen av appen behöver tränas.  

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Träna appen](./media/luis-quickstart-intent-and-key-phrase/train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Träningen är klar](./media/luis-quickstart-intent-and-key-phrase/trained.png)

## <a name="publish-app-to-endpoint"></a>Publicera app till slutpunkt

1. Välj **Publish** (Publicera) i det övre högra navigeringsfältet.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png "Skärmbild på sidan Publish (Publicera) med knappen Publish to production slot (Publicera till produktionsplats) markerad")](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png#lightbox)

2. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png "Skärmbild på sidan Publish (Publicera) med knappen Publish to production slot (Publicera till produktionsplats) markerad")](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png#lightbox)

3. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-an-utterance"></a>Skicka fråga till slutpunkten med ett yttrande

1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    ![Skärmbild på sidan Publish (Publicera) med slutpunkts-URL markerad](media/luis-quickstart-intent-and-key-phrase/hr-endpoint-url-inline.png )

2. Gå till slutet av URL:en i adressen och ange `does form hrf-123456 cover the new dental benefits and medical plan`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

```
{
  "query": "does form hrf-123456 cover the new dental benefits and medical plan",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9300641
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.9300641
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0359598845
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0141798034
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0112197418
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00507669244
    },
    {
      "intent": "None",
      "score": 0.00238501839
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00202810857
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00102957746
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0008688423
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.557994E-05
    }
  ],
  "entities": [
    {
      "entity": "hrf-123456",
      "type": "HRF-number",git 
      "startIndex": 10,
      "endIndex": 19
    },
    {
      "entity": "new dental benefits",
      "type": "builtin.keyPhrase",
      "startIndex": 31,
      "endIndex": 49
    },
    {
      "entity": "medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 55,
      "endIndex": 66
    },
    {
      "entity": "hrf",
      "type": "builtin.keyPhrase",
      "startIndex": 10,
      "endIndex": 12
    },
    {
      "entity": "-123456",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 19,
      "resolution": {
        "value": "-123456"
      }
    }
  ]
}
```

Användaren som sökte efter ett formulär angav mer information än vad som var nödvändigt för att hitta det. Den ytterligare informationen returneras som **builtin.keyPhrase**. Klientprogrammet kan använda den ytterligare informationen till följdfrågor som ”Vill du tala med en representant på personalavdelningen om den nya tandvårdsförsäkringen?”, eller för att uppge en meny med fler alternativ som ”Mer information om den nya tandvårds- eller sjukvårdsförsäkringen”.

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Med keyPhrase-entitetsidentifiering identifierade den här appen en frågeavsikt i naturligt språk och returnerade extraherade data, inklusive nyckelämnet. 

Din chattrobot har nu tillräckligt med information för att avgöra nästa steg i konversationen. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och keyPhrase-data från yttrandet för att gå vidare. LUIS utför inte detta programmässiga arbete för roboten eller det anropande programmet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Välja **My apps** (Mina appar) på menyn längst upp till vänster. Välj ellipsknappen (***...***) till höger om appnamnet i applistan och väljer **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till sentimentanalys i appen](luis-quickstart-intent-and-sentiment-analysis.md)