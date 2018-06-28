---
title: Självstudie om hur du lägger till yttranden till en LUIS-app med hjälp av C# | Microsoft Docs
description: I den här självstudien lär du dig att anropa en LUIS-app med hjälp av C#.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: v-geberr
ms.openlocfilehash: d9b3ca46cc635d961edadf3e3555f9656b6b5a1d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264251"
---
# <a name="tutorial-add-utterances-to-a-luis-app-using-c"></a>Självstudie: Lägga till yttranden till en LUIS-app med C# 
I den här självstudien skriver du ett program för att lägga till ett yttrande till en avsikt med hjälp av redigerings-API:er i C#.

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa Visual Studio-konsolprojekt 
> * Lägg till metod för att anropa LUIS API för att lägga till yttrande och träna appen
> * Lägg till JSON-fil med exempelyttranden för BookFlight-avsikt
> * Kör konsol och visa träningsstatus för yttranden

Mer information finns i den tekniska dokumentationen för API:erna för [lägga till exempelyttrande till avsikt](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [träna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) och [träningsstatus](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Senaste [**Visual Studio Community-versionen**](https://www.visualstudio.com/downloads/). 
* Din **[LUIS-redigeringsnyckel](luis-concept-keys.md#authoring-key)**. Du hittar den här nyckeln under kontoinställningarna på [LUIS-webbplatsen](luis-reference-regions.md).
* Ditt befintliga [**LUIS-program-ID**](./luis-get-started-create-app.md). Program-ID visas på programmets instrumentpanel. LUIS-programmet med de avsikter och entiteter som används i filen `utterances.json` måste finnas innan den här koden körs. Koden i den här artikeln skapar inte avsikterna och entiteterna. Den lägger till yttrandena för befintliga avsikter och entiteter. 
* Den **versions-ID** i programmet som tar emot yttrandena. Standard-ID är ”0.1”
* Skapa en ny fil med namnet `add-utterances.cs` i projektet i VSCode.

> [!NOTE] 
> Den fullständiga C#-lösningen inklusive en `utterances.json`-exempelfil finns tillgängliga från [**LUIS-Samples**-Github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/csharp/).

## <a name="create-the-project-in-visual-studio"></a>Skapa projektet i Visual Studio

I Visual Studio skapar du en ny **Windows Classic Desktop Console**-app med hjälp av .Net Framework. 

![Visual Studio-projekttyp](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

## <a name="add-the-systemweb-dependency"></a>Lägga till System.Web-beroendet

I Visual Studio-projektet behöver projektet **System.Web**. I Solution Explorer högerklickar du på **Referenser** och väljer **Lägg till referens**.

![Lägga till System.web-referens](./media/luis-quickstart-cs-add-utterance/system.web.png)

## <a name="write-the-c-code"></a>Skriva C#-koden
**Program.cs**-filen ska vara:

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

Lägg till System.IO- och System.Net.Http-beroendena.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=1-6 "Add the dependencies")]


Lägg till LUIS-ID:n och strängarna till klassen **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=12-30&dedent=8 "Add the LUIS IDs and strings")]

Lägg till metoden JsonPrettyPrint.

   [!code-csharp[Add the JsonPrettyPrint method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=32-92 "Add the JsonPrettyPrint method")]

Lägg till den GET-begäran som används för att skapa yttranden eller starta träning. 

   [!code-csharp[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=93-103 "SendGet")]


Lägg till den POST-begäran som används för att skapa yttranden eller starta träning. 

   [!code-csharp[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=104-115 "SendPost")]

Lägg till `AddUtterances`-funktionen.

   [!code-csharp[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=116-125 "AddUtterances method")]


Lägg till `Train`-funktionen. 

   [!code-csharp[Train](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=126-136 "Train")]

Lägg till `Status`-funktionen.

   [!code-csharp[Status](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=137-143 "Status")]

För att hantera argument lägger du till huvudkoden.

   [!code-csharp[Main code](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=144-172 "Main code")]

## <a name="specify-utterances-to-add"></a>Ange yttranden att lägga till
Skapa och redigera filen `utterances.json` för att ange den **matris med yttranden** som du vill lägga till LUIS-appen. Avsikten och entiteterna **måste** redan finnas i LUIS-appen.

> [!NOTE]
> LUIS-programmet med de avsikter och entiteter som används i filen `utterances.json` måste finnas innan koden i `add-utterances.cs` körs. Koden i den här artikeln skapar inte avsikterna och entiteterna. Den lägger bara till yttrandena för befintliga avsikter och entiteter.

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

## <a name="mark-the-json-file-as-content"></a>Märk JSON-filen som innehåll
I Solution Explorer högerklickar du på `utterances.json` och väljer **Egenskaper**. I fönstret Properties (Egenskaper) märker du **Build Action** (Skapandeåtgärd) för `Content` och **Copy to Output Directory** (Kopiera till utdatakatalog) för `Copy Always`.  

![Märk JSON-filen som innehåll](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="add-an-utterance-from-the-command-line"></a>Lägg till ett yttrande från kommandoraden

Kompilera och köra programmet från en kommandorad med C# från katalogen /bin/Debug. Kontrollera att filen utterances.json också finns i den här katalogen.

Om add-utterances.cs anropas med endast utterance.json som ett argument läggs LUIS till i de nya yttrandena, men det tränas inte.
````
ConsoleApp\bin\Debug> ConsoleApp1.exe utterances.json
````

Den här kommandoraden visar resultatet av anrop av API för att lägga till yttranden. Fältet `response` är i det här formatet för de yttranden som lades till. `hasError` är falskt, vilket indikerar att yttrandet lades till.  

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
Anropa add-utterance med argumentet `-train` för att skicka en begäran om att träna. 

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -train utterances.json
````

> [!NOTE]
> Duplicerade yttranden läggs inte till igen, men de orsakar heller inte något fel. `response` innehåller ID för det ursprungliga yttrandet.

Följande JSON visar resultatet av en lyckad begäran om att träna:

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
Anropa appen med argumentet `-status` för att kontrollera träningsstatus och visa statusinformation.

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med självstudien tar du bort Visual Studio och konsolprogrammet om du inte längre behöver dem. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skapa en LUIS-app programmässigt](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
