---
title: Skapa en enkel app med två avsikter – Azure | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar du en enkel LUIS-app med två avsikter (inga entiteter) för att identifiera användaryttranden.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: fea311eb36c0654b45b2354cb283f1699be7257c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109349"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>Självstudie: 1. Skapa en app med en anpassad domän
I den här självstudien skapar du en app som visar hur du använder **avsikter** för att ta reda på användarens _avsikt_ baserat på yttrandet (texten) de skickar till appen. När du är klar har du en LUIS-slutpunkt som körs i molnet.

Den här appen är den enklaste typen av LUIS-app eftersom den inte extraherar data från yttranden. Den bestämmer bara användarens avsikt med yttrandet.

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa en ny app för en HR-domän (Human Resources) 
> * Lägga till avsikten GetJobInformation
> * Lägga till exempelyttranden i avsikten GetJobInformation 
> * Träna och publicera appen
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar
> * Lägga till avsikten ApplyForJob
> * Lägga till exempelyttranden i avsikten ApplyForJob 
> * Träna, publicera och fråga slutpunkten igen 

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto](luis-reference-regions.md#luis-website) för att kunna redigera LUIS-programmet.

## <a name="purpose-of-the-app"></a>Syftet med appen
Den här appen innehåller ett par avsikter. Den första avsikten, **`GetJobInformation`**, identifierar när en användare vill ha information om lediga tjänster i ett företag. Den andra avsikten, **`None`**, identifierar alla andra typer av yttranden. Senare i snabbstarten lägger vi till en tredje avsikt, `ApplyForJob`. 

## <a name="create-a-new-app"></a>Skapa en ny app
1. Logga in på [LUIS](luis-reference-regions.md#luis-website)-webbplatsen. Se till att logga in på den [region](luis-reference-regions.md#publishing-regions) där du behöver få LUIS-slutpunkterna publicerade.

2. På [LUIS](luis-reference-regions.md#luis-website)-webbplatsen väljer du **Create new app** (Skapa ny app).  

    [![](media/luis-quickstart-intents-only/app-list.png "Skärmbild av sidan Mina appar")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. I popup-dialogrutan anger du namnet `HumanResources`. Den här appen hanterar frågor om ditt företags HR-avdelning. Avdelningen hanterar anställningsfrågor, till exempel lediga tjänster i företaget.

    ![Ny LUIS-app](./media/luis-quickstart-intents-only/create-app.png)

4. När processen är klar visar appen sidan **Intents** (Avsikter) med avsikten **None** (Ingen). 

    [![](media/luis-quickstart-intents-only/intents-list.png "Skärmbild på sidan med en lista över avsikter")](media/luis-quickstart-intents-only/intents-list.png#lightbox)

## <a name="create-getjobinformation-intention"></a>Skapa avsikten GetJobInformation
1. Välj **Create new intent** (Skapa ny avsikt). Ange det nya avsiktsnamnet `GetJobInformation`. Den här avsikten förväntas varje gång en användare vill ha information om lediga tjänster på ditt företag.

    ![](media/luis-quickstart-intents-only/create-intent.png "Skärmbild på dialogrutan New intent (Ny avsikt)")

    Genom att skapa en avsikt skapar du en kategori för information som du vill identifiera. Tack vare att kategorin får ett namn kan andra program som använder LUIS-frågeresultaten använda det kategorinamnet för att hitta ett lämpligt svar. LUIS svarar inte på de här frågorna, utan identifierar bara vilken typ av information som efterfrågas på ett naturligt språk. 

2. Lägg till sju yttranden till avsikten som du förväntar dig att en användare begär, till exempel:

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

3. LUIS-appen har för närvarande inga yttranden för avsikten **None** (Ingen). Den måste ha yttranden som appen inte besvarar. Lämna den inte tom. Välj **Intents** (Avsikter) på den vänstra panelen. 

4. Välj avsikten **None** (Ingen). Lägg till tre yttranden som din användare kan tänkas ange men som inte är relevanta för appen. Exempel på bra yttranden för avsikten **None** (Ingen) om appen används för jobbannonser:

    | Exempel på yttranden|
    |--|
    |Skällande hundar är irriterande|
    |Beställ en pizza åt mig|
    |Pingviner i havet|

    I det LUIS-anropande programmet, till exempel en chattrobot, kan roboten, om LUIS returnerar avsikten **None** (Ingen) för ett yttrande, fråga om användaren vill avsluta konversationen. Chattroboten kan även ge fler anvisningar för att fortsätta konversationen om användaren inte vill avsluta den. 

## <a name="train-and-publish-the-app"></a>Träna och publicera appen
1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna). 

    ![Knappen Train (Träna)](./media/luis-quickstart-intents-only/train-button.png)

    Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Statusfält för avslutad träning](./media/luis-quickstart-intents-only/trained.png)

2. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera) för att öppna sidan Publish (Publicera). Produktionsplatsen väljs som standard. Välj knappen **Publish** (Publicera) bredvid valet av produktionsplats. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    Du behöver inte skapa en LUIS-slutpunktsnyckel i Azure-portalen innan du publicerar eller testar slutpunktens URL. Varje LUIS-app har en kostnadsfri startnyckel för redigering. Det ger dig obegränsad redigering och [några slutpunktsanrop](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-for-getjobinformation-intent"></a>Fråga slutpunkten om avsikten GetJobInformation
1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunktens URL i adressfältet. 

2. Gå till slutet av URL:en i adressen och ange `I'm looking for a job with Natual Language Processing`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som något av exempelyttrandena i steg 4. Därför är det ett bra test och bör returnera avsikten `GetJobInformation` som den avsikt som har högst poäng. 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
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

## <a name="create-applyforjob-intention"></a>Skapa avsikten ApplyForJob
Gå tillbaka till webbläsarfliken för LUIS-webbplatsen och skapa en ny avsikt för att söka ett jobb.

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

    [Träna och publicera](#train-and-publish-the-app) igen. 

## <a name="query-endpoint-for-applyforjob-intent"></a>Fråga slutpunkten om avsikten ApplyForJob
På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. I det nya webbläsarfönstret anger du `Can I submit my resume for job 235986` i slutet av URL:en. 

    ```
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

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Den här appen har med bara några få avsikter identifierat en fråga på ett naturligt språk som har samma avsikt, men som använder andra ord. 

JSON-resultatet identifierar avsikten med högst poäng. Alla poäng är mellan 1 och 0, ju närmare 1 desto bättre. Poängen för avsikterna `GetJobInformation` och `None` är mycket närmare noll. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan ta topScoringIntent-resultatet och antingen hitta information (som inte lagras i LUIS) för att besvara frågan eller avsluta konversationen. Det här är programmatiska alternativ för roboten eller det anropande programmet. LUIS utför inte det arbetet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Det gör du genom att välja **My apps** (Mina appar) på menyn längst upp till vänster. Välj menyn med tre punkter (...) till höger om appnamnet i applistan och välj **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till fördefinierade avsikter och entiteter i appen](luis-tutorial-prebuilt-intents-entities.md)
