---
title: 'Snabbstart: Identifiera dataavvikelser med klientbiblioteket avvikelsedetektor för Python'
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
ms.openlocfilehash: c13657b8f2dae3868c1b9820236585aa930c6be4
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239090"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Snabbstart: Klientbibliotek för avvikelsedetektor för Node.js

Kom igång med klientbiblioteket avvikelsedetektor för Node.js. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Med tjänsten Avvikelsedetektor kan du hitta avvikelser i dina tidsseriedata genom att automatiskt använda de bäst passande modellerna på den, oavsett bransch, scenario eller datavolym.

Använd klientbiblioteket avvikelsedetektor för Node.js för att:

* Identifiera avvikelser i hela tidsseriedatauppsättningen, som en batchbegäran
* Identifiera avvikelsestatus för den senaste datapunkten i tidsserien

[Referensdokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [källkodspaket (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [Hitta koden på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node.js](https://nodejs.org/)
* En avvikelsedetektornyckel och slutpunkt

## <a name="setting-up"></a>Inrätta

### <a name="create-an-anomaly-detector-azure-resource"></a>Skapa en Azure-resurs för avvikelsedetektor

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den. 

```console
mkdir myapp && cd myapp
```

Kör `npm init` kommandot för att skapa ett `package.json` nodprogram med en fil. 

```console
npm init
```

Skapa en `index.js` fil med namnet och importera följande bibliotek:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Skapa variabler som resursens Azure-slutpunkt och -nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln. Skapa en annan variabel för exempeldatafilen som du hämtar i ett senare steg och en tom lista för datapunkterna. Skapa sedan `ApiKeyCredentials` ett objekt som innehåller nyckeln.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera `ms-rest-azure` paketen och `azure-cognitiveservices-anomalydetector` NPM-paketen. Csv-parse-biblioteket används också i den här snabbstarten:

```console
npm install  @azure/cognitiveservices-anomalydetector @azure/ms-rest-js csv-parse
```

Appens `package.json` fil uppdateras med beroenden.

## <a name="object-model"></a>Objektmodell

Anomaly Detector-klienten är ett [AnomalyDetectorClient-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) som autentiserar till Azure med hjälp av nyckeln. Klienten innehåller två metoder för avvikelseidentifiering: På en hel datauppsättning med [helaDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)och på den senaste datapunkten med [LastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Tidsseriedata skickas som serie [punkter](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) i ett [begärandeobjekt.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) Objektet `Request` innehåller egenskaper för att beskriva data ([granularitet](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) till exempel) och parametrar för avvikelseidentifiering. 

Avvikelsedetektorsvaret är ett [LastDetectResponse-](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) eller [EntireDetectResponse-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) beroende på vilken metod som används. 

## <a name="code-examples"></a>Kodexempel 

Dessa kodavsnitt visar hur du gör följande med klientbiblioteket Avvikelsedetektor för Node.js:

* [Autentisera klienten](#authenticate-the-client)
* [Läsa in en tidsseriedatauppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela datauppsättningen](#detect-anomalies-in-the-entire-data-set) 
* [Identifiera avvikelsestatus för den senaste datapunkten](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera ett [AnomalyDetectorClient-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) med slutpunkten och autentiseringsuppgifterna.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Läsa in tidsseriedata från en fil

Hämta exempeldata för den här snabbstarten från [GitHub:](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv)
1. Högerklicka på **Raw**i webbläsaren .
2. Klicka på **Spara länk som**.
3. Spara filen i programkatalogen som en CSV-fil.

Den här tidsseriedata är formaterad som en CSV-fil och skickas till API:et för avvikelsedetektor.

Läs din datafil med csv-parse-bibliotekets `readFileSync()` metod och tolka `parse()`filen med . För varje rad trycker du på ett [Point-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) som innehåller tidsstämpeln och det numeriska värdet.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela datauppsättningen 

Anropa API:et för att identifiera avvikelser genom hela tidsserien som en batch med klientens [helaDetect()-metod.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) Lagra det returnerade [EntireDetectResponse-objektet.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) Iterera genom svarslistan `isAnomaly` och skriv ut `true` index för eventuella värden. Dessa värden motsvarar index för avvikande datapunkter, om några hittades.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelsestatus för den senaste datapunkten

Anropa API:et för avvikelsedetektor för att avgöra om den senaste datapunkten är en avvikelse med klientens [metod lastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) och lagra det returnerade [LastDetectResponse-objektet.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) Svarets `isAnomaly` värde är en boolesk som anger den punktens avvikelsestatus.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `node` kommandot på snabbstartsfilen.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
