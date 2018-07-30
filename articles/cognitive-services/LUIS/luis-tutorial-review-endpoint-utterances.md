---
title: Självstudie om att granska slutpunktsyttranden i Language Understanding (LUIS) – Azure | Microsoft Docs
description: I den här självstudien lär du dig hur du granskar slutpunktsyttranden i HR-domänen i LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: diberry
ms.openlocfilehash: 1f1e3310e0d02983aaecc3f87ba9c116d65b751b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237221"
---
# <a name="tutorial-review-endpoint-utterances"></a>Självstudie: Granska slutpunktsyttranden
I den här självstudien förbättrar du appförutsägelser genom att verifiera eller korrigera yttranden som tas emot via LUIS HTTP-slutpunkt. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå granskning av slutpunktsyttranden 
> * Använda LUIS-appen för HR-domänen 
> * Granska slutpunktsyttranden
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto](luis-reference-regions.md#luis-website) för att kunna redigera LUIS-programmet.

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har HR-appen från självstudien om [sentiment](luis-quickstart-intent-and-sentiment-analysis.md) importerar du appen från Github-lagringsplatsen för [LUIS-exempel](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-sentiment-HumanResources.json). Om du använder den här självstudien som en ny, importerad app måste du även träna, publicera och sedan lägga till yttrandena till slutpunkten med ett [skript](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) eller från slutpunkten i en webbläsare. De yttranden som ska läggas till är:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `review`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 

Om du har alla versioner av appen kan det verka förvånande att i serien med självstudier se att listan över **yttranden för slutpunktsgranskning** inte ändras, baserat på version. Det finns en enstaka pool med yttranden att granska, oavsett versionen av de yttranden du aktivt redigerar eller versionen av den app som publicerades på slutpunkten. 

## <a name="purpose-of-reviewing-endpoint-utterances"></a>Syftet med att granska slutpunktsyttranden
Den här granskningsprocessen är ett annat sätt för LUIS för att lära sig din appdomän. LUIS valde yttrandena i granskningslistan. Följande gäller för listan:

* Den är specifik för appen.
* Den är avsedd att förbättra noggrannheten hos appens förutsägelse. 
* Den bör granskas regelbundet. 

Genom att granska slutpunktsyttranden verifierar eller korrigerar du det yttrandets förutsagda avsikt. Du märker även anpassade entiteter som inte förutsades. 

## <a name="review-endpoint-utterances"></a>Granska slutpunktsyttranden

1. Kontrollera att Human Resources-appen (Personalfrågor) finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

    [ ![Skärmbild på LUIS-appen med Build (Skapa) markerat i navigeringsfältet längst upp till höger](./media/luis-tutorial-review-endpoint-utterances/first-image.png)](./media/luis-tutorial-review-endpoint-utterances/first-image.png#lightbox)

1. Välj **Granska slutpunktsyttranden** i det vänstra navigeringsfönstret. Listan filtreras efter avsikten **ApplyForJob**. 

    [ ![Skärmbild av knappen Granska slutpunktsyttranden i det vänstra navigeringsfönstret](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png)](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png#lightbox)

2. Växla **entitetsvyn** för att visa de märkta entiteterna. 
    
    [ ![Skärmbild av Granska slutpunktsyttranden med växeln Entitetsvy markerad](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png#lightbox)

    |Yttrande|Rätt avsikt|Saknade entiteter|
    |:--|:--|:--|
    |Jag letar efter ett jobb med naturlig språkbearbetning|GetJobInfo|Jobb – ”Natural Language Process”|

    Den här yttrandet är inte i rätt avsikt och har en poäng som är mindre än 50 %. Avsikten **ApplyForJob** har 21 yttranden jämfört med 7 yttranden i **GetJobInformation**. Utöver korrekt justering av slutpunktsyttrandena bör fler yttranden läggas till i avsikten **GetJobInformation**. Det kvarstår som en övning som du kan slutföra på egen hand. Varje avsikt, förutom avsikten **Ingen**, bör ha ungefär samma antal exempelyttranden. Avsikten **Ingen** bör ha 10 % av de totala yttrandena i appen. 

    När du är i **Tokens View** (Tokenvy) kan du hovra över blå text för yttranden för att se det förutsedda entitetsnamnet. 

3. För avsikten `I'm looking for a job with Natual Language Processing` väljer du rätt avsikt, **GetJobInformation**, i kolumnen **Aligned intent** (Justerad avsikt). 

    [ ![Skärmbild av Granska slutpunktsyttranden som justerar yttranden till avsikt](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

4. I samma uttryck är entiteten för `Natural Language Processing` keyPhrase. Detta bör vara en **Job**-entitet (Jobb) i stället. Välj `Natural Language Processing` och välj sedan entiteten **Job** (Jobb) från listan.

    [ ![Skärmbild av Granska slutpunktsyttranden som märker entitet i yttrande](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

5. På samma rad väljer du den inringade markeringen i kolumnen **Add to aligned intent** (Lägg till i justerad avsikt). 

    [ ![Skärmbild av slutförande av yttrandejustering i avsikt](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Den här åtgärden flyttar yttrandet från **yttrandena för slutpunktsgranskning** till avsikten **GetJobInformation**. Slutpunktsyttrandet är nu ett exempelyttrande för den avsikten. 

6. Granska de återstående yttrandena i den här avsikten, och märk yttranden och korrigera den **justerade avsikten** om dessa är felaktiga.

7. När alla yttranden är korrekta markerar du kryssrutan på varje rad och väljer sedan **Add selected** (Lägg till valda) för att justera yttrandena korrekt. 

    [ ![Skärmbild av slutförande av återstående yttranden till justerad avsikt](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

8. Listan bör inte längre ha dessa yttranden. Om flera yttranden visas kan du fortsätta att gå igenom listan, korrigera avsikter och märka eventuella saknade entiteter tills listan är tom. Välj nästa avsikt i listan Filter och fortsätt sedan korrigera yttranden och märka entiteter. Kom ihåg att det sista steget för varje avsikt är att antingen välja **Add to aligned intent** (Lägg till i justerad avsikt) på yttrandets rad eller markera kryssrutan bredvid varje avsikt och välja **Add selected** (Lägg till valda) ovanför tabellen. 

    Det här är en mycket liten app. Granskningsprocessen tar bara några minuter.

## <a name="add-new-job-name-to-phrase-list"></a>Lägga till nytt jobbnamn till fraslistan
Håll fraslistan uppdaterad med nyligen identifierade jobbnamn. 

1. Välj **Fraslistor** i det vänstra navigeringsfönstret.

2. Välj fraslistan **Jobs** (Jobb).

3. Lägg till `Natural Language Processing` som ett värde och välj sedan **Spara**. 

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till ändringarna förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL
För att få en den uppdaterade modellen av LUIS-appen i en chattrobot eller i ett annat program måste du publicera appen. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera). 

2. Om du har importerat den här appen måste du välja **Sentimentanalys**. 

3. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

4. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-an-utterance"></a>Skicka fråga till slutpunkten med ett yttrande
Prova ett yttrande som liknar det korrigerade yttrandet. 

1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunktens URL i adressfältet. 

2. Gå till slutet av URL:en i adressen och ange `Are there any natural language processing jobs in my department right now?`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

```JSON
{
  "query": "are there any natural language processing jobs in my department right now?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.9247605
  },
  "intents": [
    {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    {
      "intent": "ApplyForJob",
      "score": 0.129989788
    },
    {
      "intent": "FindForm",
      "score": 0.006438211
    },
    {
      "intent": "EmployeeFeedback",
      "score": 0.00408575451
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00194211153
    },
    {
      "intent": "None",
      "score": 0.00166400627
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00118593348
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0007885918
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0006373631
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0005980781
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.719905E-05
    }
  ],
  "entities": [
    {
      "entity": "right now",
      "type": "builtin.datetimeV2.datetime",
      "startIndex": 64,
      "endIndex": 72,
      "resolution": {
        "values": [
          {
            "timex": "PRESENT_REF",
            "type": "datetime",
            "value": "2018-07-05 15:23:18"
          }
        ]
      }
    },
    {
      "entity": "natural language processing",
      "type": "Job",
      "startIndex": 14,
      "endIndex": 40,
      "score": 0.9869922
    },
    {
      "entity": "natural language processing jobs",
      "type": "builtin.keyPhrase",
      "startIndex": 14,
      "endIndex": 45
    },
    {
      "entity": "department",
      "type": "builtin.keyPhrase",
      "startIndex": 53,
      "endIndex": 62
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8251864
  }
}
}
```

Den korrekta avsikten förutsades med en hög poäng och entiteten **Job** (Jobb) identifieras som `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Går det att ersätta granskning genom att lägga till fler yttranden? 
Du undrar kanske varför man inte bara kan lägga till fler exempelyttranden. Vad är syftet med att granska slutpunktsyttranden? I en riktig LUIS-app kommer slutpunktsyttranden från användare med ordval och ordningsföljd som du inte har använt än. Om du hade använt samma ordval och ordningsföljd skulle den ursprungliga förutsägelsen ha haft ett högre procenttal. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Varför är den högsta avsikten på yttrandelistan? 
Några av slutpunktsyttrandena har ett högt procenttal granskningslistan. Du behöver ändå granska och kontrollera de yttrandena. De finns med i listan eftersom nästföljande högsta avsikt hade en poäng som var för nära avsikten med högst poäng. 

## <a name="what-has-this-tutorial-accomplished"></a>Vad har den här självstudien åstadkommit?
Den här appens förutsägelsenoggrannhet har ökat genom granskning av yttranden från slutpunkten. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Välja **My apps** (Mina appar) på menyn längst upp till vänster. Välj ellipsen **...** till höger om appnamnet i applistan och välj **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder mönster](luis-tutorial-pattern.md)
