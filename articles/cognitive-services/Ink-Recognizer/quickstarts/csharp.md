---
title: 'Snabbstart: Känner igen digitalt bläck med INK Recognizer REST API och C #'
titleSuffix: Azure Cognitive Services
description: Den här snabbstarten visar hur du använder API:et för bläckre recognizeer för att börja känna igen digitala pennstreck.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: c24d055f1904453d2f512a278f00e23c6fea1d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371383"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Snabbstart: Känner igen digitalt bläck med INK Recognizer REST API och C #

Använd den här snabbstarten för att börja skicka digitala pennstreck till INK Recognizer API. Det här C#-programmet skickar en API-begäran som innehåller JSON-formaterade pennstrecksdata och hämtar svaret.

Även om det här programmet är skrivet i C#, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Vanligtvis anropar du API:et från en digital inking-app. Den här snabbstarten skickar pennstrecksdata för följande handskrivna exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Källkoden för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Krav

- Valfri version av [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Så här installerar du Newtonsoft.Json som NuGet-paket i Visual studio:
        1. Högerklicka på **Solution Manager**
        2. Klicka på **Hantera NuGet-paket...**
        3. Sök `Newtonsoft.Json` efter och installera paketet
- Om du använder Linux/MacOS kan det här programmet kördes med [Mono](https://www.mono-project.com/).

- Exempelvis pennstreckdata för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Skapa en resurs för pennanteckningskone igen

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny konsollösning i Visual Studio och lägg till följande paket. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Skapa variabler för din prenumerationsnyckel och slutpunkt och exempelfilen JSON. Slutpunkten kombineras senare med `inkRecognitionUrl` för att komma åt API:et. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny async-funktion som anropas `Request` och som tar de variabler som skapats ovan.

2. Ange klientens säkerhetsprotokoll och huvudinformation med hjälp av ett `HttpClient` objekt. Var noga med att lägga `Ocp-Apim-Subscription-Key` till din prenumerationsnyckel i sidhuvudet. Skapa sedan `StringContent` ett objekt för begäran.
 
3. Skicka begäran `PutAsync()`med . Om begäran lyckas returnerar du svaret.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Skicka en begäran om käckigerkänning

1. Skapa en ny `recognizeInk()`funktion som heter . Konstruera begäran och skicka `Request()` den genom att anropa funktionen med din slutpunkt, prenumerationsnyckel, URL:en för API:et och data för digital bläcklinje.

2. Avserialisera JSON-objektet och skriv det till konsolen. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Ladda dina digitala bläckdata

Skapa en `LoadJson()` funktion som anropas för att läsa in bläckdata JSON-filen. Använd `StreamReader` a `JsonTextReader` och `JObject` för att skapa en och returnera den.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Skicka API-begäran

1. I huvudmetoden för ditt program, ladda dina JSON-data med den funktion som skapats ovan. 

2. Anropa `recognizeInk()` funktionen som skapats ovan. Används `System.Console.ReadKey()` för att hålla konsolfönstret öppet när programmet har körts.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Kör programmet och visa svaret

Kör appen. Ett lyckat svar returneras i JSON-format. Du kan också hitta JSON-svaret på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://go.microsoft.com/fwlink/?linkid=2089907)


Om du vill se hur API:et för bläckigenkänning fungerar i en digital pennanteckningsapp kan du ta en titt på följande exempelprogram på GitHub:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
