---
title: Snabb start för avvikelse detektor python-klient bibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: c06cb7143756eed3d50fe1d2d03ce7ba8d6d9d4d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371814"
---
Kom igång med klient biblioteket för avvikelse detektor för python. Följ de här stegen för att installera paket starten med hjälp av algoritmerna som tillhandahålls av tjänsten. Med tjänsten avvikelse detektor kan du hitta avvikelser i dina Time Series-data genom att automatiskt använda de bästa passnings modellerna, oavsett bransch, scenario eller data volym.

Använd klient biblioteket för avvikelse detektor för python för att:

* Identifiera avvikelser i din tids serie data uppsättning som en batch-begäran
* Identifiera avvikelse status för den senaste data punkten i din tids serie
* Identifiera trend ändrings punkter i din data uppsättning.

Dokumentation om biblioteks [referens](https://go.microsoft.com/fwlink/?linkid=2090370)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector)  |  [Paket (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/)  |  [Hitta exempel koden på GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Förutsättningar

* [Python 3.x](https://www.python.org/)
* [Pandas data analys bibliotek](https://pandas.pydata.org/)
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" skapar du en avvikelse detektor resurs "  target="_blank"> skapa en avvikelse <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i den Azure Portal för att hämta nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API: t för avvikelse identifiering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.


## <a name="setting-up"></a>Konfigurera

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

 Skapa en ny python-fil och importera följande bibliotek.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Skapa variabler för din nyckel som en miljö variabel, sökvägen till en tids serie data fil och Azure-platsen för din prenumeration. Exempelvis `westus2`.

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera klient biblioteket med:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Objekt modell

Klienten för avvikelse detektor är ett [AnomalyDetectorClient](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) -objekt som autentiserar till Azure med hjälp av din nyckel. Klienten kan utföra avvikelse identifiering av en hel data uppsättning med hjälp av [entire_detect ()](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)eller på den senaste data punkten med [Last_detect ()](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). Funktionen [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090370) identifierar punkter som markerar ändringar i en trend.

Time Series-data skickas som en serie [punkter](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) i ett [Request](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) -objekt. `Request`Objektet innehåller egenskaper för att beskriva data (till exempel[granularitet](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) ) och parametrar för avvikelse identifiering.

Avvikelse detektorns svar är ett [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python)-, [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)-eller [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) -objekt beroende på vilken metod som används.

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du gör följande med klient biblioteket för avvikelse detektor för python:

* [Autentisera klienten](#authenticate-the-client)
* [Läs in en tids serie data uppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela data uppsättningen](#detect-anomalies-in-the-entire-data-set)
* [Identifiera avvikelse statusen för den senaste data punkten](#detect-the-anomaly-status-of-the-latest-data-point)
* [Identifiera ändrings punkterna i data uppsättningen](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till din Azure location-variabel i slut punkten och autentisera klienten med din nyckel.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Läsa in tids serie data från en fil

Hämta exempel data för den här snabb starten från [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Högerklicka på **RAW** i webbläsaren.
2. Klicka på **Spara länk som**.
3. Spara filen i program katalogen som en CSV-fil.

Denna tids serie data formateras som en. csv-fil och skickas till API: t för avvikelse identifiering.

Läs in data filen med Pandas-bibliotekets `read_csv()` metod och gör en tom List variabel för att lagra din data serie. Iterera igenom filen och Lägg till data som ett [punkt](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) objekt. Det här objektet kommer att innehålla tidstämpeln och det numeriska värdet från raderna i CSV-datafilen.

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Skapa ett [Request](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) -objekt med din tids serie och [granularitet](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (eller periodiciteten) för dess data punkter. Exempelvis `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela data uppsättningen

Anropa API: et för att identifiera avvikelser genom hela tids serie data med hjälp av klientens [entire_detect ()-](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metod. Lagra det returnerade [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) -objektet. Upprepa i svars `is_anomaly` listan och skriv ut indexet för alla `true` värden. Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

Anropa API: t för avvikelse detektor för att avgöra om den senaste data punkten är en avvikelse med hjälp av klientens [last_detect ()](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) -Metod och lagra det returnerade [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) -objektet. Svarets `is_anomaly` värde är ett booleskt värde som anger den punktens avvikelse status.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Identifiera ändrings punkter i data uppsättningen

Anropa API: et för att identifiera ändrings punkter i tids serie data med hjälp av klientens [detect_change_point ()-](https://go.microsoft.com/fwlink/?linkid=2090370) metod. Lagra det returnerade [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) -objektet. Upprepa i svars `is_change_point` listan och skriv ut indexet för alla `true` värden. Dessa värden motsvarar indexen för trend ändrings punkter, om de påträffades.

[!code-python[detect change points](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=changePointDetection)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `python` kommandot och ditt fil namn.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]