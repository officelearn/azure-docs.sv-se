---
title: 'Snabbstart: Syntetisera tal, C# (.NET Core) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du syntetiserar tal i C# under .NET Core i Windows med hjälp av Tal-SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 91e06805b687e66c147b0904175ae20d01387acf
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275139"
---
> [!NOTE]
> .NET Core är en plattformsoberoende plattform med öppen källkod som implementerar [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard)-specifikationen.

## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=dotnetcore&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna `Program.cs` och ersätt all kod i den med nedanstående.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri utvärderingsversion).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. Välj **Bygg** > **bygglösning**på menyraden . Koden ska kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Lyckad konstruktion")

1. Starta programmet. Välj **Felsökning Avsöka** > **Felsökning**på menyraden eller tryck på **F5**.

    ![Skärmbild av Visual Studio-programmet med Starta felsökning markerat](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Starta appen för felsökning")

1. Ett konsolfönster visas där du uppmanas att skriva text. Skriv några ord eller en mening. Texten som du skrev överförs till taltjänsten och syntetiseras till tal, som spelas upp på högtalaren.

    ![Skärmbild av konsolutdata efter lyckad syntes](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Konsolutdata efter lyckad syntes")

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röstexempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
