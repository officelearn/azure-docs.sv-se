---
title: 'Snabbstart: Identifiera avvikelser i tidsseriedata med rest-APIn för avvikelsedetektorn och C #'
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
ms.openlocfilehash: b53fecad3655048a7b9d799134926b2730b16dae
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239101"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Snabbstart: Identifiera avvikelser i tidsseriedata med rest-APIn för avvikelsedetektorn och C # 

Använd den här snabbstarten för att börja använda API:et för avvikelsedetektorns två identifieringslägen för att identifiera avvikelser i tidsseriedata. Det här C#-programmet skickar två API-begäranden som innehåller JSON-formaterade tidsseriedata och får svaren.

| API-begäran                                        | Programutdata                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svaret som innehåller avvikelsestatus (och andra data) för varje datapunkt i tidsseriedata och positionerna för identifierade avvikelser. |
| Identifiera avvikelsestatus för den senaste datapunkten | JSON-svaret som innehåller avvikelsestatus (och andra data) för den senaste datapunkten i tidsseriedata.                                        |

 Även om det här programmet är skrivet i C#, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Du hittar källkoden för den här snabbstarten på [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Krav

- Alla utgåvor av [Visual Studio 2017 eller senare](https://visualstudio.microsoft.com/downloads/),
- En avvikelsedetektornyckel och slutpunkt
- [Json.NET](https://www.newtonsoft.com/json) framework, tillgänglig som ett NuGet-paket. Så här installerar du Newtonsoft.Json som NuGet-paket i Visual Studio:
    
    1. Högerklicka på projektet i **Solution Explorer**.
    2. Välj **Hantera NuGet-paket**.
    3. Sök efter *Newtonsoft.Json* och installera paketet.

- Om du använder Linux/MacOS kan det här programmet köras med [mono](https://www.mono-project.com/).

- En JSON-fil som innehåller tidsseriedatapunkter. Exempeldata för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Skapa en avvikelsedetektorresurs

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny konsollösning i Visual Studio och lägg till följande paket. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Skapa variabler för din prenumerationsnyckel och din slutpunkt. Nedan finns de URI:er som du kan använda för avvikelseidentifiering. Dessa läggs till i tjänstslutpunkten senare för att skapa URL:erna för API-begäran.

    | Identifieringsmetod                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Identifiering av batch                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Identifiering på den senaste datapunkten | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny async-funktion som anropas `Request` och som tar de variabler som skapats ovan.

2. Ange klientens säkerhetsprotokoll och huvudinformation med hjälp av ett `HttpClient` objekt. Var noga med att lägga `Ocp-Apim-Subscription-Key` till din prenumerationsnyckel i sidhuvudet. Skapa sedan `StringContent` ett objekt för begäran.

3. Skicka begäran `PostAsync()`med och returnera sedan svaret.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en ny `detectAnomaliesBatch()`funktion som heter . Konstruera begäran och skicka `Request()` den genom att anropa funktionen med slutpunkten, prenumerationsnyckeln, URL:en för identifiering av batchavvikelser och tidsseriedata.

2. Avserialisera JSON-objektet och skriv det till konsolen.

3. Om svaret innehåller `code` fält skriver du ut felkoden och felmeddelandet. 

4. Annars hittar du positionerna för avvikelser i datauppsättningen. Svarets `isAnomaly` fält innehåller en matris med booleska värden, som var och en anger om en datapunkt är en anomali. Konvertera detta till en strängmatris `ToObject<bool[]>()` med svarsobjektets funktion. Iterera genom matrisen och skriv `true` ut index för eventuella värden. Dessa värden motsvarar index för avvikande datapunkter, om några hittades.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelsestatus för den senaste datapunkten

1. Skapa en ny `detectAnomaliesLatest()`funktion som heter . Konstruera begäran och skicka `Request()` den genom att anropa funktionen med slutpunkten, prenumerationsnyckeln, URL:en för senaste punktavvikelseidentifiering och tidsseriedata.

2. Avserialisera JSON-objektet och skriv det till konsolen.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Ladda dina tidsseriedata och skicka begäran

1. I huvudmetoden för ditt program läser du `File.ReadAllText()`in dina JSON-tidsseriedata med . 

2. Anropa de avvikelseidentifieringsfunktioner som skapats ovan. Används `System.Console.ReadKey()` för att hålla konsolfönstret öppet när programmet har körts.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan om du vill visa JSON-svaret på GitHub:
* [Exempel på batchidentifieringssvar](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exempel på senaste punktidentifieringssvar](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
