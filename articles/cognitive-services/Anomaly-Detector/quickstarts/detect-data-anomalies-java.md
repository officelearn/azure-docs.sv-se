---
title: 'Snabbstart: Identifiera avvikelser i dina tidsseriedata med REST API:et för avvikelsedetektor och Java'
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
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239056"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Snabbstart: Identifiera avvikelser i dina tidsseriedata med REST API:et för avvikelsedetektor och Java

Använd den här snabbstarten för att börja använda API:et för avvikelsedetektorns två identifieringslägen för att identifiera avvikelser i tidsseriedata. Detta Java-program skickar två API-begäranden som innehåller JSON-formaterade tidsseriedata och får svaren.

| API-begäran                                        | Programutdata                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svaret som innehåller avvikelsestatus (och andra data) för varje datapunkt i tidsseriedata och positionerna för identifierade avvikelser. |
| Identifiera avvikelsestatus för den senaste datapunkten | JSON-svaret som innehåller avvikelsestatus (och andra data) för den senaste datapunkten i tidsseriedata.                                                                                                                                         |

 Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Du hittar källkoden för den här snabbstarten på [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Krav

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) eller senare.
- En avvikelsedetektornyckel och slutpunkt
- Importera dessa bibliotek från Maven-databasen
    - [JSON i Java-paketet](https://mvnrepository.com/artifact/org.json/json)
    - [Apache HttpClient-paket](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- En JSON-fil som innehåller tidsseriedatapunkter. Exempeldata för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Skapa en avvikelsedetektorresurs

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa ett nytt Java-projekt och importera följande bibliotek.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Skapa variabler för din prenumerationsnyckel och din slutpunkt. Nedan finns de URI:er som du kan använda för avvikelseidentifiering. Dessa läggs till i tjänstslutpunkten senare för att skapa URL:erna för API-begäran.

    |Identifieringsmetod  |URI  |
    |---------|---------|
    |Identifiering av batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Identifiering på den senaste datapunkten     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny `sendRequest()` funktion som kallas som tar de variabler som skapats ovan. Utför sedan följande steg.

2. Skapa `CloseableHttpClient` ett objekt som kan skicka begäranden till API:et. Skicka begäran till `HttpPost` ett begärandeobjekt genom att kombinera slutpunkten och url:en avvikelsedetektor.

3. Använd funktionen för `setHeader()` begäran för `Content-Type` att `application/json`ställa in huvudet på `Ocp-Apim-Subscription-Key` och lägg till prenumerationsnyckeln i sidhuvudet.

4. Använd begärans `setEntity()` funktion till de data som ska skickas.

5. Använd klientens `execute()` funktion för att skicka begäran `CloseableHttpResponse` och spara den i ett objekt.

6. Skapa `HttpEntity` ett objekt för att lagra svarsinnehållet. Hämta innehållet `getEntity()`med . Om svaret inte är tomt returnerar du det.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en `detectAnomaliesBatch()` metod som anropas för att identifiera avvikelser i data som en batch. Anropa `sendRequest()` den metod som skapats ovan med slutpunkt, url, prenumerationsnyckel och json-data. Hämta resultatet och skriv ut det på konsolen.

2. Om svaret innehåller `code` fält skriver du ut felkoden och felmeddelandet.

3. Annars hittar du positionerna för avvikelser i datauppsättningen. Svarets `isAnomaly` fält innehåller ett booleskt värde som relaterar till om en viss datapunkt är en avvikelse. Hämta JSON-matrisen och iterera igenom den `true` och skriv ut index över eventuella värden. Dessa värden motsvarar index för avvikande datapunkter, om några hittades.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelsestatus för den senaste datapunkten

Skapa en `detectAnomaliesLatest()` metod som anropas för att identifiera avvikelsestatus för den sista datapunkten i datauppsättningen. Anropa `sendRequest()` den metod som skapats ovan med slutpunkt, url, prenumerationsnyckel och json-data. Hämta resultatet och skriv ut det på konsolen.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Ladda dina tidsseriedata och skicka begäran

1. I huvudmetoden för ditt program, läs i JSON-filen som innehåller de data som kommer att läggas till i begäranden.

2. Anropa de två avvikelseidentifieringsfunktionerna som skapats ovan.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan om du vill visa JSON-svaret på GitHub:
* [Exempel på batchidentifieringssvar](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exempel på senaste punktidentifieringssvar](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
