---
title: Hämta avsikt, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: I den här Java-snabbstarten använder du en tillgänglig offentlig LUIS-app för att fastställa användarens avsikt i konversationstext.
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c55721e803b85c536067a90019f75dd5aa0f7845
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085356"
---
# <a name="quickstart-get-intent-using-java"></a>Snabbstart: Hämta avsikt med Java

I den här snabbstarten överför du yttranden till en LUIS-slutpunkt och få avsikt och entiteter tillbaka.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Nödvändiga komponenter

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* Offentligt app-ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Hämta avsikter i en webbläsare

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Hämta avsikter programmatiskt 

Du kan använda Java för att komma åt samma resultat som du såg i webbläsarfönstret i föregående steg. 

1. Kopiera följande kod för att skapa en klass i en fil som heter `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Ersätt värdet för variabeln `YOUR-KEY` med din LUIS-nyckel.

3. Kompilera Java-programmet med `javac -cp ":lib/*" LuisGetRequest.java`. 

4. Kör programmet med `java -cp ":lib/*" LuisGetRequest.java`. Det visar samma JSON som du såg tidigare i webbläsarfönstret.

    ![Konsolfönstret visar JSON-resultat från LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort Java-filen. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lägg till yttranden](luis-get-started-java-add-utterance.md)