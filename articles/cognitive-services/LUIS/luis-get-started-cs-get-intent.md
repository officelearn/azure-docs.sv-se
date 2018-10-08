---
title: Snabbstart i C# – förutspå avsikter – LUIS
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten använder du en tillgänglig offentlig LUIS-app för att fastställa användarens avsikt i konversationstext. Skicka med hjälp av C# användarens avsikt som text till den offentliga appens HTTP-slutpunkt för förutsägelse. Vid slutpunkten tillämpar LUIS den offentliga appens modell för att analysera betydelsen av text med naturligt språk, och fastställa den övergripande avsikten och extrahera data som är relevanta för appens ämnesdomän.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 51c23029cc771db5351575ce329944a9f06dd286
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035852"
---
# <a name="quickstart-get-intent-using-c"></a>Snabbstart: Hämta avsikter med hjälp av C#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Programmeringsspråket C# (ingår i VS Community 2017)
* Offentlig app-ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Hämta avsikter i en webbläsare

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Hämta avsikter programmatiskt

Använd C# för att fråga förutsägelseslutpunktens GET-[API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) för att få samma resultat som du såg i webbläsarfönstret i det föregående avsnittet. 

1. Skapa ett nytt konsolprogram i Visual Studio. 

    ![Åtkomst till menyn för användarinställningar i LUIS](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. I Visual Studio-projektet, i Solutions Explorer, väljer du **Lägg till referens** och välj sedan **System.Web** på fliken Sammansättningar.

    ![Åtkomst till menyn för användarinställningar i LUIS](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Skriv över Program.cs med följande kod:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Ersätt värdet för `YOUR_KEY` med LUIS-nyckeln.

5. Skapa och kör konsolprogrammet. Det visar samma JSON som du såg tidigare i webbläsarfönstret.

    ![Konsolfönstret visar JSON-resultat från LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstarten stänger du Visual Studio-projektet och tar bort projektkatalogen från filsystemet. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till yttranden och träna med C#](luis-get-started-cs-add-utterance.md)