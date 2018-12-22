---
title: Hämta avsikt, Go
titleSuffix: Language Understanding - Azure Cognitive Services
description: I den här Go-snabbstarten använder du en tillgänglig offentlig LUIS-app för att fastställa användarens avsikt i konversationstext.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9768e7945a94c5b28a1f28ea0264108cd47865d6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104721"
---
# <a name="quickstart-get-intent-using-go"></a>Snabbstart: Hämta avsikt med Go

I den här snabbstarten överför du yttranden till en LUIS-slutpunkt och få avsikt och entiteter tillbaka.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Programmeringsspråket [Go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)
* Offentligt app-ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Hämta avsikter i en webbläsare

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Hämta avsikter programmatiskt

Du kan använda Go för att komma åt samma resultat som du såg i webbläsarfönstret i föregående steg. 

1. Skapa en ny fil med namnet `endpoint.go`. Lägg till följande kod:
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. Med en kommandotolk i samma katalog som där du skapade filen anger du `go build endpoint.go` för att kompilera Go-filen. Kommandotolken returnerar inte någon information för ett lyckat bygge.

3. Kör Go-programmet från kommandoraden genom att ange följande text i kommandotolken: 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Ersätt `<add-your-key>` med värdet för nyckeln.  
    
    Svaret från kommandotolken är: 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```
    
## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Rensa resurser
Stäng Go-filen och ta bort den från filsystemet. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lägg till yttranden](luis-get-started-go-add-utterance.md)