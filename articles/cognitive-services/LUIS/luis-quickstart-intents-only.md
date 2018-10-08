---
title: 'Självstudie 1: Hitta avsikter i anpassade LUIS-appar'
titleSuffix: Azure Cognitive Services
description: Skapa en anpassad app som förutspår en användares avsikt. Den här appen är den enklaste typen av LUIS-app eftersom den inte extraherar olika dataelement från yttranden, som e-postadresser eller datum.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b229dbc90f3f6ecc226c88ee393114f233bcf1a2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035417"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>Självstudie 1: Skapa en anpassad app som identifierar vad en användare vill

I den här självstudien skapar du en anpassad Human Resources-app som förutspår vad en användare vill baserat på yttrandet (text). När du är klar har du en LUIS-slutpunkt som körs i molnet.

Syftet med appen är att avgöra syftet med en konversationsbaserad text på naturligt språk. Dessa syften är indelade i **avsikter**. Den här appen innehåller ett par avsikter. Den första avsikten, **`GetJobInformation`**, identifierar när en användare vill ha information om lediga tjänster i ett företag. Den andra avsikten **`None`** används för yttranden från användaren som ligger utanför appens _domän_ (omfattning). Senare läggs en tredje avsikt, **`ApplyForJob`**, till för yttranden om att söka ett jobb. Den här tredje avsikten skiljer sig från `GetJobInformation` eftersom jobbinformationen redan bör vara känd när någon söker jobbet. Beroende på ordval kan det dock vara svårt att avgöra vad avsikten är eftersom båda handlar om ett jobb.

När LUIS returnerar JSON-svaret är LUIS färdig med förfrågningen. LUIS svarar inte på användarnas yttranden utan identifierar bara vilken typ av information som efterfrågas på det naturliga språket. 

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en ny app 
> * Skapa avsikter
> * Lägga till exempelyttranden
> * Träna appen
> * Publicera app
> * Hämta avsikter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Skapa en ny app

1. Logga in på LUIS-portalen med webbadressen [https://www.luis.ai](https://www.luis.ai). 

2. Välj **Create new app** (Skapa ny app).  

    [![](media/luis-quickstart-intents-only/app-list.png "Skärmbild av LUIS-sidan Mina appar")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. I dialogrutan anger du namnet `HumanResources` och behåller standardkulturen **Engelska**. Lämna beskrivningen tom.

    ![Ny LUIS-app](./media/luis-quickstart-intents-only/create-app.png)

    Appen visar sedan sidan **Avsikter** med avsikten **Ingen**.

## <a name="getjobinformation-intent"></a>Avsikten GetJobInformation

1. Välj **Create new intent** (Skapa ny avsikt). Ange det nya avsiktsnamnet `GetJobInformation`. Den här avsikten förväntas varje gång en användare vill ha information om lediga tjänster på företaget.

    ![](media/luis-quickstart-intents-only/create-intent.png "Skärmbild av LUIS-sidan Ny avsikt")

2. Genom att tillhandahålla _exempelyttranden_ tränar du LUIS i vilka typer av yttranden som ska förväntas i samband med den här avsikten. Lägg till flera exempelyttranden till den här avsikten som du förväntar dig att en användare kan fråga, till exempel:

    | Exempel på yttranden|
    |--|
    |Har nya jobb publicerats i dag?|
    |Finns det lediga tjänster för erfarna tekniker?|
    |Finns det jobb med databaser?|
    |Söker efter ett nytt arbete inom redovisning|
    |Var finns jobblistorna|
    |Nya jobb?|
    |Finns det lediga tjänster på Seattle-kontoret?|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Skärmbild av hur du anger nya yttranden för avsikten MyStore (MittArkiv)")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>Avsikten Ingen 
Klientprogrammet behöver veta om ett yttrande ligger utanför appens domän. Om LUIS returnerar avsikten **Ingen** för ett yttrande kan klientprogrammet fråga om användaren vill avsluta konversationen. Klientprogrammet kan även ge fler anvisningar för att fortsätta konversationen om användaren inte vill avsluta den. 

Dessa exempelyttranden, som ligger utanför ämnesområdet, samlas under avsikten **Ingen**. Lämna den inte tom. 

1. Välj **Intents** (Avsikter) på den vänstra panelen.

2. Välj avsikten **None** (Ingen). Lägg till tre yttranden som användarna kan tänkas ange men som inte är relevanta för Human Resources-appen. Exempel på yttranden för avsikten **Ingen** om appen används för jobbannonser:

    | Exempel på yttranden|
    |--|
    |Skällande hundar är irriterande|
    |Beställ en pizza åt mig|
    |Pingviner i havet|


## <a name="train"></a>Träna 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>Hämta avsikt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av webbadressen i adressfältet och ange `I'm looking for a job with Natural Language Processing`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte identiskt med något av exempelyttrandena. Det är ett bra test och bör returnera avsikten `GetJobInformation` som avsikten med högst poäng. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

    Resultatet innehåller **alla avsikter** i appen, för närvarande 2. Entitetsmatrisen är tom eftersom appen för närvarande inte har några entiteter. 

    I JSON-resultatet identifieras avsikten med högst poäng i egenskapen **`topScoringIntent`**. Alla poäng är mellan 1 och 0, ju närmare 1 desto bättre. 

## <a name="applyforjob-intent"></a>Avsikten ApplyForJob
Gå tillbaka till LUIS-webbplatsen och skapa en ny avsikt för att avgöra om användarens yttrande handlar om att söka ett jobb.

1. Välj **Build** (Skapa) på menyn längst upp till höger för att återgå till sidan för att skapa appen.

2. Välj **Intents** (Avsikter) på den vänstra menyn.

3. Välj **Create new intent** (Skapa ny avsikt) och ange namnet `ApplyForJob`. 

    ![LUIS-dialogrutan för att skapa en ny avsikt](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. Lägg till flera yttranden till den här avsikten som du förväntar dig att en användare begär, till exempel:

    | Exempel på yttranden|
    |--|
    |Jag vill söka det nya jobbet inom redovisning|
    |Fyll i ansökan till jobbet 123456|
    |Skicka cv för teknikertjänsten|
    |Här är mitt cv för tjänsten 654234|
    |Jobbet 567890 och mina dokument|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "Skärmbild av hur du anger nya yttranden för avsikten ApplyForJob")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    Den märkta avsikten är markerad i rött eftersom LUIS för närvarande är osäker på om avsikten är korrekt. Genom träning av appen informeras LUIS om att yttrandena gäller rätt avsikt. 

## <a name="train-again"></a>Träna igen

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Publicera igen

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>Hämta avsikt igen

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. I det nya webbläsarfönstret anger du `Can I submit my resume for job 235986` i slutet av URL:en. 

    ```JSON
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

    Resultatet innehåller den nya avsikten **ApplyForJob** samt befintliga avsikter. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat appen Human Resources, skapat 2 avsikter, lagt till exempelyttranden för varje avsikt, lagt till exempelyttranden för avsikten Ingen samt tränat upp, publicerat och testat vid slutpunkten. Det här är de grundläggande stegen i att skapa en LUIS-modell. 

> [!div class="nextstepaction"]
> [Lägga till fördefinierade avsikter och entiteter i appen](luis-tutorial-prebuilt-intents-entities.md)
