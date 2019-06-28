---
title: 'Snabbstart: Syntetisera tal, C# (UWP) – Speech Services'
titleSuffix: Azure Cognitive Services
description: I den här artikeln ska du skapa ett C#-baserat UWP-program (Universal Windows Platform) med hjälp av Cognitive Services Speech SDK. Du syntetisera tal från text i realtid till din enhets-API. Programmet har skapats med Speech SDK NuGet-paketet och Microsoft Visual Studio 2017.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: c11fbff883d2699bdd0a107fc462524d92ee410d
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467448"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Snabbstart: Syntetisera tal i en UWP-app med hjälp av tal-SDK

Snabbstarter kan också användas för [taligenkänning](quickstart-csharp-uwp.md), [talöversättning](quickstart-translate-speech-uwp.md) och [röst första virtuella assistenter](quickstart-virtual-assistant-csharp-uwp.md).

I den här artikeln ska du utveckla ett C#-baserat UWP-program (Universal Windows Platform, Windows-version 1709 eller senare) med hjälp av Cognitive Services [Speech SDK](speech-sdk.md). Programmet kommer att syntetisera tal från text i realtid till din enhets-API. Programmet skapas med [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (valfri version).

> [!NOTE]
> Med Universell Windows Platform kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Programmets användargränssnitt definieras med hjälp av XAML. Öppna `MainPage.xaml` i Solution Explorer. I designerns XAML-vy infogar du följande XAML-kodavsnitt i Grid-taggen (mellan `<Grid>` och `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Öppna källfilen för den bakomliggande koden, `MainPage.xaml.cs` (du hittar den grupperad under `MainPage.xaml`). Ersätt all kod i den med nedanstående.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. I `Speak_ButtonClicked`-hanteraren i den här filen ersätter du strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. I `Speak_ButtonClicked`-hanteraren ersätter du även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri utvärderingsversion).

1. Spara alla ändringar i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. På menyraden väljer du **Skapa** > **Skapa lösning**. Koden bör nu kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-uwp-08-build.png "Slutförd byggprocess")

1. Starta programmet. På menyraden väljer du **Felsök** > **Starta felsökning**, eller så trycker du på **F5**.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-uwp-09-start-debugging.png "Starta appen i felsökningsläge")

1. Ange text i textrutan och klicka på **Läs upp**. Texten skickas till Speech Services och syntetiskt till tal som spelar upp på högtalaren.

    ![Skärmbild av användargränssnittet för tal syntes](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa rösttyper](how-to-customize-voice-font.md)
- [Post voice-exempel](record-custom-voice-samples.md)
