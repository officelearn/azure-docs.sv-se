---
title: Använd batch testning för att förbättra LUIS förutsägelser | Microsoft Docs
titleSuffix: Azure
description: Läsa in batch test, granska resultaten och förbättra LUIS förutsägelser med ändringar.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266404"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Använd batch testning för att hitta prognosens noggrannhet problem

Den här kursen visar hur du använder batch testa för att hitta utterance förutsägelse problem.  

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
* Skapa en batchfil test 
* Kör ett batch-test
* Granska testresultaten
* Åtgärda felen för avsikter
* Testa om gruppen

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * För den här artikeln får du också ha en [LUIS][LUIS] konto för att kunna redigera LUIS programmet.

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera för en [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Skapa ny app
Den här artikeln använder fördefinierade HomeAutomation-domän. Fördefinierade domänen har avsikter, enheter och utterances för att styra HomeAutomation enheter, till exempel ljus. Skapa appen, lägga till domänen, träna och publicera.

1. I den [LUIS] webbplatsen, skapa en ny app genom att välja **Skapa ny app** på den **MyApps** sidan. 

    ![Skapa ny app](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Ange namnet `Batchtest-HomeAutomation` i dialogrutan.

    ![Ange appens namn](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Välj **färdiga domäner** i nedre vänstra hörnet. 

    ![Välj färdiga domän](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Välj **Lägg till domän** för HomeAutomation.

    ![Lägg till HomeAutomation domän](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Välj **Train** i det övre högra navigeringsfältet.

    ![Knappen Välj Train](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Villkor för batch-test
Batch-testning kan du testa upp till 1 000 utterances i taget. Satsen får inte innehålla dubbletter. [Exportera](create-new-app.md#export-app) appen för att se en lista över aktuella utterances.  

Test-strategin för LUIS använder tre olika uppsättningar av data: modellen utterances, batch test utterances och slutpunkten utterances. Kontrollera att du inte använder utterances från antingen modellen utterances (läggs till syftet) eller slutpunkt utterances för den här självstudiekursen. 

Använd inte någon av utterances redan i appen för batch-test:

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Skapa en grupp för att testa avsiktshantering förutsägelsefunktionen
1. Skapa `homeauto-batch-1.json` i en textredigerare som [VSCode](https://code.visualstudio.com/). 

2. Lägg till utterances med den **avsikt** du vill att förväntade i testet. Den här självstudiekursen för att göra det enkelt ta utterances i den `HomeAutomation.TurnOn` och `HomeAutomation.TurnOff` och växla den `on` och `off` text i utterances. För den `None` avsikt, lägga till några utterances som inte är en del av den [domän](luis-glossary.md#domain) (ämne). 

    Lägg till endast sex avsikter för att förstå hur batch testresultaten stämmer med batch JSON.

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Kör om gruppen
1. Välj **Test** i det övre navigeringsfältet. 

    ![Välj Test i navigeringsfältet](./media/luis-tutorial-batch-testing/test-1.png)

2. Välj **Batch-tester panelen** i höger panel. 

    ![Välj Batch test](./media/luis-tutorial-batch-testing/test-2.png)

3. Välj **importera dataset**.

    ![Välj Importera dataset](./media/luis-tutorial-batch-testing/test-3.png)

4. Välj system sökvägen till den `homeauto-batch-1.json` filen.

5. Namnge datauppsättningen `set 1`.

    ![Välj fil](./media/luis-tutorial-batch-testing/test-4.png)

6. Klicka på knappen **Kör**. Vänta tills testet är klart.

    ![Välj Kör](./media/luis-tutorial-batch-testing/test-5.png)

7. Välj **se resultatet**.

    ![Visa resultat](./media/luis-tutorial-batch-testing/test-6.png)

8. Granska resultatet i diagram och legend.

    ![Batch-resultat](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Granska resultatet från batch
Batch-resultat finns i två delar. Den övre delen finns diagrammet och förklaringen. Den nedre delen visas utterances när du väljer ett namn för diagrammet.

Eventuella fel anges med röd färg. Diagrammet har fyra avsnitt med två avsnitt visas i rött. **Dessa finns i avsnitt för att fokusera på**. 

Upp till höger avsnittet anger testet felaktigt förutsade förekomsten av en avsikt eller enhet. Avsnittet nedre vänstra anger testet förutsade felaktigt avsaknaden av ett avsikt eller enhet.

### <a name="homeautomationturnoff-test-results"></a>Testresultat för HomeAutomation.TurnOff
I förklaringen, Välj den `HomeAutomation.TurnOff` avsikt. Den har en grön lyckas visas till vänster om namnet i förklaringen. Det finns inga fel för det här syftet. 

![Batch-resultat](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn och ingen avsikter innehåller fel
De andra två metoderna har fel, vilket innebär att testa förutsägelser matchade förväntningar för batch-filen. Välj den `None` avsiktshantering i förklaringen att granska det första felet. 

![Ingen avsiktshantering](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Fel visas i diagrammet i avsnitten red: **falska positiva** och **falska negativa**. Välj den **falska negativa** avsnittsnamn i diagrammet för att se misslyckade utterances under diagrammet. 

![FALSKT negativt fel](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

Misslyckas-utterance `help` förväntades som en `None` avsikt men testet förutsade `HomeAutomation.TurnOn` avsikt.  

Det finns två fel, en i HomeAutomation.TurnOn och en inget. Både orsakades av utterance `help` eftersom den inte gick att uppfylla kraven i ingen och det var en oväntat matchning för HomeAutomation.TurnOn avsikt. 

Att avgöra varför den `None` utterances misslyckas kan du granska utterances för närvarande i `None`. 

## <a name="review-none-intents-utterances"></a>Granska ingen avsiktshantering har utterances

1. Stäng den **Test** panelen genom att välja den **Test** knappen i det övre navigeringsfältet. 

2. Välj **skapa** från övre navigeringsfönstret. 

3. Välj **ingen** avsiktshantering från listan över avsikter.

4. Välj att visa utterances token kontroll + E 
    
    |Ingen avsiktshantering har utterances|Förutsägelse poäng|
    |--|--|
    |”minska temperatur för mig ange”|0.44|
    |”dim kökspersonalen ljus 25”.|0.43|
    |”sänka volymen”|0.46|
    |”Aktivera internet i min sovrum se”|0.28|

## <a name="fix-none-intents-utterances"></a>Åtgärda ingen avsiktshantering har utterances
    
Alla utterances i `None` bör finnas utanför tillämpningsdomänen. Dessa utterances är i förhållande till HomeAutomation, så att de är i fel avsikten. 

LUIS ger även utterances mindre än 50% (<.50) förutsägelse poäng. Om du tittar på utterances i de andra två metoderna kan du se mycket högre förutsägelse resultat. När LUIS har låg poäng för exempel utterances som är en bra indikation är på utterances förvirrande för LUIS mellan aktuella avsikten och andra avsikter. 

Åtgärda appen utterances för närvarande i den `None` avsikt måste flyttas till rätt avsikten och `None` avsikt måste nya, lämpliga avsikter. 

Tre av utterances i den `None` avsikt är avsedda att sänka Enhetsinställningar automation. De använder ord som `dim`, `lower`, eller `decrease`. Den fjärde utterance frågar aktivera på internet. Eftersom alla fyra utterances om att aktivera eller ändra graden av power till en enhet, bör de flyttas till den `HomeAutomation.TurnOn` avsikt. 

Detta är en lösning. Du kan också skapa en ny syftet med `ChangeSetting` flytta utterances med dim, sänka och minska till den nya avsikten. 

## <a name="fix-the-app-based-on-batch-results"></a>Åtgärda appen baserat på batch-resultat
Flytta fyra utterances till den `HomeAutomation.TurnOn` avsikt. 

1. Markera kryssrutan ovan utterance listan så att alla utterances är markerade. 

2. I den **omtilldela avsikt** listrutan, Välj `HomeAutomation.TurnOn`. 

    ![Flytta utterances](./media/luis-tutorial-batch-testing/move-utterances.png)

    När fyra utterances tilldelas utterance lista för den `None` avsikten är tom.

3. Lägg till fyra nya avsikter för avsiktshantering ingen:

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Dessa utterances är definitivt utanför domänen för HomeAutomation. När du anger varje utterance bevaka poängsättningen den. Poängsättningen kanske låg eller även mycket låg (med en röd ruta runt den). När du tränar appen i steg 8 vara poängsättningen mycket högre. 

7. Ta bort alla etiketter genom att välja den blå etiketten i utterance och välj **ta bort etiketten**.

8. Välj **Train** i det övre högra navigeringsfältet. Poängen för varje utterance är mycket högre. Alla resultat för den `None` avsikt ska finnas på högre.80 nu. 

## <a name="verify-the-fix-worked"></a>Verifiera korrigering fungerade
För att verifiera utterances i batch-test beräknas korrekt för den **ingen** avsikt, kör testet batch igen.

1. Välj **Test** i det övre navigeringsfältet. 

2. Välj **Batch-tester panelen** i höger panel. 

3. Välj de tre punkterna (...) till höger om batch-namn och välj **kör Dataset**. Vänta tills batch-testet är klart.

    ![Kör dataset](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Välj **se resultatet**. Innehållet bör ha grön ikoner till vänster om avsiktshantering namn. Med rätt typ av filter inställd på den `HomeAutomation.Turnoff` avsikt, Välj gröna punkt i den övre högra panelen som är närmast mitten av diagrammet. Namnet på utterance visas i tabellen nedan diagrammet. Poängen för `breezeway off please` är mycket låg. En valfri aktivitet är att lägga till flera utterances i avsikt att öka det här resultatet. 

    ![Kör dataset](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mer information om exempel utterances](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions