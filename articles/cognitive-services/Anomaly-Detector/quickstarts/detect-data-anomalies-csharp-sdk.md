---
title: 'Snabbstart: Identifiera avvikelser i tidsseriedata med klientbiblioteket Avvikelsedetektor för .NET'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API:et för avvikelsedetektor för att identifiera avvikelser i dataserien, antingen som en batch eller på strömmande data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 268ceee5504e6e48b9eb8fdae18564482480e250
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239124"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Snabbstart: Klientbibliotek för avvikelsedetektor för .NET

Kom igång med klientbiblioteket avvikelsedetektor för .NET. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Med tjänsten Avvikelsedetektor kan du hitta avvikelser i dina tidsseriedata genom att automatiskt använda de bäst passande modellerna på den, oavsett bransch, scenario eller datavolym.

Använd klientbiblioteket avvikelsedetektor för .NET för att:

* Identifiera avvikelser i hela tidsseriedatauppsättningen, som en batchbegäran
* Identifiera avvikelsestatus för den senaste datapunkten i tidsserien

[Biblioteksreferensdokumentation](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [Hitta koden på GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* En avvikelsedetektornyckel och slutpunkt

## <a name="setting-up"></a>Inrätta

### <a name="create-an-anomaly-detector-resource"></a>Skapa en avvikelsedetektorresurs

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du `anomaly-detector-quickstart`kommandot för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World"-projekt med en enda C#-källfil: *Program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

```dotnetcli
dotnet build
```

Byggutdata bör inte innehålla några varningar eller fel. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera klientbiblioteket Avvikelsedetektor för .NET i programkatalogen med följande kommando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Öppna program.cs-filen *från* projektkatalogen och lägg till `directives`följande med:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

I programmets `main()` metod skapar du variabler för resursens Azure-plats och din nyckel som en miljövariabel. Om du har skapat miljövariabeln efter att programmet har startats måste redigeraren, IDE: n eller skalet som kör den stängas och laddas om för att komma åt variabeln.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objektmodell

Anomaly Detector-klienten är ett [AnomalyDetectorClient-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) som autentiserar till Azure med [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), som innehåller din nyckel. Klienten innehåller två metoder för avvikelseidentifiering: På en hel datauppsättning med [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)och på den senaste datapunkten med [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Tidsseriedata skickas som en serie [punkter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) i ett [begärandeobjekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) Objektet `Request` innehåller egenskaper för att beskriva data ([granularitet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) till exempel) och parametrar för avvikelseidentifiering. 

Avvikelsedetektorsvaret är antingen ett [WholeDetectResponse-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) eller [LastDetectResponse-objekt,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) beroende på vilken metod som används. 

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med klientbiblioteket Avvikelsedetektor för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Läsa in en tidsseriedatauppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela datauppsättningen](#detect-anomalies-in-the-entire-data-set) 
* [Identifiera avvikelsestatus för den senaste datapunkten](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du en klient med slutpunkten och nyckeln. Skapa ett [ApiKeyServiceClientCredentials-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) med nyckeln och använd det med slutpunkten för att skapa ett [AnomalyDetectorClient-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Läsa in tidsseriedata från en fil

Hämta exempeldata för den här snabbstarten från [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. Högerklicka på **Raw**i webbläsaren .
2. Klicka på **Spara länk som**.
3. Spara filen i programkatalogen som en CSV-fil.

Den här tidsseriedata är formaterad som en CSV-fil och skickas till API:et för avvikelsedetektor.

Skapa en ny metod som ska läsas i tidsseriedata och lägg till den i ett [begärandeobjekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) Anropa `File.ReadAllLines()` med filsökvägen och skapa en lista med [Point-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) och ta bort alla nya linjetecken. Extrahera värdena och separera datestamp från dess numeriska `Point` värde och lägg till dem i ett nytt objekt. 

Skapa `Request` ett objekt med poängserien `Granularity.Daily` och för datapunkternas [kornighet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (eller periodicitet).

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela datauppsättningen 

Skapa en metod för att anropa klientens [metoden WholeDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) med `Request` objektet och invänta svaret som ett [WholeDetectResponse-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) Om tidsserien innehåller avvikelser iterar du genom svarets [IsAnomaly-värden](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) `true`och skriver ut alla som är . Dessa värden motsvarar index för avvikande datapunkter, om några hittades.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelsestatus för den senaste datapunkten

Skapa en metod för att anropa klientens [Metoden LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) med `Request` objektet och invänta svaret som ett [LastDetectResponse-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) Kontrollera svarets [IsAnomaly-attribut](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) för att avgöra om den senaste datapunkten som skickades var en avvikelse eller inte. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `dotnet run` kommandot från programkatalogen.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
