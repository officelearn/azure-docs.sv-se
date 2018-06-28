---
title: Självstudie om hur du lägger till yttranden till en LUIS-app med hjälp av Python | Microsoft Docs
description: I den här självstudien lär du dig att anropa en LUIS-app med hjälp av Python.
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 3e1ca2ea874b6b39ac8a1b1eaa94fe9ff1894ea3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264309"
---
# <a name="tutorial-add-utterances-to-app-using-python"></a>Självstudie: Lägga till yttranden till en app med Python
I den här självstudien skriver du ett program för att lägga till ett yttrande till en avsikt med hjälp av redigerings-API:er i Python.

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa Visual Studio-konsolprojekt 
> * Lägg till metod för att anropa LUIS API för att lägga till yttrande och träna appen
> * Lägg till JSON-fil med exempelyttranden för BookFlight-avsikt
> * Kör konsol och visa träningsstatus för yttranden

Mer information finns i den tekniska dokumentationen för API:erna för [lägga till exempelyttrande till avsikt](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45), [träna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) och [träningsstatus](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 3.6](https://www.python.org/downloads/) eller senare.
* **(Rekommenderas)** [Visual Studio Code](https://code.visualstudio.com/) för IntelliSense och felsökning.
* Din **[LUIS-redigeringsnyckel](luis-concept-keys.md#authoring-key)**. Du hittar den här nyckeln under kontoinställningarna på [LUIS-webbplatsen](luis-reference-regions.md).
* Ditt befintliga [**LUIS-program-ID**](./luis-get-started-create-app.md). Program-ID visas på programmets instrumentpanel. LUIS-programmet med de avsikter och entiteter som används i filen `utterances.json` måste finnas innan koden i `add-utterances.js` körs. Koden i den här artikeln skapar inte avsikterna och entiteterna. Den lägger bara till yttrandena för befintliga avsikter och entiteter. 
* Den **versions-ID** i programmet som tar emot yttrandena. Standard-ID är ”0.1”
* Skapa en ny fil med namnet `add-utterances-3-6.py` i projektet i VSCode.

> [!NOTE] 
> Den fullständiga `add-utterances-3-6.py`-filen finns tillgänglig från [**LUIS-Samples**-Github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/python).


## <a name="write-the-python-code"></a>Skriva Python-koden

1. Kopiera följande kodfragment:

   [!code-python[Console app code that adds an utterance Python 3.6](~/samples-luis/documentation-samples/authoring-api-samples/python/add-utterances-3-6.py)]

## <a name="specify-utterances-to-add"></a>Ange yttranden att lägga till
Skapa och redigera filen `utterances.json` för att ange den **matris med yttranden** som du vill lägga till LUIS-appen. Avsikten och entiteterna **måste** redan finnas i LUIS-appen.

> [!NOTE]
> LUIS-programmet med de avsikter och entiteter som används i filen `utterances.json` måste finnas innan koden i `add-utterances.js` körs. Koden i den här artikeln skapar inte avsikterna och entiteterna. Den lägger bara till yttrandena för befintliga avsikter och entiteter.

Fältet `text` innehåller texten för yttrandet. Fältet `intentName` måste motsvara namnet på en avsikt i LUIS-appen. Fältet `entityLabels` är obligatoriskt. Om du inte vill märka några entiteter anger du en tom lista såsom det visas i följande exempel:

Om listan entityLabels inte är tom måste `startCharIndex` och `endCharIndex` märka den entitet som anges i fältet `entityName`. Båda index är nollbaserade antal; alltså refererar 6 i det översta exemplet till ”S” i Seattle och inte till blanksteget före versalt S.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>Lägg till ett yttrande från kommandoraden

Kör programmet från en kommandorad med Python 3.6.

Om add-utterance anropas utan argument läggs ett yttrande till i appen, men det tränas inte.

````
> python add-utterances-3-6.py
````

Det här exemplet skapar en fil med den `results.json` som innehåller resultatet av anrop av API för att lägga till yttranden. Fältet `response` är i det här formatet för de yttranden som lades till. `hasError` är falskt, vilket indikerar att yttrandet lades till.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>Lägg till ett yttrande och träna från kommandoraden
Anropa add-utterance med argumentet `-train` för att skicka en begäran om att träna, och begär sedan träningsstatus. Statusen placeras i kö omedelbart efter att träningen börjar. Statusinformation skrivs till en fil.

````
> python add-utterances-3-6.py -train
````

> [!NOTE]
> Duplicerade yttranden läggs inte till igen, men de orsakar heller inte något fel. `response` innehåller ID för det ursprungliga yttrandet.

När du anropar exemplet med argumentet `-train` skapas en `training-results.json`-fil som indikerar att begäran om att träna LUIS-appen placerades i kö. 

Nedan visas resultatet av en lyckad begäran om att träna:
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

När begäran om att träna har placerats i kö kan det ta en stund att slutföra träningen.

## <a name="get-training-status-from-the-command-line"></a>Hämta träningsstatus från kommandoraden
Anropa exemplet med argumentet `-status` för att kontrollera träningsstatus och skriva statusinformation till en fil.

````
> python add-utterances-3-6.py -status
````
## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med självstudien tar du bort Visual Studio och konsolprogrammet om du inte längre behöver dem. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skapa en LUIS-app programmässigt](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website

