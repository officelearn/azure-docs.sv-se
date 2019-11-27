---
title: 'Snabb start: identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektor REST API och Java'
titleSuffix: Azure Cognitive Services
description: 'Använd API: t för avvikelse detektor för att identifiera avvikelser i din data serie antingen som en batch eller vid strömmande data.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 6b79470194c5e8dc9b8d51f8b528cb4e51f7daf2
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483009"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Snabb start: identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektor REST API och Java

Använd den här snabb starten för att börja använda de två identifierings lägena för avvikelse detektor API: erna för att identifiera avvikelser i dina tids serie data. Java-programmet skickar två API-begäranden som innehåller JSON-formaterade Time Series-data och hämtar svaren.

| API-begäran                                        | Programutdata                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svaret som innehåller avvikelse status (och andra data) för varje data punkt i tids serie data och positionerna för identifierade avvikelser. |
| Identifiera avvikelse statusen för den senaste data punkten | JSON-svaret som innehåller avvikelse status (och andra data) för den senaste data punkten i tids serie data.                                                                                                                                         |

 Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Du hittar käll koden för den här snabb starten på [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Krav

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) eller senare.
- En avvikelse nyckel och slut punkt
- Importera de här biblioteken från maven-lagringsplatsen
    - [JSON i Java](https://mvnrepository.com/artifact/org.json/json) -paket
    - [Apache httpclient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) -paket

- En JSON-fil som innehåller tids serie data punkter. Exempel data för den här snabb starten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa ett nytt Java-projekt och importera följande bibliotek.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Skapa variabler för din prenumerations nyckel och din slut punkt. Nedan visas de URI: er som du kan använda för avvikelse identifiering. Dessa kommer att läggas till i tjänst slut punkten senare för att skapa API-begärandena URL: er.

    |Identifierings metod  |URI  |
    |---------|---------|
    |Batch-identifiering    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Identifiering på den senaste data punkten     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begär Anden

1. Skapa en ny funktion med namnet `sendRequest()` som använder variablerna som skapats ovan. Utför sedan följande steg.

2. Skapa ett `CloseableHttpClient`-objekt som kan skicka begär anden till API: et. Skicka begäran till ett `HttpPost` begär ande objekt genom att kombinera slut punkten och en URL för avvikelse identifiering.

3. Använd begärans `setHeader()` funktion för att ange `Content-Type`s rubriken till `application/json`och Lägg till din prenumerations nyckel i `Ocp-Apim-Subscription-Key`-rubriken.

4. Använd begärans `setEntity()` funktion för att skicka de data som ska skickas.

5. Använd klientens `execute()` funktion för att skicka begäran och spara den till ett `CloseableHttpResponse`-objekt.

6. Skapa ett `HttpEntity`-objekt för att lagra svars innehållet. Hämta innehållet med `getEntity()`. Returnera det om svaret inte är tomt.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en metod som kallas `detectAnomaliesBatch()` för att identifiera avvikelser i alla data som en batch. Anropa metoden `sendRequest()` som skapats ovan med din slut punkt, URL, prenumerations nyckel och JSON-data. Hämta resultatet och skriv ut det till-konsolen.

2. Om svaret innehåller `code` fältet, skriver du ut felkoden och fel meddelandet.

3. Annars hittar du positionerna för avvikelser i data uppsättningen. Svarets `isAnomaly` fält innehåller ett booleskt värde som relaterar till om en viss data punkt är en avvikelse. Hämta JSON-matrisen och upprepa den genom att skriva ut indexet för `true` värden. Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

Skapa en metod med namnet `detectAnomaliesLatest()` för att identifiera avvikelse statusen för den sista data punkten i data uppsättningen. Anropa metoden `sendRequest()` som skapats ovan med din slut punkt, URL, prenumerations nyckel och JSON-data. Hämta resultatet och skriv ut det till-konsolen.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Läs in dina Time Series-data och skicka begäran

1. I appens huvud metod läser du i JSON-filen som innehåller de data som ska läggas till i begäran.

2. Anropa de två avvikelse identifierings funktionerna som skapats ovan.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan om du vill visa JSON-svaret på GitHub:
* [Exempel svar för batch-identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exempel svar på senaste Poäng identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
