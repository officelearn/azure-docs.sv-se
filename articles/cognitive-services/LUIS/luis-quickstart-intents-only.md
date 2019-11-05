---
title: 'Självstudie: förutsäga-LUIS'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en anpassad app som förutspår en användares avsikt. Den här appen är den enklaste typen av LUIS-app eftersom den inte extraherar olika dataelement från yttranden, som e-postadresser eller datum.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 83ecf0767f2b21065c698421e3ad8f07f31d5b16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465287"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Självstudie: Bygg LUIS-app för att fastställa användar avsikter

I den här självstudien skapar du en anpassad Human Resources-app som förutspår vad en användare vill baserat på yttrandet (text). 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en ny app 
> * Skapa avsikter
> * Lägga till exempelyttranden
> * Träna appen
> * Publicera app
> * Hämta avsikter från en slutpunkt


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Användares syften som avsikter

Syftet med appen är att avgöra syftet med en konversationsbaserad text på naturligt språk: 

`Are there any new positions in the Seattle office?`

Dessa syften är indelade i **avsikter**. 

Den här appen innehåller ett par avsikter. 

|Avsikt|Syfte|
|--|--|
|`ApplyForJob`|Avgör om användaren söker ett jobb.|
|`GetJobInformation`|Avgör om användaren letar efter information om jobb allmänt eller ett specifikt jobb.|
|`None`|Avgör om användaren frågar om något som appen inte är avsedd att besvara. Den här avsikten tillhandahålls som en del av att skapa appen och kan inte tas bort. |

## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Skapa avsikt för jobbinformation

1. Välj **Create new intent** (Skapa ny avsikt). Ange det nya avsiktsnamnet `GetJobInformation`. Den här avsikten förväntas när en användare vill ha information om lediga tjänster på företaget. 

    ![Skärm bild av Language Understanding (LUIS) ny avsikts dialog ruta](media/luis-quickstart-intents-only/create-intent.png "Skärm bild av Language Understanding (LUIS) ny avsikts dialog ruta")

1. Välj **Done** (Klar).

2. Lägg till flera exempelyttranden till den här avsikten som du förväntar dig att en användare kan fråga:

    | Exempel på yttranden|
    |--|
    |`Any new jobs posted today?`|
    |`Are there any new positions in the Seattle office?`|
    |`Are there any remote worker or telecommute jobs open for engineers?`|
    |`Is there any work with databases?`|
    |`I'm looking for a co-working situation in the tampa office.`|
    |`Is there an internship in the san francisco office?`|
    |`Is there any part-time work for people in college?`|
    |`Looking for a new situation with responsibilities in accounting`|
    |`Looking for a job in new york city for bilingual speakers.`|
    |`Looking for a new situation with responsibilities in accounting.`|
    |`New jobs?`|
    |`Show me all the jobs for engineers that were added in the last 2 days.`|
    |`Today's job postings?`|
    |`What accounting positions are open in the london office?`|
    |`What positions are available for Senior Engineers?`|
    |`Where is the job listings`|

    [![Skärm bild av att ange nya yttranden för avsikten med mittArkiv](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Skärm bild av att ange nya yttranden för avsikten med mittArkiv")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Genom att tillhandahålla _exempelyttranden_ tränar du LUIS i vilka typer av yttranden som ska förväntas i samband med den här avsikten. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Lägg till exempel på yttranden i avsikten Ingen 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Träna appen innan du testar eller publicerar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicera appen att fråga från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Hämta avsiktsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Gå till slutet av webbadressen i adressfältet och ange `I'm looking for a job with Natural Language Processing`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte identiskt med något av exempelyttrandena. Det är ett bra test och bör returnera avsikten `GetJobInformation` som avsikten med högst poäng. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    Frågesträngsparametern `verbose=true` innebär att inkludera **alla avsikter** i appens frågeresultat. Entitetsmatrisen är tom eftersom appen för närvarande inte har några entiteter. 

    I JSON-resultatet identifieras avsikten med högst poäng i egenskapen **`topScoringIntent`** . Alla poäng är mellan 1 och 0, ju närmare 1 desto bättre. 

## <a name="create-intent-for-job-applications"></a>Skapa avsikt för jobbansökningar

Gå tillbaka till LUIS-portalen och skapa en ny avsikt för att avgöra om användarens yttrande handlar om att söka ett jobb.

1. Välj **Build** (Skapa) på menyn längst upp till höger för att återgå till sidan för att skapa appen.

1. Välj **Avsikter** på den vänstra menyn för att få en lista över avsikter.

1. Välj **Create new intent** (Skapa ny avsikt) och ange namnet `ApplyForJob`. 

    ![LUIS-dialogrutan för att skapa en ny avsikt](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Lägg till flera yttranden till den här avsikten som du förväntar dig att en användare begär, till exempel:

    | Exempel på yttranden|
    |--|
    |`Fill out application for Job 123456`|
    |`Here is my c.v. for position 654234`|
    |`Here is my resume for the part-time receptionist post.`|
    |`I'm applying for the art desk job with this paperwork.`|
    |`I'm applying for the summer college internship in Research and Development in San Diego`|
    |`I'm requesting to submit my resume to the temporary position in the cafeteria.`|
    |`I'm submitting my resume for the new Autocar team in Columbus, OH`|
    |`I want to apply for the new accounting job`|
    |`Job 456789 accounting internship paperwork is here`|
    |`Job 567890 and my paperwork`|
    |`My papers for the tulsa accounting internship are attached.`|
    |`My paperwork for the holiday delivery position`|
    |`Please send my resume for the new accounting job in seattle`|
    |`Submit resume for engineering position`|
    |`This is my c.v. for post 234123 in Tampa.`|


## <a name="train-again"></a>Träna igen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Publicera igen

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Hämta avsiktsförutsägelse igen

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. I det nya webbläsarfönstret anger du `Can I submit my resume for job 235986` i slutet av URL:en. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    Resultatet innehåller den nya avsikten **ApplyForJob** samt befintliga avsikter. 

## <a name="client-application-next-steps"></a>Nästa steg för klientprogrammet

När LUIS returnerar JSON-svaret är LUIS färdig med förfrågningen. LUIS svarar inte på användarnas yttranden utan identifierar bara vilken typ av information som efterfrågas på det naturliga språket. Konversationsuppföljningen tillhandahålls av klientprogram, till exempel en Azure-robot. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Typer av entiteter](luis-concept-entity-types.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat appen Human Resources, skapat 2 avsikter, lagt till exempelyttranden för varje avsikt, lagt till exempelyttranden för avsikten Ingen samt tränat upp, publicerat och testat vid slutpunkten. Det här är de grundläggande stegen i att skapa en LUIS-modell. 

Fortsätt med den här appen och [lägg till en enkel entitet och fraslista](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Lägga till fördefinierade avsikter och entiteter i appen](luis-tutorial-prebuilt-intents-entities.md)
