---
title: 'Snabbstart: Syntetiskt tal, C# (UWP) – tal service'
titleSuffix: Azure Cognitive Services
description: I den här artikeln ska du skapa ett C#-baserat UWP-program (Universal Windows Platform) med hjälp av Cognitive Services Speech SDK. Du kan syntetisera tal från text i real tid till enhetens högtalare. Programmet har skapats med tal SDK NuGet-paketet och Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382228"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Snabbstart: Syntetisera tal i en UWP-app med hjälp av talet SDK

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-csharp-uwp.md), [tal översättning](quickstart-translate-speech-uwp.md)och [röst för första virtuella assistent](quickstart-virtual-assistant-csharp-uwp.md).

I den här artikeln utvecklar du ett C# universell Windows-plattform-program (UWP) med hjälp av COGNITIVE Services [Speech SDK](speech-sdk.md). Programmet syntetiserar tal från text i real tid till enhetens högtalare. Du skapar programmet med hjälp av [talet SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2019 (alla versioner).

> [!NOTE]
> Med Universell Windows Platform kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till C# koden för bakomliggande implementering.

1. Öppna`MainPage.xaml`i **Solution Explorer**.

1. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen ( `<Grid>` mellan `</Grid>`och):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Öppnakäll filen `MainPage.xaml.cs`bakomliggande kod i Solution Explorer. (Det är grupperat `MainPage.xaml`under.)

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Leta upp strängen `YourSubscriptionKey`i käll `Speak_ButtonClicked` filens hanterare och ersätt den med din prenumerations nyckel.

1. Leta upp strängen `YourServiceRegion`i [](regions.md) hanteraren och ersätt den med den region som är associerad med din prenumeration `Speak_ButtonClicked` . (Använd `westus` till exempel för den kostnads fria prov prenumerationen.)

1. Välj **Arkiv** > **Spara alla** på Meny raden för att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP tal syntes program C# i snabb start](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Skriv lite text i text rutan och klicka på **tala**. Texten skickas till tal tjänsterna och syntetiskt till tal, som spelas upp på din talare.

    ![Användar gränssnitt för tal syntes](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Skapa och Använd anpassade röst modeller](how-to-custom-voice-create-voice.md)
- [Spela in röst exempel](record-custom-voice-samples.md)
