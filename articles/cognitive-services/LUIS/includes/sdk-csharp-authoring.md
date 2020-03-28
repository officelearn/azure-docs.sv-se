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
ms.openlocfilehash: 53e6382cf8d046b2c9818b906890bc64642fd2ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372136"
---
Använd klientbiblioteket För redigering av språkförståelse (LUIS) för .NET för att:

* Skapa en app
* Lägga till avsikter, entiteter och exempelyttranden
* Lägga till funktioner som en fraslista
* Appen Träna och publicera

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [Författande paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C# Exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Krav

* Portalkonto (Language Understanding) - [Skapa ett gratis](https://www.luis.ai)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).


## <a name="setting-up"></a>Inrätta

### <a name="get-your-language-understanding-luis-starter-key"></a>Hämta startnyckeln för språkförståelse (LUIS)

Hämta [startnyckeln](../luis-how-to-azure-subscription.md#starter-key) genom att skapa en LUIS-redigeringsresurs. Behåll nyckeln och regionen för nyckeln för nästa steg.

### <a name="create-an-environment-variable"></a>Skapa en miljövariabel

Med hjälp av nyckeln och regionen för nyckeln skapar du två miljövariabler för autentisering:

* `COGNITIVESERVICE_AUTHORING_KEY`- Resursnyckeln för att autentisera dina begäranden.
* `COGNITIVESERVICE_REGION`- Regionen som är kopplad till din nyckel. Till exempel `westus`.

Följ instruktionerna för operativsystemet.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

När du har lagt till miljövariabeln startar du om konsolfönstret.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[Macos](#tab/unix)

Redigera `.bash_profile`din och lägg till miljövariabeln:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.
***

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

Installera klientbiblioteket för att skapa språkförfattande (LUIS) i programkatalogen med följande kommando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Om du använder Visual Studio IDE är klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.


## <a name="object-model"></a>Objektmodell

LUIS-redigeringsklienten (Language Understanding) är ett [LUISAuthoringClient-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) som autentiserar till Azure, som innehåller din redigeringsnyckel.

När klienten har skapats använder du den här klienten för att komma åt funktioner, inklusive:

* Appar - [skapa,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) [ta bort](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [publicera](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Exempel yttranden - [lägga till efter batch](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), ta bort av [ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Funktioner - hantera [fraslistor](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Modell - hantera [avsikter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) och entiteter
* Mönster - hantera [mönster](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Tåg - [träna](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) appen och enkät för [träningsstatus](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versioner](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - hantera med klon, export och ta bort


## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med klientbiblioteket För att skapa språkförfattande (LUIS) för .NET:

* [Skapa en app](#create-a-luis-app)
* [Lägg till entiteter](#create-entities-for-the-app)
* [Lägg till avsikter](#create-intent-for-the-app)
* [Lägga till exempelyttranden](#add-example-utterance-to-intent)
* [Träna appen](#train-the-app)
* [Publicera appen](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna den *Program.cs* filen i önskad redigerare eller IDE från projektkatalogen. Ersätt den `using` befintliga koden `using` med följande direktiv:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autentisera klienten

1. Skapa en variabel för att hantera redigeringsnyckeln som hämtats från en miljövariabel med namnet `COGNITIVESERVICES_AUTHORING_KEY`. Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE: t eller shell som kör den stängas och laddas om för att komma åt variabeln. Metoderna skapas senare.

1. Skapa variabler för att hålla din redigeringsregion och slutpunkt. Vilken region för din redigeringsnyckel beror på var du skriver. De [tre författarregionerna](../luis-reference-regions.md) är:

    * Australien -`australiaeast`
    * Europa -`westeurope`
    * USA och andra regioner `westus` - (Standard)

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Skapa ett [ApiKeyServiceClientCredentials-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) med nyckeln och använd det med slutpunkten för att skapa ett [LUISAuthoringClient-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Skapa en LUIS-app

1. Skapa en LUIS-app som innehåller NLP-modellen (Natural Language Processing) som innehåller avsikter, entiteter och exempelyttranden.

1. Skapa ett [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). Namn och språkkultur är obligatoriska egenskaper.

1. Anropa metoden [Apps.AddAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) Svaret är app-ID.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Skapa avsikt för appen
Det primära objektet i en LUIS-apps modell är avsikten. Avsikten justerar med en gruppering av avsikter för _användarens_yttrande . En användare kan ställa en fråga eller göra ett uttalande som letar efter ett särskilt _avsett_ svar från en bot (eller annat klientprogram). Exempel på avsikter är att boka ett flyg, fråga om vädret i en destinationsstad och fråga om kontaktuppgifter för kundservice.

Skapa ett [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) med namnet på den unika avsikten och skicka sedan app-ID, versions-ID och ModelCreateObject till metoden [Model.AddIntentAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) Svaret är avsikts-ID.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Skapa entiteter för appen

Entiteter krävs inte, men de finns i de flesta appar. Entiteten extraherar information från användarens yttrande, nödvändigt att fullfil användarens avsikt. Det finns flera typer av färdiga och anpassade [entiteter,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) var och en med sina egna DTO-modeller (Data Transformation Object).  Vanliga fördefinierade entiteter som ska läggas till i appen är [nummer,](../luis-reference-prebuilt-number.md) [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Den här metoden `Location` **AddEntities** skapade en `Class` enkel entitet med två roller, en enkel entitet, en `Flight` sammansatt entitet och lägger till flera fördefinierade entiteter.

Det är viktigt att veta att entiteter inte är markerade med en avsikt. De kan och brukar gälla för många avsikter. Endast exempelanvändaryttranden är markerade för en specifik, enda avsikt.

Skapa metoder för entiteter är en del av [klassen Modell.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) Varje entitetstyp har sin egen DTO-modell `model` (Data Transformation Object), som vanligtvis innehåller ordet i [modellnamnområdet.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet)

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Lägga till exempelutseende till avsikt

För att fastställa en yttrande avsikt och extrahera entiteter, behöver appen exempel på yttranden. Exemplen måste inriktas på en specifik, enda avsikt och bör markera alla anpassade entiteter. Fördefinierade entiteter behöver inte markeras.

Lägg till exempelyttranden genom att skapa en lista med [ExempelLabelObject-objekt,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) ett objekt för varje exempelutseende. Varje exempel ska markera alla entiteter med en ordlista med namn-/värdepar med entitetsnamn och entitetsvärde. Entitetsvärdet ska vara exakt som det visas i texten i exempelutseendet.

Ring [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) med app-ID, versions-ID och listan med exempel. Samtalet svarar med en lista med resultat. Du måste kontrollera varje exempels resultat för att se till att det har lagts till i modellen.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

**Metoderna CreateUtterance** och **CreateLabel** är verktygsmetoder som hjälper dig att skapa objekt.

## <a name="train-the-app"></a>Träna appen

När modellen har skapats måste LUIS-appen tränas för den här versionen av modellen. En tränad modell kan användas i en [behållare](../luis-container-howto.md)eller [publiceras](../luis-how-to-publish-app.md) på mellanlagrings- eller produktplatserna.

Metoden [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) behöver app-ID och versions-ID.

En mycket liten modell, som denna snabbstart visar, kommer att träna mycket snabbt. För program på produktionsnivå bör appen innehålla ett avsökningsanrop till [metoden GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) för att avgöra när eller om utbildningen lyckades. Svaret är en lista över [ModelTrainingInfo-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) med en separat status för varje objekt. Alla objekt måste lyckas för att utbildningen ska anses vara fullständig.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Publicera en språk understanding-app

Publicera LUIS-appen med metoden [PublishAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) Detta publicerar den aktuella tränade versionen till den angivna platsen vid slutpunkten. Klientprogrammet använder den här slutpunkten för att skicka användaryttranden för förutsägelse av avsikts- och entitetsextrahering.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `dotnet run` kommandot från programkatalogen.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa kan du ta bort LUIS-appen. Ta bort appen görs med metoden [Apps.DeleteAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) Du kan också ta bort appen från [LUIS-portalen](https://www.luis.ai).