---
title: 'Snabb start: identifiera digitalt bläck med hand SKRIFTS tolken REST API och Java'
titleSuffix: Azure Cognitive Services
description: Använd pennan tecknings tolkens API och Java för att börja identifiera digitala penndrag i den här snabb starten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-java
ms.openlocfilehash: dea46a07a7357be6079c52634be8ea2ff79cc8f3
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369128"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Snabb start: identifiera digitalt bläck med hand SKRIFTS tolken REST API och Java

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Använd den här snabb starten för att börja använda pennan tecknings tolkens API på digitala penndrag. Det här Java-programmet skickar en API-begäran som innehåller JSON-formaterade penndrag och hämtar svaret.

Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Normalt anropar du API: et från en digital intecknings app. I den här snabb starten skickas Penn strecks data för följande handskrivna exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Du hittar käll koden för den här snabb starten på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Förutsättningar

- [Java &trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) eller senare.

- Importera de här biblioteken från maven-lagringsplatsen
    - [JSON i Java](https://mvnrepository.com/artifact/org.json/json) -paket
    - [Apache httpclient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) -paket

- Du hittar exempel Penn strecks data för den här snabb starten på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Skapa en tryck färgs igenkännings resurs

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Skapa variabler för din prenumerations nyckel, slut punkt och JSON-fil. Slut punkten läggs senare till i färg tolknings-URI: n.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begär Anden

1. Skapa en ny funktion `sendRequest()` som använder variablerna som skapats ovan. Utför sedan följande steg.

2. Skapa ett `CloseableHttpClient` objekt som kan skicka begär anden till API: et. Skicka begäran till ett `HttpPut` Request-objekt genom att kombinera slut punkten och färg tolknings-URL: en.

3. Använd begärans `setHeader()` funktion för att ange `Content-Type` rubriken till `application/json` och Lägg till din prenumerations nyckel i `Ocp-Apim-Subscription-Key` rubriken.

4. Använd begärans `setEntity()` funktion för att skicka de data som ska skickas.   

5. Använd klientens `execute()` funktion för att skicka begäran och spara den till ett `CloseableHttpResponse` objekt. 

6. Skapa ett `HttpEntity` objekt för att lagra svars innehållet. Hämta innehållet med `getEntity()` . Returnera det om svaret inte är tomt.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Skicka en begäran om hand SKRIFTS igenkänning

Skapa en metod `recognizeInk()` som kallas för att identifiera Penn strecks data. Anropa `sendRequest()` metoden som skapades ovan med din slut punkt, URL, prenumerations nyckel och JSON-data. Hämta resultatet och skriv ut det till-konsolen.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Läs in dina digitala pennan tecknings data och skicka begäran

1. I appens huvud metod läser du i JSON-filen som innehåller de data som ska läggas till i begäran.

2. Anropa funktionen för tryck färgs igenkänning som skapats ovan.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Kör programmet och Visa svaret

Kör appen. Ett lyckat svar returneras i JSON-format. Du kan också hitta JSON-svaret på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för REST-API](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)


Ta en titt på följande exempel program på GitHub för att se hur API: et för färg igenkänning fungerar i en digital inkungs app:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)