---
title: 'Snabbstart: Client Library för Computer Vision för .NET'
description: I den här snabbstarten kommer du igång med klientbiblioteket Computer Vision för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: ee4b6b4247ac1a5c988d8b455fd1d8a2bff889a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136002"
---
<a name="HOLTop"></a>

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-computer-vision-azure-resource"></a>Skapa en Azure-resurs för datorseende

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Datorseende med Hjälp av [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure-portalen](https://portal.azure.com/).

När du har fått en nyckel från utvärderingsprenumerationen eller resursen `COMPUTER_VISION_ENDPOINT`skapar du [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och slutpunkts-URL:en, med namn `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive , respektive.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskad redigerare eller IDE. 

I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du `computer-vision-quickstart`kommandot för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

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

Öppna den *Program.cs* filen i önskad redigerare eller IDE från projektkatalogen. Lägg till `using` följande direktiv:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

Skapa variabler för resursens Azure-slutpunkt och -nyckel i programmets **programklass.**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera klientbiblioteket Computer Vision för .NET i programkatalogen med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Om du använder Visual Studio IDE är klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av huvudfunktionerna i Computer Vision .NET SDK.

|Namn|Beskrivning|
|---|---|
| [ComputerVisionClient (Dator)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Den här klassen behövs för alla datorseendefunktioner. Du instansierar den med din prenumerationsinformation och använder den för att göra de flesta avbildningsåtgärder.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Den här klassen innehåller ytterligare metoder för **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Den här uppräkningen definierar de olika typer av bildanalys som kan göras i en standardanalysåtgärd. Du anger en uppsättning VisualFeatureTypes-värden beroende på dina behov. |

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket Computer Vision för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)
* [Läsa tryckt och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabbstarten förutsätter att du har [skapat miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln och slutpunkten för datorseende, med namn `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive. `COMPUTER_VISION_ENDPOINT`

I en ny metod instansierar du en klient med slutpunkten och nyckeln. Skapa ett **[ApiKeyServiceClientCredentials-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** med nyckeln och använd det med slutpunkten för att skapa ett **[ComputerVisionClient-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

Du kommer förmodligen vill anropa `Main` den här metoden i metoden.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Analysera en bild

Följande kod definierar en `AnalyzeImageUrl`metod, som använder klientobjektet för att analysera en fjärravbildning och skriva ut resultaten. Metoden returnerar en textbeskrivning, kategorisering, lista med taggar, identifierade ansikten, flaggor för barnförbjudet innehåll, huvudfärger och bildtyp.

Lägg till metodanropet i metoden. `Main`

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Konfigurera testavbildning

Spara en referens till webbadressen till den bild som du vill analysera i klassen **Program.**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> Du kan också analysera en lokal bild. Se exempelkoden på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) för scenarier med lokala avbildningar.

### <a name="specify-visual-features"></a>Ange visuella funktioner

Definiera din nya metod för bildanalys. Lägg till koden nedan, som anger visuella funktioner som du vill extrahera i analysen. Se **[VisualFeatureTypes-uppräkningen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** för en fullständig lista.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

### <a name="analyze"></a>Analysera

**Metoden AnalyzeImageAsync** returnerar ett **ImageAnalysis-objekt** som innehåller all extraherad information.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

Följande avsnitt visar hur du tolkar den här informationen i detalj.

### <a name="get-image-description"></a>Få bildbeskrivning

Följande kod hämtar listan över genererade bildtexter för bilden. Mer information finns i [Beskriv bilder.](../../concept-describing-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Hämta bildkategori

Följande kod får den identifierade kategorin av bilden. Mer information [finns i Kategorisera bilder.](../../concept-categorizing-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Hämta bildtaggar

Följande kod hämtar uppsättningen identifierade taggar i bilden. Mer information [finns i Innehållstaggar.](../../concept-tagging-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Upptäcka objekt

Följande kod identifierar vanliga objekt i bilden och skriver ut dem på konsolen. Mer information [finns i Objektidentifiering.](../../concept-object-detection.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företagets varumärken och logotyper i bilden och skriver ut dem på konsolen. Mer information [finns i Varumärkesidentifiering.](../../concept-brand-detection.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande kod returnerar de identifierade ytorna i bilden med sina rektangelkoordinater och väljer ansiktsattribut. Mer information finns i [Ansiktsigenkänning.](../../concept-detecting-faces.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera innehåll för vuxna, racy eller blodiga

Följande kod skriver ut den upptäckta förekomsten av sexuellt innehåll i bilden. Se [Vuxen, racy, blodiga innehåll](../../concept-detecting-adult-content.md) för mer information.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Hämta bildfärgschema

Följande kod skriver ut de identifierade färgattributen i bilden, till exempel de dominerande färgerna och accentfärgen. Se [Färgscheman](../../concept-detecting-color-schemes.md) för mer information.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Hämta domänspecifikt innehåll

Computer Vision kan använda specialiserade modeller för att göra ytterligare analys på bilder. Mer information [finns i Domänspecifikt innehåll.](../../concept-detecting-domain-content.md) 

Följande kod tolkar data om identifierade kändisar i bilden.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Hämta bildtypen

Följande kod skriver ut information om&mdash;vilken typ av bild det är ClipArt eller en linjeritning.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Läsa tryckt och handskriven text

Computer Vision kan läsa synlig text i en bild och konvertera den till en teckenström. Koden i det här avsnittet `ExtractTextUrl`definierar en metod, som använder klientobjektet för att identifiera och extrahera utskriven eller handskriven text i bilden.

Lägg till metodanropet i metoden. `Main`

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Konfigurera testavbildning

Spara en referens till webbadressen till den bild som du vill extrahera text från i klassen **Program.**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> Du kan också extrahera text från en lokal bild. Se exempelkoden på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) för scenarier med lokala avbildningar.

### <a name="call-the-read-api"></a>Anropa läs-API:et

Definiera den nya metoden för att läsa text. Lägg till koden nedan, som anropar **metoden BatchReadFileAsync** för den givna bilden. Detta returnerar ett åtgärds-ID och startar en asynkron process för att läsa bildens innehåll.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Få läsresultat

Hämta sedan åtgärds-ID:et som returneras från **BatchReadFileAsync-anropet** och använder det för att fråga tjänsten efter åtgärdsresultat. Följande kod kontrollerar åtgärden med en sekunds mellanrum tills resultaten returneras. Sedan skrivs de extraherade textdata ut till konsolen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Visa läsresultat

Lägg till följande kod för att tolka och visa hämtade textdata och avsluta metoddefinitionen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>Köra appen

Kör programmet från programkatalogen `dotnet run` med kommandot.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[API-referens för datorseende (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Vad är visuellt innehåll?](../../Home.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).
