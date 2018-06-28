---
title: Självstudie om att skapa en LUIS-app som returnerar sentimentanalys – Azure | Microsoft Docs
description: I den här självstudien lägger du till sentimentanalys till din LUIS-app för att analysera yttranden för positiva, negativa och neutrala känslor.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265342"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Självstudie: skapa app som returnerar sentiment tillsammans med avsiktsförutsägelse
I den här självstudien skapar du en app som visar hur det går till att extrahera positiva, negativa och neutrala sentiment från yttranden.

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå hierarkiska entiteter och underordnade element med kontextuell inlärning 
> * Skapa ny LUIS-app för resedomän med avsikten Bookflight
> * Lägga till avsikten _None_ (Ingen) och lägga till exempelyttranden
> * Lägg till platshierarkisk entitet med ursprung och underordnade destinationselement
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar inklusive hierarkiska underordnade element 

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="sentiment-analysis"></a>Sentimentanalys
Sentimentanalys är förmågan att avgöra om en användares yttrande är positivt, negativt eller neutralt. 

Följande yttranden visar exempel på sentiment:

|Sentiment och poäng|Yttrande|
|:--|--|
|positivt – 0,89 |Soppan och salladen var toppen.|
|negativt – 0,07 |Jag gillade inte förrätten under middagsservicen.|

Sentimentanalys är en appinställning som tillämpas på alla yttranden. Du behöver inte hitta de ord som indikerar sentiment i yttrandet och märka dem. LUIS gör det åt dig.

## <a name="create-a-new-app"></a>Skapa en ny app
1. Logga in på [LUIS-webbplatsen][LUIS]. Se till att logga in på den [region][LUIS-regions] där du behöver få LUIS-slutpunkterna publicerade.

2. På [LUIS-webbplatsen][LUIS] väljer du **Create new app** (Skapa ny app). 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "Skärmbild på sidan App lists (App-listor)")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. I dialogrutan **Create new app** (Skapa ny app) ger du appen namnet `Restaurant Reservations With Sentiment` och väljer **Done** (Klar). 

    ![Bild på dialogrutan Create new app (Skapa ny app)](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    När processen för att skapa appen är klar visar LUIS listan över avsikter som innehåller avsikten None (Ingen).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "Skärmbild på sidan Intents lists (Lista över avsikter)")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>Lägga till en fördefinierad domän
Lägg till en fördefinierad domän för att snabbt lägga till avsikter, entiteter och märkta yttranden.

1. Välj **Prebuilt Domains** (Fördefinierade domäner) på den vänstra menyn.

    [ ![Skärmbild på knappen Prebuilt Domain (Fördefinierad domän)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. Välj **Add domain** (Lägg till domän) för den fördefinierade domänen **RestaurantReservation**. Vänta tills domänen har lagts till.

    [ ![Skärmbild på Prebuilt Domain list (Lista över fördefinierade domäner)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. Välj **Intents** (Avsikter) i det vänstra navigeringsfältet. Den här fördefinierade domänen har en avsikt.

    [ ![Skärmbild på Prebuilt domain list (Lista över fördefinierade domäner) med Intents (Avsikter) markerade i det vänstra navigeringsfältet](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  Välj avsikten **RestaurantReservation.Reserve**. 

    [ ![Skärmbild på Intents list (Lista över avsikter) med RestaurantReservation.Reserve markerat](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. Växla **Entities View** (Entitetsvy) för att se de många yttranden som medföljer de märkta domänspecifika entiteterna.

    [ ![Skärmbild på avsikten RestaurantReservation.Reserve med Entities View (Entitetsvy) växlad för att markera vyn Token](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till ändringarna av avsikterna och entiteterna (modellen) förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Skärmbild på markerad knapp Train (Träna)](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Skärmbild på meddelandefält om att Training (Träning) är klar ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurera appen för att inkludera sentimentanalys
Sentimentanalys är aktiverat på sidan **Publish** (Publicera). 

1. Välj **Publish** (Publicera) i det övre högra navigeringsfältet.

    ![Skärmbild på sidan Intent (Avsikt) med knappen Publish (Publicera) expanderad ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. Välj **Enable Sentiment Analysis** (Aktivera sentimentanalys).

    ![Skärmbild på sidan Publish (Publicera) med Enable Sentiment Analysis (Aktivera sentimentanalys) aktiverat ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "Skärmbild på sidan Publish (Publicera) med knappen Publish to production slot (Publicera till produktionsplats) markerad")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-an-utterance"></a>Skicka fråga till slutpunkten med ett yttrande

1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    ![Skärmbild på sidan Publish (Publicera) med slutpunkts-URL markerad](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. Gå till slutet av URL:en i adressen och ange `Reserve table for  10 on upper level away from kitchen`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `RestaurantReservation.Reserve` med sentimentanalysen extraherad.

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

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
