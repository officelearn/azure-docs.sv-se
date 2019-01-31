---
title: Hämta avsikt, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: I den här C#-snabbstarten använder du en tillgänglig offentlig LUIS-app för att fastställa användarens avsikt i konversationstext.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 81a05ed3e981c2a35221830a16b5859ee5c1bd4f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225524"
---
# <a name="quickstart-get-intent-using-c"></a>Snabbstart: Hämta avsikt med C#

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

    ![Skapa ett nytt konsolprogram i Visual Studio](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. I Visual Studio-projektet, i Solutions Explorer, väljer du **Lägg till referens** och välj sedan **System.Web** på fliken Sammansättningar.

    ![välj Lägg till referens och sedan System.Web från fliken Sammansättningar](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

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
