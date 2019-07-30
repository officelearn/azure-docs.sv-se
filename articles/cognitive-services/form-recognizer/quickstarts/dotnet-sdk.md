---
title: 'Snabbstart: Formulär tolkens klient bibliotek för .NET | Microsoft Docs'
description: Kom igång med formulär tolkens klient bibliotek för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: f6cb364c231c2415bf49a24e6e9406a14640b892
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600275"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Snabbstart: Formulär tolkens klient bibliotek för .NET

Kom igång med formulär tolkens klient bibliotek för .NET. Formulär tolken är en kognitiv tjänst som använder maskin inlärnings teknik för att identifiera och extrahera nyckel/värde-par och tabell data från formulär dokument. Sedan returnerar tjänsten strukturerade data som innehåller relationerna i den ursprungliga filen. Följ de här stegen för att installera SDK-paketet och prova exempel koden för grundläggande uppgifter.

Använd formulär tolkens klient bibliotek för .NET för att:

* Träna en anpassad formulär igenkännings modell
* Analysera formulär med en anpassad modell
* Hämta en lista över anpassade modeller

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer) | [paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/).
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* En Azure Storage-blob som innehåller en uppsättning tränings data. Se [skapa en tränings data uppsättning för en anpassad modell](../build-training-data-set.md) för tips och alternativ för att sätta samman dina tränings data. 

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-form-recognizer-azure-resource"></a>Skapa en Azure-resurs för formulär igenkänning

[!INCLUDE [create resource](../includes/create-resource.md)]

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln, `FORM_RECOGNIZER_KEY`med namnet.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C# program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet. `formrecognizer-quickstart` Det här kommandot skapar ett enkelt "Hello World C# "-projekt med en enda källfil: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Bygg sedan programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Från projekt katalogen öppnar du _program.cs_ -filen i önskat redigerings program eller IDE. Lägg till följande `using`-uttryck:

```csharp
using Microsoft.Azure.CognitiveServices.FormRecognizer;
using Microsoft.Azure.CognitiveServices.FormRecognizer.Models;

using System;
using System.IO;
using System.Threading.Tasks;
```

Lägg sedan till följande kod i programmets **main** -metod. Du ska definiera den här asynkrona uppgiften senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du formulär tolkens klient bibliotek för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

## <a name="object-model"></a>Objekt modell

Följande klasser hanterar huvud funktionerna i formulär igenkännings-SDK: n.

|Namn|Beskrivning|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Den här klassen krävs för alla formulär igenkännings funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Du använder den här klassen för att träna en anpassad formulär igenkännings modell med hjälp av egna utbildnings indata. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Den här klassen ger resultatet av en anpassad modell träna-åtgärd, inklusive modell-ID, som du sedan kan använda för att analysera formulär. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Den här klassen ger resultatet av en analys åtgärd för anpassad modell. Den innehåller en lista över **ExtractedPage** -instanser. |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Den här klassen representerar alla data som extraheras från ett enda formulär dokument.|

## <a name="code-examples"></a>Kod exempel

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

De här kodfragmenten visar hur du utför följande uppgifter med formulär tolkens klient bibliotek för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Träna en anpassad formulär igenkännings modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hämta en lista över anpassade modeller](#get-a-list-of-custom-models)

### <a name="define-variables"></a>Definiera variabler

Innan du definierar några metoder lägger du till följande variabel definitioner överst i **program** klassen. Du måste fylla i några av variablerna själv. 

* Du kan hitta tjänstens slut punkts värde i **översikts** avsnittet i Azure Portal. 
* Om du vill hämta SAS-URL: en för dina utbildnings data öppnar du Microsoft Azure Storage Explorer, högerklickar på behållaren och väljer **Hämta signatur för delad åtkomst**. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Den bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

### <a name="authenticate-the-client"></a>Autentisera klienten

Under metoden definierar du uppgiften som refereras till i `Main`. `Main` Här autentiserar du klient-objektet med hjälp av de prenumerations variabler som du definierade ovan. Du ska definiera andra metoder senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

### <a name="train-a-custom-model"></a>Träna en anpassad modell

I följande metod används ditt formulär tolks klient objekt för att träna en ny igenkännings modell på dokumenten som lagras i din Azure Blob-behållare. Den använder en hjälp metod för att visa information om den nyligen intränaa modellen (som representeras av ett [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) -objekt) och returnerar modell-ID: t.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

Följande hjälp metod visar information om en formulär tolks modell.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

### <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Med den här metoden används formulär tolks klienten och ett modell-ID för att analysera ett PDF-formulär dokument och extrahera nyckel/värde-data. Den använder en hjälp metod för att visa resultaten (representeras av ett [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) -objekt).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Följande hjälp metod visar information om en analys åtgärd.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

### <a name="get-a-list-of-custom-models"></a>Hämta en lista över anpassade modeller

Du kan returnera en lista över alla utbildade modeller som tillhör ditt konto och du kan hämta information om när de skapades. Listan över modeller representeras av ett [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) -objekt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="run-the-application"></a>Köra programmet

Kör programmet genom att anropa `dotnet run` kommandot från program katalogen.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken .NET-klient bibliotek för att träna en anpassad modell och analysera formulär. Nu ska du lära dig hur du skapar en bättre tränings data uppsättning och ger mer exakta modeller.

> [!div class="nextstepaction"]
>[Skapa en tränings data uppsättning](../build-training-data-set.md)

* [Vad är formulär igenkänning?](../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
