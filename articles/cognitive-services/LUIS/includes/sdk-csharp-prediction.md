---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 2ba136cd479da0cd394b5e5afe6ebe7c22b539d5
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732047"
---
Använd klientbiblioteket För förutsägelse för språkförståelse (LUIS) för .NET för att:

* Få förutsägelse av kortplats
* Förutsägelse efter version

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Biblioteks källkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Förutsägelsekörningspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Krav

* Portalkonto (Language Understanding) - [Skapa ett gratis](https://www.luis.ai)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Ett LUIS-app-ID – använd det offentliga `df67dcdb-c37d-46af-88e1-8b97951ca1c2`IoT-app-ID:et för . Användarfrågan som används i snabbstartskoden är specifik för den appen.

## <a name="setting-up"></a>Inrätta

### <a name="create-an-environment-variable"></a>Skapa en miljövariabel

Med hjälp av nyckeln och namnet på din resurs skapar du två miljövariabler för autentisering:

* `LUIS_PREDICTION_KEY`- Resursnyckeln för att autentisera dina begäranden.
* `LUIS_ENDPOINT_NAME`- Resursnamnet som är associerat med nyckeln.

Följ instruktionerna för operativsystemet.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

När du har lagt till miljövariabeln startar du om konsolfönstret.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[macOS](#tab/unix)

Redigera `.bash_profile`din och lägg till miljövariabeln:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.

---

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskad redigerare eller IDE.

1. I ett konsolfönster (till exempel cmd, PowerShell eller `new` Bash) använder du kommandot `language-understanding-quickstart`dotnet för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Ändra katalogen till den nyskapade appmappen.

1. Du kan skapa programmet med:

    ```dotnetcli
    dotnet build
    ```

    Byggutdata bör inte innehålla några varningar eller fel.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>Installera SDK:n

Installera klientbiblioteket för FÖRUTSÄGELSEkörning för lömtid för .NET i programkatalogen:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Om du använder Visual Studio IDE är klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.

## <a name="object-model"></a>Objektmodell

LUIS-förutsägelsekörningsklienten (Language Understanding) är ett [LUISRuntimeClient-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) som autentiserar till Azure, som innehåller din resursnyckel.

När klienten har skapats använder du den här klienten för att komma åt funktioner, inklusive:

* Förutsägelse genom [mellanlagring eller produktplats](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Förutsägelse per [version](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med klientbiblioteket För förutsägelsekörningskörning (Language Understanding) för .NET:

* [Förutsägelse efter kortplats](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna den *Program.cs* filen i önskad redigerare eller IDE från projektkatalogen. Ersätt den `using` befintliga koden `using` med följande direktiv:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autentisera klienten

1. Skapa variabler för nyckel, namn och app-ID:

    En variabler för att hantera förutsägelsenyckeln `LUIS_PREDICTION_KEY`som hämtats från en miljövariabel med namnet . Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE: t eller shell som kör den stängas och laddas om för att komma åt variabeln. Metoderna skapas senare.

    Skapa en variabel för `LUIS_ENDPOINT_NAME`att hålla ditt resursnamn .

    Skapa en variabel för app-ID:t som en miljövariabel med namnet `LUIS_APP_ID`. Ange miljövariabeln till den offentliga IoT-appen:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Skapa ett [ApiKeyServiceClientCredentials-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) med nyckeln och använd det med slutpunkten för att skapa ett [LUISRuntimeClient-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet)

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Få förutsägelse från körning

Lägg till följande metod för att skapa begäran i förutsägelsekörningen.

Användaren yttrande är en del av [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) objektet.

**Metoden GetSlotPredictionAsync** behöver flera parametrar, till exempel app-ID, platsnamnet, förutsägelsebegärandeobjektet för att uppfylla begäran. De andra alternativen, till exempel utförlig, visa alla avsikter och logg är valfria.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Huvudkoden för förutsägelsen

Använd följande huvudmetod för att binda variablerna och metoderna tillsammans för att få förutsägelsen.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `dotnet run` kommandot från programkatalogen.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med dina förutsägelser rensar du arbetet från den här snabbstarten genom att ta bort program.cs filen och dess underkataloger.
