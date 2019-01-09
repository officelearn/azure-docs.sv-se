---
title: 'Snabbstart: Identifiera tal, C# (UWP) – Speech Services'
titleSuffix: Azure Cognitive Services
description: I den här artikeln ska du skapa ett C#-baserat UWP-program (Universal Windows Platform) med hjälp av Cognitive Services Speech SDK. Du transkriberar tal till text i realtid från enhetens mikrofon. Programmet har skapats med Speech SDK NuGet-paketet och Microsoft Visual Studio 2017.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 2d51bd910b86c81304fb228d35079fca166b1eb7
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719889"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i en UWP-app med hjälp av Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln ska du utveckla ett C#-baserat UWP-program (Universal Windows Platform, Windows-version 1709 eller senare) med hjälp av Cognitive Services [Speech SDK](speech-sdk.md). Programmet transkriberar tal till text i realtid från enhetens mikrofon. Programmet skapas med [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (valfri version).

> [!NOTE]
> Med Universell Windows Platform kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten krävs:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Programmets användargränssnitt definieras med hjälp av XAML. Öppna `MainPage.xaml` i Solution Explorer. I designerns XAML-vy infogar du följande XAML-kodavsnitt i Grid-taggen (mellan `<Grid>` och `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Öppna källfilen för den bakomliggande koden, `MainPage.xaml.cs` (du hittar den grupperad under `MainPage.xaml`). Ersätt all kod i den med nedanstående.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. I `SpeechRecognitionFromMicrophone_ButtonClicked`-hanteraren i den här filen ersätter du strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. I `SpeechRecognitionFromMicrophone_ButtonClicked`-hanteraren ersätter du även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri utvärderingsversion).

1. Spara alla ändringar i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. På menyraden väljer du **Skapa** > **Skapa lösning**. Koden bör nu kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-uwp-08-build.png "Slutförd byggprocess")

1. Starta programmet. På menyraden väljer du **Felsök** > **Starta felsökning**, eller så trycker du på **F5**.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-uwp-09-start-debugging.png "Starta appen i felsökningsläge")

1. Ett fönster öppnas. Välj **Aktivera mikrofon** och bekräfta behörighetsbegäran som visas.

    ![Skärmbild av behörighetsbegäran](media/sdk/qs-csharp-uwp-10-access-prompt.png "Starta appen i felsökningsläge")

1. Välj **Speech recognition with microphone input** (Taligenkänning med mikrofonindata) och tala in en fras eller mening på engelska i enhetens mikrofon. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i fönstret.

    ![Skärmbild av användargränssnittet för taligenkänning](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se även

- [Översätta tal](how-to-translate-speech-csharp.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
