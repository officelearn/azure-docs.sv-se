---
title: Självstudie om att skapa en LUIS-app som returnerar nyckelfraser – Azure | Microsoft Docs
description: I den här självstudien lägger du till och returnerar keyPhrase-entiteten i din LUIS-app för att analysera yttranden för nyckelämnen.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264623"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>Självstudie: skapa app som returnerar keyPhrases-entitetsdata som hittas i yttranden
I den här självstudien skapar du en app som visar hur det går till att extrahera nyckelämnen från yttranden.

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå keyPhrase-entiteter 
> * Skapa ny LUIS-app för personalfrågedomän
> * Lägga till avsikten _None_ (Ingen) och lägga till exempelyttranden
> * Lägga till keyPhrase-entitet för att extrahera innehåll från yttrande
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

För den här artikeln kan du använda ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="keyphrase-entity-extraction"></a>Extrahering av keyPhrase-entitet
Nyckelämnen tillhandahålls av den fördefinierade entiteten **keyPhrase**. Den här entiteten returnerar nyckelämnen i yttrandet

Följande yttranden visar exempel på nyckelfraser:

|Yttrande|keyPhrase-entitetsvärden|
|--|--|
|Kommer det att erbjudas en ny sjukvårdsplan med lägre självrisk nästa år?|”lägre självrisk”<br>”ny sjukvårdsplan”<br>”år”|
|Ingår synbehandling i sjukvårdsplanen med hög självrisk?|”sjukvårdsplan med hög självrisk”<br>”synbehandling”|

Din chattrobot kan överväga de här värdena, utöver alla de andra entiteter som extraheras, när den bestämmer nästa steg i konversationen.

## <a name="download-sample-app"></a>Ladda ned exempelapp
Ladda ned appen [Human Resources](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) (Personalfrågor) och spara den till en fil med filnamnstillägget *.json. Den här exempelappen identifierar yttranden som är relevanta för de anställdas förmåner, organisationsscheman och fysiska tillgångar.

## <a name="create-a-new-app"></a>Skapa en ny app
1. Logga in på [LUIS-webbplatsen][LUIS]. Se till att logga in på den [region][LUIS-regions] där du behöver få LUIS-slutpunkterna publicerade.

2. På [LUIS-webbplatsen][LUIS] väljer du **Import new app** (Importera ny app) för att importera appen Human Resources (Personalfrågor) som laddades ned i föregående avsnitt. 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "Skärmbild på sidan App lists (App-listor)")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. I dialogrutan **Import new app** (Importera ny app) ger du appen namnet `Human Resources with Key Phrase entity`. 

    ![Bild på dialogrutan Create new app (Skapa ny app)](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    När processen för att skapa appen är klar visar LUIS listan över avsikter.

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "Skärmbild på sidan Intents lists (Lista över avsikter)")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>Lägga till keyPhrase-entitet 
Lägg till den fördefinierade keyPhrase-entiteten för att extrahera ämnesinnehåll från yttranden.

1. Välj **Entities** (Entiteter) på den vänstra menyn.

    [ ![Skärmbild på Entities (Entiteter) markerade i vänster navigeringsfält på avsnittet Build (Skapa)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. Välj **Manage prebuilt entities** (Hantera fördefinierade entiteter).

    [ ![Skärmbild på popup-dialogrutan Entities list (Lista över entiteter)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. I popup-dialogrutan väljer du **keyPhrase** och sedan **Done** (Klar). 

    [ ![Skärmbild på popup-dialogrutan Entities list (Lista över entiteter)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till den här ändringen av modellen förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Skärmbild på markerad knapp Train (Träna)](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Skärmbild på meddelandefält om att Training (Träning) är klar ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>Publicera app till slutpunkt

1. Välj **Publish** (Publicera) i det övre högra navigeringsfältet.

    ![Skärmbild på sidan Entity (Entitet) med knappen Publish (Publicera) expanderad ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "Skärmbild på sidan Publish (Publicera) med knappen Publish to production slot (Publicera till produktionsplats) markerad")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-an-utterance"></a>Skicka fråga till slutpunkten med ett yttrande

1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    ![Skärmbild på sidan Publish (Publicera) med slutpunkts-URL markerad](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. Gå till slutet av URL:en i adressen och ange `Is there a new medical plan with a lower deductible offered next year?`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Med keyPhrase-entitetsidentifiering identifierade den här appen en frågeavsikt i naturligt språk och returnerade extraherade data, inklusive nyckelämnet. 

Din chattrobot har nu tillräckligt med information för att avgöra nästa steg i konversationen. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och keyPhrase-data från yttrandet för att gå vidare. LUIS utför inte detta programmässiga arbete för roboten eller det anropande programmet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. För att göra det väljer du menyn med tre punkter (...) till höger om appnamnet i applistan och väljer **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa app som returnerar sentiment tillsammans med avsiktsförutsägelse](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
