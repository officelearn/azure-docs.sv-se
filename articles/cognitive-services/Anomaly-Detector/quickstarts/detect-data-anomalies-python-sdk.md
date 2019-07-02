---
title: 'Snabbstart: Identifiera avvikelser för data med hjälp av Avvikelseidentifiering detektor bibliotek och Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: 'Använda API: T för Avvikelseidentifiering detektor för att identifiera avvikelser i dina dataserien som en batch eller på strömmande data.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 1d89ed8f40547142d41af9c587fc8fc000fa4dd9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503679"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Snabbstart: Avvikelseidentifiering detektor klientbibliotek för Python

Kom igång med Avvikelseidentifiering detektor-klientbiblioteket för .NET. Följ dessa steg för att installera paketet och prova att använda kodexempel för grundläggande uppgifter. Avvikelseidentifiering detektor-tjänsten kan du hitta avvikelser i tidsseriedata med automatiskt med bäst passningsåtgärderna modeller, oavsett bransch, scenario eller datavolym.

## <a name="key-concepts"></a>Viktiga begrepp

Använd Avvikelseidentifiering detektor-klientbiblioteket för Python för att:

* Identifiera avvikelser i hela din time series datauppsättning som en batchbegäran
* Identifiera avvikelser status för den senaste tidpunkten för data i din tidsserier

[Referensdokumentation för biblioteket](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [paketet (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [exempel](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa ett kostnadsfritt](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Den [Pandas data analysbibliotek](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Ställa in

### <a name="create-an-anomaly-detector-resource"></a>Skapa en Avvikelseidentifiering detektor-resurs

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>Installera klientbiblioteket

När du har installerat Python, kan du installera klientbiblioteket med:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objektmodell

Avvikelseidentifiering detektor klienten är en [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller två metoder för avvikelseidentifiering: På en hel datauppsättning med hjälp av [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-), och peka på den senaste informationen med hjälp av [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Time series-data skickas som en serie [punkter](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) i en [begära](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) objekt. Den `Request` objekt innehåller egenskaper för att beskriva data ([kornighet](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) till exempel), och parametrar för avvikelseidentifiering. 

Avvikelseidentifiering detektor svaret är ett [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) eller [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objekt beroende på vilken metod som används. 

## <a name="getting-started"></a>Komma igång

Skapa ett nytt Python-program i önskat redigeringsprogram eller IDE. Lägg sedan till följande import förklaringar till din fil. 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> Den här snabbstarten förutsätter vi att du har [skapas en miljövariabel](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för Avvikelseidentifiering detektor-nyckel med namnet `ANOMALY_DETECTOR_KEY`.

Skapa variabler för din nyckel som en miljövariabel, sökvägen till en time series-datafil och azure-plats för din prenumeration. Till exempel `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>Kodexempel 

Följande kodfragment visar hur du gör följande med Avvikelseidentifiering detektor-klientbibliotek för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Läsa in en time series-datauppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela datauppsättningen](#detect-anomalies-in-the-entire-data-set) 
* [Identifiera avvikelser status för senaste datapunkt](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till din azure-plats-variabel till slutpunkten och autentisera klienten med din nyckel.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Läsa in time series-data från en fil

Ladda ned exempeldata för den här snabbstarten från [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. I webbläsaren, högerklickar du på **Raw**
2. Klicka på **Spara länk som**
3. Spara filen till programkatalogen, som en CSV-fil.

Den här time series-data är formaterade som en CSV-fil och skickas till API: T för Avvikelseidentifiering detektor.

Läser in datafilen med Pandas-biblioteket `read_csv()` metoden och gör en tom lista variabel att lagra dina dataserie. Gå igenom filen och lägga till data som en [punkt](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) objekt. Det här objektet innehåller tidsstämpel och numeriska värden från rader i CSV-filen. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Skapa en [begära](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) objekt med din tidsserier och [kornighet](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (eller periodicitet) av datapunkterna. Till exempel `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela datauppsättningen 

Anropa API för att identifiera avvikelser via den hela time series-data med hjälp av klientens [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metod. Store den returnerade [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objekt. Gå igenom svaret `is_anomaly` lista och skriva ut index för någon `true` värden. Dessa värden motsvarar index för avvikande datapunkter, om några.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelser status för senaste datapunkt

Anropa Avvikelseidentifiering detektor API för att avgöra om din senaste datapunkt är ett fel med klientens [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) metod och lagra den returnerade [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) objekt. Svarets `is_anomaly` värdet är ett booleskt värde som anger status för den återställningspunkt avvikelseidentifiering.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Köra programmet

Kör programmet i din IDE eller i kommandoraden med den `python` kommandot och ditt filnamn.
 
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
