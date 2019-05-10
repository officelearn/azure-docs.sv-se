---
title: 'Snabbstart: Översätt tal, C# (.NET Core Windows)'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten skapar du ett enkelt .NET Core-program för att samla in användarens tal, översätta det till ett annat språk och mata ut texten till kommandoraden. Den här guiden är utformad för Windows-användare.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: fa516c5050bfd53add761bea56c74a58057263c7
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465686"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-core"></a>Snabbstart: Översätt tal med Speech-SDK för .NET Core

Snabbstarter kan också användas för [tal till text](quickstart-csharp-dotnet-windows.md) och [text till tal](quickstart-text-to-speech-dotnet-windows.md).

I den här snabbstarten skapar du ett enkelt .NET Core-program som samlar in användarens tal från din dators mikrofon, översätter talet och transkriberar den översatta texten till kommandoraden i realtid. Det här programmet är gjort att köras på 64-bitars Windows och är skapat med [Speech SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017.

En fullständig lista över språk som är tillgängliga för talöversättning finns i [språkstöd](language-support.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten krävs:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna `Program.cs` och ersätt all kod i den med följande.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. I menyraden väljer du **Skapa** > **Skapa lösning**. Koden ska kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Skapandet lyckades")

1. Starta programmet. I menyraden väljer du **Felsök** > **Starta felsökning**, eller tryck på **F5**.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Starta appen i felsökningsläge")

1. Ett konsolfönster öppnas där du uppmanas att säga något. Säg en engelsk fras eller en mening. Ditt tal överförs till Speech-tjänsten, översätts och transkriberas till text som visas i samma fönster.

    ![Skärmbild av konsolens utdata efter en lyckad översättning](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "Konsolens utdata efter lyckad översättning")


## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel att läsa tal från en ljudfil och mata ut översatt text som syntetiserat tal, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
