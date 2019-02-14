---
title: Förutsäga avsikter
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en anpassad app som förutspår en användares avsikt. Den här appen är den enklaste typen av LUIS-app eftersom den inte extraherar olika dataelement från yttranden, som e-postadresser eller datum.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: b4ac0ccb249bac7149014861056c10f9093d6759
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878176"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Självstudier: Skapa en LUIS-app som identifierar användarens avsikter

I den här självstudien skapar du en anpassad Human Resources-app som förutspår vad en användare vill baserat på yttrandet (text). 

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
|ApplyForJob|Avgör om användaren söker ett jobb.|
|GetJobInformation|Avgör om användaren letar efter information om jobb allmänt eller ett specifikt jobb.|
|Ingen|Avgör om användaren frågar om något som appen inte är avsedd att besvara. Den här avsikten tillhandahålls som en del av att skapa appen och kan inte tas bort. |

## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Skapa avsikt för jobbinformation

1. Välj **Create new intent** (Skapa ny avsikt). Ange det nya avsiktsnamnet `GetJobInformation`. Den här avsikten förväntas när en användare vill ha information om lediga tjänster på företaget. 

    ![Skärmbild av Language Understanding (LUIS), dialogrutan Ny avsikt](media/luis-quickstart-intents-only/create-intent.png "Skärmbild av Language Understanding (LUIS), dialogrutan Ny avsikt")

1. Välj **Done** (Klar).

2. Lägg till flera exempelyttranden till den här avsikten som du förväntar dig att en användare kan fråga:

    | Exempel på yttranden|
    |--|
    |Har nya jobb publicerats i dag?|
    |Finns det lediga tjänster på Seattle-kontoret?|
    |Finns det några lediga distansjobb för tekniker?|
    |Finns det jobb med databaser?|
    |Jag letar efter ett jobb på tampa-kontoret.|
    |Finns det någon ledig praktikplats på san francisco-kontoret?|
    |Finns det några deltidstjänster för studerande vid universitetet?|
    |Söker efter ett nytt arbete inom redovisning|
    |Letar efter ett jobb i new york för tvåspråkiga.|
    |Söker efter ett nytt arbete inom redovisning.|
    |Nya jobb?|
    |Visa mig alla jobb för tekniker som har lagts till de 2 senaste dagarna.|
    |Dagens jobbannonser?|
    |Vilka redovisningstjänster är lediga på london-kontoret?|
    |Finns det lediga tjänster för erfarna tekniker?|
    |Var finns jobblistorna|

    [![Skärmbild av att ange nya yttranden för MyStore-avsikter](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Skärmbild av att ange nya yttranden för MyStore-avsikter")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

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

    I JSON-resultatet identifieras avsikten med högst poäng i egenskapen **`topScoringIntent`**. Alla poäng är mellan 1 och 0, ju närmare 1 desto bättre. 

## <a name="create-intent-for-job-applications"></a>Skapa avsikt för jobbansökningar

Gå tillbaka till LUIS-portalen och skapa en ny avsikt för att avgöra om användarens yttrande handlar om att söka ett jobb.

1. Välj **Build** (Skapa) på menyn längst upp till höger för att återgå till sidan för att skapa appen.

1. Välj **Avsikter** på den vänstra menyn för att få en lista över avsikter.

1. Välj **Create new intent** (Skapa ny avsikt) och ange namnet `ApplyForJob`. 

    ![LUIS-dialogrutan för att skapa en ny avsikt](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Lägg till flera yttranden till den här avsikten som du förväntar dig att en användare begär, till exempel:

    | Exempel på yttranden|
    |--|
    |Fyll i ansökan till jobbet 123456|
    |Här är mitt cv för tjänsten 654234|
    |Här är mitt CV för deltidstjänsten som receptionist.|
    |Jag söker tjänsten på reklamavdelningen med dessa dokument.|
    |Jag söker sommarpraktikplatsen på forskning och utveckling i San Diego|
    |Jag vill lämna in mitt CV för den tillfälliga tjänsten i kafeterian.|
    |Jag skickar mitt CV för det nya Autocar-teamet i Columbus, OH|
    |Jag vill söka det nya jobbet inom redovisning|
    |Dokumenten för jobb 456789, praktikplatsen på redovisningsavdelningen, är här|
    |Jobbet 567890 och mina dokument|
    |Jag bifogar mina dokument för redovisningspraktikplatsen i tulsa.|
    |Mina dokument för helgleveranstjänsten|
    |Skicka mitt CV för det nya redovisningsjobbet i seattle|
    |Skicka cv för teknikertjänsten|
    |Jag skickar härmed mitt cv för tjänsten 234123 i Tampa.|

    [![Skärmbild av att ange nya yttranden för ApplyForJob-avsikter](media/luis-quickstart-intents-only/utterance-applyforjob.png "Skärmbild av att ange nya yttranden för ApplyForJob-avsikter")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    Den märkta avsikten är markerad i rött eftersom LUIS för närvarande är osäker på om avsikten är korrekt. Genom träning av appen informeras LUIS om att yttrandena gäller rätt avsikt. 

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
