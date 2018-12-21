---
title: 'Snabbstart: Känna igen tal, .NET Core (Windows) – Taltjänster'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du känner igen tal i C# under .NET Core i Windows med hjälp av Speech Service SDK
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: e82e39eb3fc6c7ebaf4798ad10038bfd2fa9a41b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085492"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i C# under .NET Core i Windows med hjälp av Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln skapar du ett C#-konsolprogram för .NET Core på Windows med hjälp av Cognitive Services [Speech SDK](speech-sdk.md). Du transkriberar tal till text i realtid från datorns mikrofon. Programmet har skapats med [NuGet-paketets tal-SDK](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (alla versioner).

> [!NOTE]
> .NET Core är en plattformsoberoende plattform med öppen källkod som implementerar [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard)-specifikationen.

Du behöver en Speech-tjänstprenumerationsnyckel för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Mer information finns i [Prova Speech-tjänsten kostnadsfritt](get-started.md).


## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Starta Visual Studio 2017.

1. Kontrollera att arbetsbelastningen  **.NET cross-platform development** (Plattformsoberoende .NET-utveckling) är tillgänglig. Välj **Verktyg** > **Get Tools and Features** (Hämta verktyg och funktioner) på menyraden i Visual Studio för att öppna installationsprogrammet för Visual Studio. Om den här arbetsbelastningen redan är aktiverad stänger du dialogrutan.

    ![Skärmbild av Visual Studio-installationsprogrammet med fliken Arbetsbelastningar markerad](media/sdk/vs-enable-net-core-workload.png)

    Annars markerar du rutan bredvid **.NET Core cross-platform development** (Plattformsoberoende .NET-utveckling) och väljer **Ändra** i det nedre högra hörnet av dialogrutan. Installationen av den nya funktionen tar en stund.

1. Skapa en ny Visual C# .NET-konsolapp. I dialogrutan **Nytt projekt** expanderar du **Installerat** > **Visual C#** > **.NET Core** i den vänstra rutan. Välj sedan **Console App (.NET Core)**. För projektnamnet anger du *helloworld*.

    ![Skärmbild av dialogrutan Nytt projekt](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Skapa Visual C#-konsolapp (.NET Core)")

1. Installera och referera till [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget). Högerklicka på lösningen i Solution Explorer och välj sedan **Hantera NuGet-paket för lösningen**.

    ![Skärmbild av Solution Explorer, med alternativet Hantera NuGet-paket för lösningen markerat](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Hantera NuGet-paket för lösning")

1. I fältet för **paketkälla** uppe till höger väljer du **nuget.org**. Sök efter `Microsoft.CognitiveServices.Speech`-paketet och installera det i **helloworld**-projektet.

    ![Skärmbild av dialogrutan Hantera paket för lösningen](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Installera NuGet-paket")

1. Acceptera licensvillkoren som visas för att påbörja installationen av NuGet-paketet.

    ![Skärmbild av dialogrutan för att acceptera licensvillkoren](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Acceptera licensen")

När paketet har installerats visas en bekräftelse i Package Manager-konsolen.


## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna `Program.cs` och ersätt all kod i den med följande.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. I menyraden väljer du **Skapa** > **Skapa lösning**. Koden ska kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Skapandet lyckades")

1. Starta programmet. I menyraden väljer du **Felsök** > **Starta felsökning**, eller tryck på **F5**.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Starta appen i felsökningsläge")

1. Ett konsolfönster öppnas där du uppmanas att säga något. Säg en engelsk fras eller en mening. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i samma fönster.

    ![Skärmbild av konsolens utdata efter lyckad taligenkänning](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Konsolens utdata efter lyckad taligenkänning")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i mappen `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera avsikter i tal med hjälp av Speech SDK för C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Se även

- [Översätta tal](how-to-translate-speech-csharp.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
