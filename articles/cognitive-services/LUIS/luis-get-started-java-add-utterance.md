---
title: Självstudie om hur du lägger till yttranden till en LUIS-app med hjälp av Java | Microsoft Docs
description: I den här självstudien lär du dig att anropa en LUIS-app med hjälp av Java.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5b9c7b90ca96b23fbabfeb1e1d06e4124e65a0dc
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266048"
---
# <a name="tutorial-add-utterances-to-app-using-java"></a>Självstudie: Lägga till yttranden till en app med Java 
I den här självstudien skriver du ett program för att lägga till ett yttrande till en avsikt med hjälp av redigerings-API:er i Java.

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa Visual Studio-konsolprojekt 
> * Lägg till metod för att anropa LUIS API för att lägga till yttrande och träna appen
> * Lägg till JSON-fil med exempelyttranden för BookFlight-avsikt
> * Kör konsol och visa träningsstatus för yttranden

Mer information finns i den tekniska dokumentationen för API:erna för [lägga till exempelyttrande till avsikt](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [träna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) och [träningsstatus](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Senaste Oracle [**Java/JDK**](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Googles GSON JSON-bibliotek](https://github.com/google/gson).
* Din **[LUIS-redigeringsnyckel](luis-concept-keys.md#authoring-key)**. Du hittar den här nyckeln under kontoinställningarna på [LUIS-webbplatsen](luis-reference-regions.md).
* Ditt befintliga [**LUIS-program-ID**](./luis-get-started-create-app.md). Program-ID visas på programmets instrumentpanel. LUIS-programmet med de avsikter och entiteter som används i filen `utterances.json` måste finnas innan koden i `AddUtterances.java` körs. Koden i den här artikeln skapar inte avsikterna och entiteterna. Den lägger bara till yttrandena för befintliga avsikter och entiteter. 
* Den **versions-ID** i programmet som tar emot yttrandena. Standard-ID är ”0.1”
* Skapa en ny textfil med namnet `AddUtterances.java`.

> [!NOTE] 
> Den fullständiga `add-utterances.cs`-filen och en `utterances.json`-exempelfil finns tillgängliga från [**LUIS-Samples**-Github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/java).


## <a name="write-the-java-code"></a>Skriva Java-koden

Lägg till Java-beroendena till filen.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=31-34 "Java Dependencies")]

Skapa klassen `AddUtterances`

```Java
public class AddUtterances {
    // Insert code here
}
```

Den här klassen innehåller alla kodstycken som följer.

Lägg till LUIS-konstanterna i klassen. Kopiera följande kod och ändra till din redigeringsnyckel, ditt program-ID och ditt versions-ID.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=41-53 "LUIS-based IDs")]

Lägg till metoden för att anropa LUIS API. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=55-178 "HTTP request to LUIS")]

Lägg till metoden för HTTP-svaret från LUIS API.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=180-226 "HTTP response from LUIS")]


Lägg till undantagshantering. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=228-256 "Exception Handling")]

Lägga till hantering av utdata/utskrift.

   [!code-java[Add output handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=258-267 "Add configuration information for adding utterance")]

Lägg till huvudfunktionen.

   [!code-java[Add main function](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=269-345 "Add main function")]


## <a name="specify-utterances-to-add"></a>Ange yttranden att lägga till
Skapa och redigera filen `utterances.json` för att ange den **matris med yttranden** som du vill lägga till LUIS-appen. Avsikten och entiteterna **måste** redan finnas i LUIS-appen.

> [!NOTE]
> LUIS-programmet med de avsikter och entiteter som används i filen `utterances.json` måste finnas innan koden i `AddUtterances.java` körs. Koden i den här artikeln skapar inte avsikterna och entiteterna. Den lägger bara till yttrandena för befintliga avsikter och entiteter.

Fältet `text` innehåller texten för yttrandet. Fältet `intentName` måste motsvara namnet på en avsikt i LUIS-appen. Fältet `entityLabels` är obligatoriskt. Om du inte vill märka några entiteter anger du en tom lista såsom det visas i följande exempel.

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

Kompilera AddUtterance med beroendena

```
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

Om `AddUtterance` anropas utan argument läggs LUIS-yttrandena till i appen, men de tränas inte.
````
> java -classpath .;gson-2.8.2.jar AddUtterances
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
Anropa `add-utterance` med argumentet `-train` för att skicka en begäran om att träna. 

````
> java -classpath .;gson-2.8.2.jar AddUtterances -train
````

> [!NOTE]
> Duplicerade yttranden läggs inte till igen, men de orsakar heller inte något fel. `response` innehåller ID för det ursprungliga yttrandet.

När du anropar appen med argumentet `-train` skapas en `training-results.json`-fil som indikerar att begäran om att träna LUIS-appen placerades i kö. 

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
Anropa appen med argumentet `-status` för att kontrollera träningsstatus och skriva statusinformation till en fil.

````
> java -classpath .;gson-2.8.2.jar AddUtterances -status
````

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med självstudien tar du bort Visual Studio och konsolprogrammet om du inte längre behöver dem. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skapa en LUIS-app programmässigt](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website