---
title: 'Snabb start: identifiera data avvikelser med hjälp av klient biblioteket för avvikelse detektor för python'
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
ms.openlocfilehash: 0493f9e4b45d8d4804d1933bb923d3483b87005e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448982"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Snabb start: klient bibliotek för avvikelse detektor för python

Kom igång med klient biblioteket för avvikelse detektor för python. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Med tjänsten avvikelse detektor kan du hitta avvikelser i dina Time Series-data genom att automatiskt använda de bästa passnings modellerna, oavsett bransch, scenario eller data volym.

Använd klient biblioteket för avvikelse detektor för python för att:

* Identifiera avvikelser i data uppsättningen för tids serier, som en batch-begäran
* Identifiera avvikelse status för den senaste data punkten i din tids serie

[Dokumentation om biblioteks referens](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [hitta exempel koden på GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* En avvikelse nyckel och slut punkt
* [Python 3.x](https://www.python.org/)
* [Pandas data analys bibliotek](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Konfigurera

### <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

 Skapa en ny python-fil och importera följande bibliotek.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Skapa variabler för din nyckel som en miljö variabel, sökvägen till en tids serie data fil och Azure-platsen för din prenumeration. Till exempel `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera klient biblioteket med:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objekt modell

Klienten för avvikelse detektor är ett [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) -objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller två metoder för avvikelse identifiering: på en hel data uppsättning med hjälp av [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)och på den senaste data punkten med [Last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Time Series-data skickas som en serie [punkter](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) i ett [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) -objekt. `Request` Objektet innehåller egenskaper för att beskriva data och parametrar för avvikelse identifiering.[(till exempel granularitet)](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) 

Avvikelse detektorns svar är ett [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) -eller [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) -objekt beroende på vilken metod som används. 

## <a name="code-examples"></a>Kodexempel 

Dessa kodfragment visar hur du gör följande med klient biblioteket för avvikelse detektor för python:

* [Autentisera klienten](#authenticate-the-client)
* [Läs in en tids serie data uppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela data uppsättningen](#detect-anomalies-in-the-entire-data-set) 
* [Identifiera avvikelse statusen för den senaste data punkten](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till din Azure location-variabel i slut punkten och autentisera klienten med din nyckel.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Läsa in tids serie data från en fil

Hämta exempel data för den här snabb starten från [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Högerklicka på **RAW**i webbläsaren.
2. Klicka på **Spara länk som**.
3. Spara filen i program katalogen som en CSV-fil.

Denna tids serie data formateras som en. csv-fil och skickas till API: t för avvikelse identifiering.

Läs in data filen med Pandas-bibliotekets `read_csv()` metod och gör en tom List variabel för att lagra data serien. Iterera igenom filen och Lägg till data som ett [punkt](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) objekt. Det här objektet kommer att innehålla tidstämpeln och det numeriska värdet från raderna i CSV-datafilen. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Skapa ett [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) -objekt med din tids serie och [granularitet](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (eller periodiciteten) för dess data punkter. Till exempel `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela data uppsättningen 

Anropa API: et för att identifiera avvikelser genom hela tids serie data med hjälp av klientens [entire_detect ()-](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metod. Lagra det returnerade [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) -objektet. Upprepa med svarets `is_anomaly` lista och skriv ut indexet för alla `true`s värden. Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

Anropa API: t för avvikelse detektor för att avgöra om den senaste data punkten är en avvikelse med hjälp av klientens [last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) -Metod och lagra det returnerade [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) -objektet. Svarets `is_anomaly` värde är ett booleskt värde som anger den punktens avvikelse status.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Köra programmet

Kör programmet med kommandot `python` och ditt fil namn.
 
[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
