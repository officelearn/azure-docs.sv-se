---
title: Ändra, träna appen, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: I den här C#-snabbstarten lägger du till exempelyttranden till en app för hemautomatisering och tränar appen.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 23692bd6a0c708b2747a3cc211b8238d30dfe5db
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161754"
---
# <a name="quickstart-change-model-using-c"></a>Snabbstart: Ändra modell med hjälp av C#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Senaste [**Visual Studio Community-versionen**](https://www.visualstudio.com/downloads/).
* Programmeringsspråket C# installerat.
* NuGet-paketen [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) och [CommandLine](https://www.nuget.org/packages/CommandLineParser/)

[!INCLUDE [Code is available in LUIS-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Kod för att skapa snabbstart 

I Visual Studio skapar du en ny **Windows Classic Desktop Console**-app med hjälp av .Net Framework. 

![Visual Studio-projekttyp](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>Lägga till System.Web-beroendet

Visual Studio-projektet behöver **System.Web**. I Solution Explorer högerklickar du på **Referenser** och väljer **Lägg till referens**.

![Lägga till System.web-referens](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Lägg till andra beroenden

Visual Studio-projektet behöver **JsonFormatterPlus** och **CommandLineParser**. I Solution Explorer högerklickar du på **Referenser** i väljer **Manage NuGet Packages...** (Hantera NuGet-paket...). Sök efter och lägg till vart och ett av de två paketen. 

![Lägga till beroenden från tredje part](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>Skriva C#-koden
**Program.cs**-filen ska vara:

```C#
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

Lägg till beroendena.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Lägg till LUIS-ID:n och strängarna till klassen **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Lägg till klassen för att hantera kommandoradsparametrar till klassen **Program**.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Lägg till GET-begärandemetoden till klassen **Program**.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Lägg till POST-begärandemetoden till klassen **Program**. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Lägg till metoden för exempelyttranden från fil till klassen **Program**.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

När ändringarna har tillämpats på modellen tränar du modellen. Lägg till metoden till klassen **Program**.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

Träningen slutförs kanske inte omedelbart. Kontrollera status för att verifiera att träningen är klar. Lägg till metoden till klassen **Program**.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

För att hantera kommandoradsargumenten lägger du till huvudkoden. Lägg till metoden till klassen **Program**.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Kopiera utterances.json till utdatakatalogen

I Solution Explorer högerklickar du på `utterances.json` och väljer **Egenskaper**. I fönstret Properties (Egenskaper) märker du **Build Action** (Skapandeåtgärd) för `Content` och **Copy to Output Directory** (Kopiera till utdatakatalog) för `Copy Always`.  

![Märk JSON-filen som innehåll](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Kompilera koden

Kompilera koden i Visual Studio. 

## <a name="run-code"></a>Köra koden

I projektets katalog /bin/Debug kör du programmet från en kommandorad. 

```console
ConsoleApp\bin\Debug> ConsoleApp1.exe --add utterances.json --train --status
```

Den här kommandoraden visar resultatet av anrop av API för att lägga till yttranden. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med snabbstarten tar du bort alla filer som skapas i den här snabbstarten. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skapa en LUIS-app programmässigt](luis-tutorial-node-import-utterances-csv.md) 