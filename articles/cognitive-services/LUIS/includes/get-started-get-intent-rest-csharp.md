---
title: Kom avsikt med REST-anrop iC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e6ae9590cee3a2ddc3b8e121161fcf84815da28a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838496"
---
## <a name="prerequisites"></a>Förutsättningar

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Programmeringsspråket C# (ingår i VS Community 2017)
* Offentlig app-ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Hämta avsikter programmatiskt

Använd C# för att fråga förutsägelseslutpunktens GET-[API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) för att få samma resultat som du såg i webbläsarfönstret i det föregående avsnittet. 

1. Skapa ett nytt konsolprogram i Visual Studio. 

    ![Skapa ett nytt konsolprogram i Visual Studio](../media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. I Visual Studio-projektet, i Solutions Explorer, väljer du **Lägg till referens** och välj sedan **System.Web** på fliken Sammansättningar.

    ![välj Lägg till referens och sedan System.Web från fliken Sammansättningar](../media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Skriv över Program.cs med följande kod:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Ersätt värdet för `YOUR_KEY` med LUIS-nyckeln.

5. Skapa och kör konsolprogrammet. Det visar samma JSON som du såg tidigare i webbläsarfönstret.

    ![Konsolfönstret visar JSON-resultat från LUIS](../media/luis-get-started-cs-get-intent/console-turn-on.png)



## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstarten stänger du Visual Studio-projektet och tar bort projektkatalogen från filsystemet. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till yttranden och träna med C#](../luis-get-started-cs-add-utterance.md)