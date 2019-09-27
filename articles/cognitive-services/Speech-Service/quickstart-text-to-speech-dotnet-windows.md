---
title: 'Snabbstart: Syntetisera tal, .NET Framework (Windows) – tal-service'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett text till tal-konsol program med .NET Framework för Windows och talet SDK. När du är färdig kan du syntetisera tal från text och höra talet på din talare i real tid.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327454"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Snabbstart: Syntetisera tal med talet SDK för .NET Framework (Windows)

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-csharp-dotnet-windows.md) och [tal översättning](quickstart-translate-speech-dotnetframework-windows.md).

Använd den här guiden för att skapa ett text till tal-konsol program med .NET Framework för Windows och talet SDK. När du är färdig kan du syntetisera tal från text och höra talet på din talare i real tid.

För en snabb demonstration (utan att skapa Visual Studio-projektet själv, enligt beskrivningen i den här artikeln), hämtar du de senaste [Cognitive Servicesa tal SDK-exemplen](https://github.com/Azure-Samples/cognitive-services-speech-sdk) från GitHub.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra det här projektet behöver du:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* En prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).
* En talare (eller ett headset) är tillgängligt.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna **program.cs** och ersätt den automatiskt genererade koden med det här exemplet:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Hitta strängen `YourSubscriptionKey` och ersätt den med din prenumerations nyckel för tal tjänster.

1. Hitta strängen `YourServiceRegion` och ersätt den med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnads fria utvärderings prenumerationen är regionen `westus`.

1. Välj **arkiv** > **Spara alla**på Meny raden.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller Välj **F5**).

1. Ange en engelska fras eller en mening. Programmet skickar texten till tal tjänsterna, som skickar syntetiskt tal till programmet som spelas upp på din högtalare.

   ![Användar gränssnitt för tal syntes](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Skapa en anpassad röst](how-to-custom-voice-create-voice.md)
- [Spela in anpassade röst exempel](record-custom-voice-samples.md)
