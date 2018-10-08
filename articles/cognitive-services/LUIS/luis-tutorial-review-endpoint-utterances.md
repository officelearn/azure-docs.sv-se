---
title: 'Självstudie 1: Granska yttranden vid slutpunkter med aktiv inlärning'
titleSuffix: Azure Cognitive Services
description: Förbättra förutsägelserna i dina appar genom att verifiera eller korrigera yttranden som tas emot via HTTP-slutpunkten för LUIS och som LUIS inte kan fastställa säkert. I vissa yttranden kan avsikten behöva verifieras och i vissa kan du behöva verifiera entiteter. Du bör granska yttranden vid slutpunkter inom ramen för det schemalagda underhållet av LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 1047c117228b57f7361a1e386bc6cde7acbfdde8
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042286"
---
# <a name="tutorial-1-fix-unsure-predictions"></a>Självstudie 1: Åtgärda osäkra förutsägelser
I den här självstudien kommer vi att förbättra förutsägelserna i dina appar genom att verifiera eller korrigera yttranden som tas emot via HTTP-slutpunkten för LUIS och som LUIS inte kan fastställa säkert. I vissa yttranden kan avsikten behöva verifieras och i vissa kan du behöva verifiera entiteter. Du bör granska yttranden vid slutpunkter inom ramen för det schemalagda underhållet av LUIS. 

Den här granskningsprocessen är ett annat sätt för LUIS för att lära sig din appdomän. LUIS valde yttrandena som visas i granskningslistan. Följande gäller för listan:

* Den är specifik för appen.
* Den är avsedd att förbättra noggrannheten hos appens förutsägelse. 
* Den bör granskas regelbundet. 

Genom att granska slutpunktsyttranden verifierar eller korrigerar du det yttrandets förutsagda avsikt. Du märker även ut anpassade entiteter som inte förutsagts eller som förutsagts felaktigt. 

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Använda en befintlig självstudieapp
> * Granska slutpunktsyttranden
> * Uppdatera fraslistan
> * Träna appen
> * Publicera appen
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använda en befintlig app

Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Om du inte har appen HumanResources från föregående självstudie gör du så här:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `review`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

    Om du använder den här självstudien som en ny, importerad app måste du även träna, publicera och sedan lägga till yttrandena till slutpunkten med ett [skript](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) eller från slutpunkten i en webbläsare. De yttranden som ska läggas till är:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Om du har alla versioner av appen kan det verka förvånande att i serien med självstudier se att listan över **yttranden för slutpunktsgranskning** inte ändras, baserat på version. Det finns en enda pool med yttranden att granska, oavsett vilken version du aktivt redigerar eller vilken version av appen som publicerades vid slutpunkten. 

## <a name="review-endpoint-utterances"></a>Granska slutpunktsyttranden

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Granska slutpunktsyttranden** i det vänstra navigeringsfönstret. Listan filtreras efter avsikten **ApplyForJob**. 

    [ ![Skärmbild av knappen Granska slutpunktsyttranden i det vänstra navigeringsfönstret](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

3. Växla **entitetsvyn** för att visa de märkta entiteterna. 
    
    [ ![Skärmbild av Granska slutpunktsyttranden med växeln Entitetsvy markerad](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)

    |Yttrande|Rätt avsikt|Saknade entiteter|
    |:--|:--|:--|
    |Jag letar efter ett jobb med naturlig språkbearbetning|GetJobInfo|Jobb – ”Natural Language Process”|

    Den här yttrandet är inte i rätt avsikt och har en poäng som är mindre än 50 %. Avsikten **ApplyForJob** har 21 yttranden jämfört med 7 yttranden i **GetJobInformation**. Utöver korrekt justering av slutpunktsyttrandena bör fler yttranden läggas till i avsikten **GetJobInformation**. Det kvarstår som en övning som du kan slutföra på egen hand. Varje avsikt, förutom avsikten **Ingen**, bör ha ungefär samma antal exempelyttranden. Avsikten **Ingen** bör ha 10 % av de totala yttrandena i appen. 

4. För avsikten `I'm looking for a job with Natual Language Processing` väljer du rätt avsikt, **GetJobInformation**, i kolumnen **Aligned intent** (Justerad avsikt). 

    [ ![Skärmbild av Granska slutpunktsyttranden som justerar yttranden till avsikt](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

5. I samma uttryck är entiteten för `Natural Language Processing` keyPhrase. Detta bör vara en **Job**-entitet (Jobb) i stället. Välj `Natural Language Processing` och välj sedan entiteten **Job** (Jobb) från listan.

    [ ![Skärmbild av Granska slutpunktsyttranden som märker entitet i yttrande](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

6. På samma rad väljer du den inringade markeringen i kolumnen **Add to aligned intent** (Lägg till i justerad avsikt). 

    [ ![Skärmbild av slutförande av yttrandejustering i avsikt](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Den här åtgärden flyttar yttrandet från **yttrandena för slutpunktsgranskning** till avsikten **GetJobInformation**. Slutpunktsyttrandet är nu ett exempelyttrande för den avsikten. 

7. Granska de återstående yttrandena i den här avsikten, och märk yttranden och korrigera den **justerade avsikten** om dessa är felaktiga.

8. När alla yttranden är korrekta markerar du kryssrutan på varje rad och väljer sedan **Add selected** (Lägg till valda) för att justera yttrandena korrekt. 

    [ ![Skärmbild av slutförande av återstående yttranden till justerad avsikt](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

9. Listan bör inte längre ha dessa yttranden. Om du ser flera yttranden fortsätter du att gå igenom listan, korrigera avsikter och märka ut eventuella saknade entiteter tills listan är tom. 

10. Välj nästa avsikt i listan Filter och fortsätt sedan korrigera yttranden och märka entiteter. Kom ihåg att det sista steget för varje avsikt är att antingen välja **Add to aligned intent** (Lägg till i justerad avsikt) på yttrandets rad eller markera kryssrutan bredvid varje avsikt och välja **Add selected** (Lägg till valda) ovanför tabellen.

    Fortsätt tills alla avsikter och entiteter i filterlistan har en tom lista. Det här är en mycket liten app. Granskningsprocessen tar bara några minuter. 

## <a name="update-phrase-list"></a>Uppdatera fraslistan
Håll fraslistan uppdaterad med nyligen identifierade jobbnamn. 

1. Välj **Fraslistor** i det vänstra navigeringsfönstret.

2. Välj fraslistan **Jobs** (Jobb).

3. Lägg till `Natural Language Processing` som ett värde och välj sedan **Spara**. 

## <a name="train"></a>Träna

LUIS känner inte till ändringarna förrän den tränas. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

Om du har importerat den här appen måste du välja **Sentimentanalys**.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

Prova ett yttrande som liknar det korrigerade yttrandet. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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
Vissa av slutpunktsyttrandena har en hög förutsägelsepoäng i granskningslistan. Du behöver ändå granska och kontrollera de yttrandena. De finns med i listan eftersom nästföljande högsta avsikt hade en poäng som var för nära avsikten med högst poäng. Du vill ha en skillnad på ungefär 15 % mellan de två översta avsikterna.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du granskat yttranden som skickats vid slutpunkten och som LUIS inte kunnat tolka säkert. När de här yttrandena har verifierats och flyttas till rätt avsikter som exempelyttranden blir förutsägelserna i LUIS bättre.

> [!div class="nextstepaction"]
> [Lär dig hur du använder mönster](luis-tutorial-pattern.md)
