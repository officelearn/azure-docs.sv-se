---
title: 'Snabbstart: Känner igen digitalt bläck med INK Recognizer REST API och Java'
titleSuffix: Azure Cognitive Services
description: Använd API:et för bläckreformat för att börja känna igen digitala pennstreck i den här snabbstarten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: d2cd4e56477ea39587ce318538c9ddd84c51b03b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448127"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Snabbstart: Känner igen digitalt bläck med INK Recognizer REST API och Java

Använd den här snabbstarten för att börja använda INK Recognizer-API:et på digitala pennstreck. Detta Java-program skickar en API-begäran som innehåller JSON-formaterade pennstreckdata och får svaret.

Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Vanligtvis anropar du API:et från en digital inking-app. Den här snabbstarten skickar pennstrecksdata för följande handskrivna exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Källkoden för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Krav

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) eller senare.

- Importera dessa bibliotek från Maven-databasen
    - [JSON i Java-paketet](https://mvnrepository.com/artifact/org.json/json)
    - [Apache HttpClient-paket](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Exempelvis pennstreckdata för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Skapa en resurs för pennanteckningskone igen

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Skapa variabler för din prenumerationsnyckel, slutpunkt och JSON-fil. Slutpunkten läggs senare till i bläckrebonader-URI:n.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny `sendRequest()` funktion som kallas som tar de variabler som skapats ovan. Utför sedan följande steg.

2. Skapa `CloseableHttpClient` ett objekt som kan skicka begäranden till API:et. Skicka begäran till `HttpPut` ett begärandeobjekt genom att kombinera slutpunkten och URL:en för bläckkonekänningsverktyget.

3. Använd funktionen för `setHeader()` begäran för `Content-Type` att `application/json`ställa in huvudet på `Ocp-Apim-Subscription-Key` och lägg till prenumerationsnyckeln i sidhuvudet.

4. Använd begärans `setEntity()` funktion till de data som ska skickas.   

5. Använd klientens `execute()` funktion för att skicka begäran `CloseableHttpResponse` och spara den i ett objekt. 

6. Skapa `HttpEntity` ett objekt för att lagra svarsinnehållet. Hämta innehållet `getEntity()`med . Om svaret inte är tomt returnerar du det.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Skicka en begäran om käckigerkänning

Skapa en `recognizeInk()` metod som kallas att känna igen dina pennstrecksdata. Anropa `sendRequest()` den metod som skapats ovan med slutpunkt, url, prenumerationsnyckel och json-data. Hämta resultatet och skriv ut det på konsolen.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Ladda dina digitala bläckdata och skicka begäran

1. I huvudmetoden för ditt program, läs i JSON-filen som innehåller de data som kommer att läggas till i begäranden.

2. Anropa den bläckigenkänningsfunktion som skapats ovan.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Kör programmet och visa svaret

Kör appen. Ett lyckat svar returneras i JSON-format. Du kan också hitta JSON-svaret på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://go.microsoft.com/fwlink/?linkid=2089907)


Om du vill se hur API:et för bläckigenkänning fungerar i en digital pennanteckningsapp kan du ta en titt på följande exempelprogram på GitHub:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
