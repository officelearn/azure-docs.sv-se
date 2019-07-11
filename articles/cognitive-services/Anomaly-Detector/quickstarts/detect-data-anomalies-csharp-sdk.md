---
title: 'Snabbstart: Identifiera avvikelser i time series-data med hjälp av Avvikelseidentifiering detektor SDK för .NET'
titleSuffix: Azure Cognitive Services
description: Starta kunna identifiera avvikelser i tidsseriedata med Avvikelseidentifiering detektor-tjänsten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: a75196e035585a7501cdd842fb5b80ceff424dcc
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721571"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Snabbstart: Avvikelseidentifiering detektor klientbiblioteket för .NET

Kom igång med Avvikelseidentifiering detektor-klientbiblioteket för .NET. Följ dessa steg för att installera paketet och prova att använda kodexempel för grundläggande uppgifter. Avvikelseidentifiering detektor-tjänsten kan du hitta avvikelser i tidsseriedata med automatiskt med bäst passningsåtgärderna modeller, oavsett bransch, scenario eller datavolym.

Använd Avvikelseidentifiering detektor-klientbiblioteket för .NET för att:

* Identifiera avvikelser som en batch
* Identifiera avvikelser status för senaste datapunkt

[API-referensdokumentation](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [paketet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [exempel](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa ett kostnadsfritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Ställa in

### <a name="create-an-anomaly-detector-resource"></a>Skapa en Avvikelseidentifiering detektor-resurs

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-c-app"></a>Skapa en ny C# app

Skapa ett nytt .NET Core-program i önskat redigeringsprogram eller IDE. 

I ett konsolfönster (till exempel cmd, PowerShell eller Bash), använder du dotnet `new` kommando för att skapa en ny konsolapp med namnet `anomaly-detector-quickstart`. Det här kommandot skapar en enkel ”Hello World” C# projekt med en enskild källfil: **Program.CS**. 

```console
dotnet new console -n anomaly-detector-quickstart
```

Ändra katalogen till den nyligen skapade app-mappen. Du kan skapa program med:

```console
dotnet build
```

Resultatet bör innehålla några varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

I programkatalogen, installerar du Avvikelseidentifiering detektor-klientbiblioteket för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Om du använder Visual Studio IDE finns klientbiblioteket som NuGet-paket. 

## <a name="object-model"></a>Objektmodell

Avvikelseidentifiering detektor klienten är en [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) objekt som autentiserar till Azure med [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), som innehåller din nyckel. Klienten tillhandahåller två metoder för avvikelseidentifiering: På en hel datauppsättning med hjälp av [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync), och peka på den senaste informationen med hjälp av [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Time series-data skickas som en serie [punkter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) i en [begära](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) objekt. Den `Request` objekt innehåller egenskaper för att beskriva data ([kornighet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) till exempel), och parametrar för avvikelseidentifiering. 

Avvikelseidentifiering detektor svaret är antingen en [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) eller [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) objekt, beroende på vilken metod som används. 

## <a name="code-examples"></a>Kodexempel

Följande kodfragment visar hur du gör följande med Avvikelseidentifiering detektor-klientbibliotek för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Läsa in en time series-datauppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela datauppsättningen](#detect-anomalies-in-the-entire-data-set) 
* [Identifiera avvikelser status för senaste datapunkt](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="add-the-main-method"></a>Lägg till main-metoden

Från projektkatalogen:

1. Öppna filen Program.cs i önskat redigeringsprogram eller IDE
2. Lägg till följande `using` direktiv

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

> [!NOTE]
> Den här snabbstarten förutsätter vi att du har [skapas en miljövariabel](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för Avvikelseidentifiering detektor-nyckel med namnet `ANOMALY_DETECTOR_KEY`.

I programmets `main()` metod, Skapa variabler för din resurs Azure-plats och din nyckel som en miljövariabel. Om du har skapat miljövariabeln när appen startas behöver i redigeraren, IDE eller shell köra det vara stängd och in igen för att få åtkomst till variabeln.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

### <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod du skapa en instans av en klient med din slutpunkt och nyckel. Skapa en [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) objekt med din nyckel och använda den med din slutpunkt för att skapa en [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) objekt. 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
### <a name="load-time-series-data-from-a-file"></a>Läsa in time series-data från en fil

Ladda ned exempeldata för den här snabbstarten från [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. I webbläsaren, högerklickar du på **Raw**
2. Klicka på **Spara länk som**
3. Spara filen till programkatalogen, som en CSV-fil.

Den här time series-data är formaterade som en CSV-fil och skickas till API: T för Avvikelseidentifiering detektor.

Skapa en ny metod för att läsa i time series-data och lägger till den i en [begära](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) objekt. Anropa `File.ReadAllLines()` med sökväg och skapa en lista över [punkt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) objekt och alla nyare remsor rad tecken. Extraherar värdena och skilja tidsstämpel från dess numeriska värde och lägga till dem i en ny `Point` objekt. 

Gör en `Request` objekt med antal punkter, och `Granularity.Daily` för den [kornighet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (eller periodicitet) för datapunkter.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela datauppsättningen 

Skapa en metod för att anropa klientens [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metoden med den `Request` objekt och await svaret som en [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) objekt. Om tidsserien innehåller avvikelser, gå igenom svaret [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) värden och Skriv ut som är `true`. Dessa värden motsvarar index för avvikande datapunkter, om några.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelser status för senaste datapunkt

Skapa en metod för att anropa klientens [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metoden med den `Request` objekt och await svaret som en [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) objekt. Kontrollera svaret [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) attribut för att avgöra om den senaste datapunkten skickas var avvikelser eller inte. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Köra programmet

Köra programmet med dotnet `run` från programkatalogen.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Ta bort resursgruppen raderas även andra resurser som är associerade med resursgruppen.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Du kan också köra följande cloud shell-kommando att ta bort resursgruppen och dess kopplade resurser. Det kan ta några minuter att slutföra. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Avvikelseidentifiering med Azure Databricks för direktuppspelning](../tutorials/anomaly-detection-streaming-databricks.md)

* Vad är den [Avvikelseidentifiering detektor API?](../overview.md)
* [Bästa praxis](../concepts/anomaly-detection-best-practices.md) när du använder API: T för Avvikelseidentifiering detektor.
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
