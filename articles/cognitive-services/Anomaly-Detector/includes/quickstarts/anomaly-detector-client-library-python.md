---
title: Snabbstart för avvikelsedetektorns pythonklientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: a51025245e5b02b89126afd886bc89fb1cefdef4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759995"
---
Kom igång med klientbiblioteket Avvikelsedetektor för Python. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Med tjänsten Avvikelsedetektor kan du hitta avvikelser i dina tidsseriedata genom att automatiskt använda de bäst passande modellerna på den, oavsett bransch, scenario eller datavolym.

Använd klientbiblioteket avvikelsedetektor för Python för att:

* Identifiera avvikelser i hela tidsseriedatauppsättningen, som en batchbegäran
* Identifiera avvikelsestatus för den senaste datapunkten i tidsserien

[Biblioteksreferensdokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Bibliotekskodpaket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [(PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | Hitta[exempelkoden på GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* En avvikelsedetektornyckel och slutpunkt
* [Python 3.x](https://www.python.org/)
* [Pandas dataanalysbibliotek](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Inrätta

### <a name="create-an-anomaly-detector-resource"></a>Skapa en avvikelsedetektorresurs

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Skapa ett nytt pythonprogram

 Skapa en ny Python-fil och importera följande bibliotek.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Skapa variabler för din nyckel som en miljövariabel, sökvägen till en tidsseriedatafil och den azurblå platsen för din prenumeration. Till exempel `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Installera klientbiblioteket

När du har installerat Python kan du installera klientbiblioteket med:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objektmodell

Anomaly Detector-klienten är ett [AnomalyDetectorClient-objekt](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) som autentiserar till Azure med hjälp av nyckeln. Klienten innehåller två metoder för avvikelseidentifiering: På en hel datauppsättning med [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)och på den senaste datapunkten med [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Tidsseriedata skickas som en serie [punkter](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) i ett [begärandeobjekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) Objektet `Request` innehåller egenskaper för att beskriva data ([granularitet](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) till exempel) och parametrar för avvikelseidentifiering. 

Avvikelsedetektorsvaret är ett [LastDetectResponse-](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) eller [EntireDetectResponse-objekt](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) beroende på vilken metod som används. 

## <a name="code-examples"></a>Kodexempel 

Dessa kodavsnitt visar hur du gör följande med klientbiblioteket Avvikelsedetektor för Python:

* [Autentisera klienten](#authenticate-the-client)
* [Läsa in en tidsseriedatauppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela datauppsättningen](#detect-anomalies-in-the-entire-data-set) 
* [Identifiera avvikelsestatus för den senaste datapunkten](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till din azure-platsvariabel till slutpunkten och autentisera klienten med din nyckel.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Läsa in tidsseriedata från en fil

Hämta exempeldata för den här snabbstarten från [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. Högerklicka på **Raw**i webbläsaren .
2. Klicka på **Spara länk som**.
3. Spara filen i programkatalogen som en CSV-fil.

Den här tidsseriedata är formaterad som en CSV-fil och skickas till API:et för avvikelsedetektor.

Ladda din datafil med Pandas-bibliotekets `read_csv()` metod och gör en tom listvariabel för att lagra dina dataserier. Iterera genom filen och lägga till data som ett [Point-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) Det här objektet innehåller tidsstämpeln och det numeriska värdet från raderna i CSV-datafilen. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Skapa ett [begärandeobjekt](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) med tidsserierna och [detaljrikingen](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (eller periodiciteten) för dess datapunkter. Till exempel `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela datauppsättningen 

Anropa API:et för att identifiera avvikelser genom hela tidsseriedata med hjälp av klientens [entire_detect()-metod.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) Lagra det returnerade [EntireDetectResponse-objektet.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) Iterera genom svarslistan `is_anomaly` och skriv ut `true` index för eventuella värden. Dessa värden motsvarar index för avvikande datapunkter, om några hittades.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelsestatus för den senaste datapunkten

Anropa API:et för avvikelsedetektor för att avgöra om den senaste datapunkten är en avvikelse med klientens [last_detect()-metod](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) och lagra det returnerade [LastDetectResponse-objektet.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) Svarets `is_anomaly` värde är en boolesk som anger den punktens avvikelsestatus.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `python` kommandot och ditt filnamn.
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
