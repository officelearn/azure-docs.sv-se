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
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 65db847c5df1a05671e3b1c0ac4541f00506c8fd
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854685"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Snabbstart: Syntetisera tal med talet SDK för .NET Framework (Windows)

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-csharp-dotnet-windows.md) och [tal översättning](quickstart-translate-speech-dotnetframework-windows.md).

Använd den här guiden för att skapa ett text till tal-konsol program med .NET Framework för Windows och talet SDK. När du är färdig kan du syntetisera tal från text och höra talet på din talare i real tid.

För en snabb demonstration (utan att skapa Visual Studio-projektet själv som det visas nedan):

Hämta de senaste [Cognitive Services tal SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) -exemplen från GitHub.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra det här projektet behöver du:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* En prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).
* En talare (eller ett headset) är tillgängligt.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna `Program.cs` och ersätt den automatiskt genererade koden med det här exemplet:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Leta upp och ersätt strängen `YourSubscriptionKey` med din prenumerations nyckel för tal tjänster.

1. Leta upp och ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsversionen är regionen `westus`.

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. På menyraden väljer du **Skapa** > **Skapa lösning**. Koden bör nu kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-dotnet-windows-08-build.png "Slutförd byggprocess")

1. På menyraden väljer du **Felsök** > **Starta felsökning** eller trycker på **F5** för att starta programmet.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Starta appen i felsökningsläge")

1. Ett konsol fönster visas där du uppmanas att ange lite text. Skriv några ord eller en mening. Den text som du har angett skickas till tal tjänsterna och syntetiskt till tal, som spelas upp på din talare.

    ![Skärmbild av konsolens utdata efter lyckad taligenkänning](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Konsolens utdata efter lyckad taligenkänning")

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa röst teckensnitt](how-to-customize-voice-font.md)
- [Spela in röst exempel](record-custom-voice-samples.md)
