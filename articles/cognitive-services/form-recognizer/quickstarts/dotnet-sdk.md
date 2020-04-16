---
title: 'Snabbstart: Formulär recognizer-klientbibliotek för .NET'
description: Kom igång med formulärreformsklientbiblioteket för .NET för att träna, extrahera, analysera och få strukturerad datautdata med den här snabbstarten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 8d05f5ad07f9b3affba9442d6caa6e63162b813c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399410"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Snabbstart: Formulär recognizer-klientbibliotek för .NET

Kom igång med formulärreformsklientbiblioteket för .NET. Formulärre recognizer är en kognitiv tjänst som använder maskininlärningsteknik för att identifiera och extrahera nyckel-/värdepar och tabelldata från formulärdokument. Sedan returnerar tjänsten strukturerade data som innehåller relationerna i den ursprungliga filen. Följ dessa steg för att installera SDK-paketet och prova exempelkoden för grundläggande uppgifter.

Använd klientbiblioteket Formulär recognizer för .NET för att:

* [Träna en anpassad formulärapenetermodell](#train-a-custom-model)
* [Hämta en lista med extraherade nycklar](#get-a-list-of-extracted-keys)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Få en lista över anpassade modeller](#get-a-list-of-custom-models)
* [Ta bort en anpassad modell](#delete-a-custom-model)

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/).
* En Azure Storage-blob som innehåller en uppsättning träningsdata. Se [Skapa en utbildningsdatauppsättning för en anpassad modell](../build-training-data-set.md) för tips och alternativ för att sätta ihop dina träningsdata. För den här snabbstarten kan du använda filerna under mappen **Tåg** i [exempeldatauppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451).
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-form-recognizer-azure-resource"></a>Skapa en Azure-resurs för formulärkon recognizeer

[!INCLUDE [create resource](../includes/create-resource.md)]

När du har fått en nyckel och slutpunkt [skapar du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln och slutpunkten, namngivna `FORM_RECOGNIZER_KEY` respektive `FORM_RECOGNIZER_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du `formrecognizer-quickstart`kommandot för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Ändra katalogen till den nyskapade appmappen. Bygg sedan programmet med:

```console
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

Öppna den _Program.cs_ filen i önskad redigerare eller IDE från projektkatalogen. Lägg till följande `using`-uttryck:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Lägg sedan till följande kod i programmets **huvudmetod.** Du definierar den här asynkrona aktiviteten senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera klientbiblioteket Formulär recognizer för .NET i programkatalogen med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Om du använder Visual Studio IDE är klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.

## <a name="object-model"></a>Objektmodell

Följande klasser hanterar huvudfunktionerna i Formulär recognizer SDK.

|Namn|Beskrivning|
|---|---|
|[FormulärRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Den här klassen behövs för alla formulärreformsankärefunktioner. Du instansierar den med din prenumerationsinformation och använder den för att producera instanser av andra klasser.|
|[TågRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Du använder den här klassen för att träna en anpassad formulärreformsankäremodell med dina egna träningsindata. |
|[TågResultat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Den här klassen levererar resultaten av en anpassad modell Tågåtgärd, inklusive modell-ID, som du sedan kan använda för att analysera formulär. |
|[AnalyseraResultat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Den här klassen levererar resultaten av en anpassad modellanalysåtgärd. Den innehåller en lista över **ExtraheradePage-instanser.** |
|[ExtraheradSida](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Den här klassen representerar alla data som extraherats från ett enda formulärdokument.|

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket Formulärmedkänningsklämning för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Träna en anpassad formulärapenetermodell](#train-a-custom-model)
* [Hämta en lista med extraherade nycklar](#get-a-list-of-extracted-keys)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Få en lista över anpassade modeller](#get-a-list-of-custom-models)
* [Ta bort en anpassad modell](#delete-a-custom-model)

## <a name="define-variables"></a>Definiera variabler

Innan du definierar några metoder lägger du till **Program** följande variabeldefinitioner högst upp i programklassen. Du måste fylla i några av variablerna själv. 

* Om du vill hämta SAS-URL:en för dina träningsdata öppnar du Microsoft Azure Storage Explorer, högerklickar på behållaren och väljer **Hämta signatur för delad åtkomst**. Kontrollera att behörigheterna **Läs** och **Lista** är markerade och klicka på **Skapa**. Kopiera sedan värdet i **avsnittet URL.** Det bör ha `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`formen: .
* Om du behöver ett exempelformulär för att analysera kan du använda en av filerna under **mappen Testa** i [exempeldatauppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451). Den här guiden använder endast PDF-formulär.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Under `Main` metoden definierar du den uppgift `Main`som refereras i . Här autentiserar du klientobjektet med hjälp av de prenumerationsvariabler som du har definierat ovan. Du definierar de andra metoderna senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Följande metod använder ditt formuläridentifierare-klientobjekt för att träna en ny igenkänningsmodell på de dokument som lagras i din Azure-blob-behållare. Den använder en hjälpmetod för att visa information om den nyligen utbildade modellen (representerad av ett [ModelResult-objekt)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) och returnerar modell-ID.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

Följande hjälpmetod visar information om en formulärreformaremodell.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Hämta en lista med extraherade nycklar

När utbildningen är klar behåller den anpassade modellen en lista med nycklar som den har extraherat från utbildningsdokumenten. Den förväntar sig att framtida formulärdokument ska innehålla dessa nycklar och extraherar motsvarande värden i åtgärden Analysera. Använd följande metod för att hämta listan med extraherade nycklar och skriva ut den på konsolen. Detta är ett bra sätt att kontrollera att utbildningsprocessen var effektiv.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Den här metoden använder formulärrekreatorklienten och ett modell-ID för att analysera ett PDF-formulärdokument och extrahera nyckel-/värdedata. Den använder en hjälpmetod för att visa resultaten (representeras av ett [AnalyzeResult-objekt).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)

> [!NOTE]
> Följande metod analyserar ett PDF-formulär. Liknande metoder som analyserar JPEG- och PNG-formulär finns i den fullständiga exempelkoden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Följande hjälpmetod visar information om en analysåtgärd.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Få en lista över anpassade modeller

Du kan returnera en lista över alla tränade modeller som tillhör ditt konto och du kan hämta information om när de skapades. Listan över modeller representeras av ett [ModelsResult-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Ta bort en anpassad modell

Om du vill ta bort den anpassade modellen från ditt konto använder du följande metod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Köra appen

Kör programmet genom `dotnet run` att anropa kommandot från programkatalogen.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portalen](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Om du har tränat en anpassad modell som du vill ta bort från ditt konto kör du dessutom metoden i [Ta bort en anpassad modell](#delete-a-custom-model).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du formulärreformsklientbiblioteket .NET för att träna en anpassad modell och analysera formulär. Lär dig sedan tips för att skapa en bättre träningsdatauppsättning och producera mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../build-training-data-set.md)

* [Vad är formigenkänning?](../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
