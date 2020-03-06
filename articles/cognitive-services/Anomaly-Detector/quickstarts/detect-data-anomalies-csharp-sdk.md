---
title: 'Snabb start: identifiera avvikelser i Time Series-data med hjälp av klient biblioteket för avvikelse detektor för .NET'
titleSuffix: Azure Cognitive Services
description: 'Den här snabb starten visar hur du använder API: t för avvikelse detektor för att identifiera avvikelser i din data serie antingen som en batch eller vid strömmande data.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: fdb35edc35e07ed4ee718281942565a8f1d061d4
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402685"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Snabb start: klient bibliotek för avvikelse detektor för .NET

Kom igång med klient biblioteket för avvikelse detektor för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Med tjänsten avvikelse detektor kan du hitta avvikelser i dina Time Series-data genom att automatiskt använda de bästa passnings modellerna, oavsett bransch, scenario eller data volym.

Använd klient biblioteket för avvikelse detektor för .NET för att:

* Identifiera avvikelser i data uppsättningen för tids serier, som en batch-begäran
* Identifiera avvikelse status för den senaste data punkten i din tids serie

[Dokumentation för biblioteks referens](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [bibliotekets källkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [hitta koden på GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core)
* En avvikelse nyckel och slut punkt

## <a name="setting-up"></a>Konfigurera

### <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du kommandot `dotnet new` för att skapa en ny konsol-app med namnet `anomaly-detector-quickstart`. Det här kommandot skapar ett enkelt "Hello World"-projekt med C# en enda käll fil: *program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```dotnetcli
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du klient biblioteket för avvikelse detektor för .NET med följande kommando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Öppna *program.cs* -filen från projekt katalogen och Lägg till följande med hjälp av `directives`:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

I programmets `main()` metod skapar du variabler för resursens Azure-plats och din nyckel som en miljö variabel. Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE eller gränssnittet som kör den stängas och läsas in igen för att få åtkomst till variabeln.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objekt modell

Klienten för avvikelse detektor är ett [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) -objekt som autentiserar till Azure med hjälp av [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), som innehåller din nyckel. Klienten erbjuder två metoder för avvikelse identifiering: på en hel data uppsättning med hjälp av [EntireDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)och den senaste data punkten med [LastDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Time Series-data skickas som en serie [punkter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) i ett [Request](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) -objekt. `Request`-objektet innehåller egenskaper för att beskriva data (till exempel[granularitet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) ) och parametrar för avvikelse identifiering. 

Avvikelse detektorns svar är antingen ett [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) -eller [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) -objekt, beroende på vilken metod som används. 

## <a name="code-examples"></a>Kod exempel

Dessa kodfragment visar hur du gör följande med klient biblioteket för avvikelse detektor för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Läs in en tids serie data uppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela data uppsättningen](#detect-anomalies-in-the-entire-data-set) 
* [Identifiera avvikelse statusen för den senaste data punkten](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du en klient med din slut punkt och nyckel. Skapa ett [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) -objekt. 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Läsa in tids serie data från en fil

Hämta exempel data för den här snabb starten från [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Högerklicka på **RAW**i webbläsaren.
2. Klicka på **Spara länk som**.
3. Spara filen i program katalogen som en CSV-fil.

Denna tids serie data formateras som en. csv-fil och skickas till API: t för avvikelse identifiering.

Skapa en ny metod för att läsa tids serie data och Lägg till dem i ett [Request](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) -objekt. Anropa `File.ReadAllLines()` med sökvägen till filen och skapa en lista med [punkt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) objekt och ta bort alla nya rad tecken. Extrahera värdena och avgränsa datestamp från det numeriska värdet och Lägg till dem i ett nytt `Point`-objekt. 

Skapa ett `Request` objekt med en serie punkter och `Granularity.Daily` för data punkternas [granularitet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (eller periodicitet).

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela data uppsättningen 

Skapa en metod för att anropa klientens [EntireDetectAsync ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metod med `Request`-objektet och väntar svaret som ett [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) -objekt. Om tids serien innehåller avvikelser itererar du genom svarets [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) -värden och skriver ut de som är `true`. Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

Skapa en metod för att anropa klientens [LastDetectAsync ()-](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metod med `Request`-objektet och väntar svaret som ett [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) -objekt. Kontrol lera svarets [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) -attribut för att avgöra om den senaste data punkten som skickats var en avvikelse eller inte. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Köra programmet

Kör programmet med kommandot `dotnet run` från program katalogen.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
