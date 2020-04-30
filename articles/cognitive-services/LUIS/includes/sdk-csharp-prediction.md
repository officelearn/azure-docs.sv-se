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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732047"
---
Använd Language Understanding (LUIS) förutsägelse klient bibliotek för .NET för att:

* Hämta förutsägelse per plats
* Förutsägelse efter version

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [förutsägelse körnings paket (NuGet) C#-](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Krav

* Language Understanding (LUIS) Portal konto – [skapa ett kostnads fritt](https://www.luis.ai)
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* Ett LUIS app-ID – Använd ID: t för `df67dcdb-c37d-46af-88e1-8b97951ca1c2`den offentliga IoT-appen. Den användar fråga som används i snabb starts koden är speciell för den appen.

## <a name="setting-up"></a>Konfigurera

### <a name="create-an-environment-variable"></a>Skapa en miljö variabel

Med hjälp av din nyckel och namnet på din resurs, skapar du två miljövariabler för autentisering:

* `LUIS_PREDICTION_KEY`– Resurs nyckeln för autentisering av dina begär Anden.
* `LUIS_ENDPOINT_NAME`– Resurs namnet som är associerat med din nyckel.

Följ anvisningarna för ditt operativ system.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Starta om konsol fönstret när du har lagt till miljövariabeln.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[macOS](#tab/unix)

Redigera din `.bash_profile`och Lägg till miljövariabeln:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.

---

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE.

1. I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du kommandot dotNet `new` för att skapa en ny konsol app med namnet. `language-understanding-quickstart` Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll `Program.cs`fil:.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Ändra katalogen till mappen nyligen skapade appar.

1. Du kan bygga programmet med:

    ```dotnetcli
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

### <a name="install-the-sdk"></a>Installera SDK:n

I program katalogen installerar du klient biblioteket Language Understanding (LUIS) förutsägelse körning för .NET med följande kommando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

## <a name="object-model"></a>Objekt modell

Den Language Understanding (LUIS) förutsägelse runtime-klienten är ett [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) -objekt som autentiserar till Azure, som innehåller din resurs nyckel.

När klienten har skapats använder du den här klienten för att få åtkomst till funktioner, inklusive:

* Förutsägelse per [mellanlagring eller produkt plats](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Förutsägelse efter [version](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med klient biblioteket Language Understanding (LUIS) förutsägelse körning för .NET:

* [Förutsägelse per plats](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du *program.cs* -filen i önskat redigerings program eller IDE. Ersätt den befintliga `using` koden med följande `using` direktiv:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autentisera klienten

1. Skapa variabler för nyckeln, namn och app-ID:

    En variabel som hanterar din förutsägelse nyckel från en miljö variabel med namnet `LUIS_PREDICTION_KEY`. Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE eller gränssnittet som kör den stängas och läsas in igen för att få åtkomst till variabeln. Metoderna kommer att skapas senare.

    Skapa en variabel för att lagra resurs namnet `LUIS_ENDPOINT_NAME`.

    Skapa en variabel för app-ID som en miljö variabel med `LUIS_APP_ID`namnet. Ange miljövariabeln till den offentliga IoT-appen:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Skapa ett [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) -objekt.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Hämta förutsägelse från körning

Lägg till följande metod för att skapa begäran till förutsägelse körning.

Användaren uttryck är en del av [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) -objektet.

Metoden **GetSlotPredictionAsync** kräver flera parametrar, till exempel App-ID, plats namn, objekt för förutsägelse begär Ande för att uppfylla begäran. De andra alternativen, till exempel verbose, Visa alla avsikter och loggen är valfria.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Huvud kod för förutsägelsen

Använd följande huvudsakliga metod för att koppla ihop variablerna och metoderna för att hämta förutsägelsen.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `dotnet run` kommandot från program katalogen.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med dina förutsägelser kan du rensa upp arbetet från den här snabb starten genom att ta bort program.cs-filen och dess under kataloger.
