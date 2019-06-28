---
title: 'Snabbstart: Syntetisera tal, C# (.NET Core) – Speech Services'
titleSuffix: Azure Cognitive Services
description: Lär dig att syntetisera tal i C# under .NET Core för Windows med hjälp av tal-SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 7b4a018e38ca625e38dc1658a95d3ce0e677f711
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467427"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Snabbstart: Syntetisera tal med Speech-SDK för .NET Core

Snabbstarter kan också användas för [taligenkänning](quickstart-csharp-dotnetcore-windows.md) och [talöversättning](quickstart-translate-speech-dotnetcore-windows.md).

I den här artikeln skapar du ett C#-konsolprogram för .NET Core på Windows med hjälp av Cognitive Services [Speech SDK](speech-sdk.md). Du syntetisera tal från text i realtid till datorns högtalare. Programmet har skapats med [Speech SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (alla versioner).

> [!NOTE]
> .NET Core är en plattformsoberoende plattform med öppen källkod som implementerar [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard)-specifikationen.

Du behöver en Speech Services-prenumeration för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Se [testa Speech Services kostnadsfritt](get-started.md) mer information.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna `Program.cs` och ersätt all kod i den med följande.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. I menyraden väljer du **Skapa** > **Skapa lösning**. Koden ska kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Skapandet lyckades")

1. Starta programmet. I menyraden väljer du **Felsök** > **Starta felsökning**, eller tryck på **F5**.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Starta appen i felsökningsläge")

1. Ett konsolfönster visas där du uppmanas att ange lite text. Skriv några ord eller en mening. Den text som du har angett skickas till Speech Services och syntetiskt till tal som spelar upp på högtalaren.

    ![Skärmbild av konsolens utdata efter lyckad syntes](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "konsolens utdata efter lyckad syntes")

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel att syntetisera tal till en ljudfil, är tillgängliga på GitHub.

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa rösttyper](how-to-customize-voice-font.md)
- [Post voice-exempel](record-custom-voice-samples.md)
