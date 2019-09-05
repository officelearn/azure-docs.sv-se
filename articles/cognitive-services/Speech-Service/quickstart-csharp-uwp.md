---
title: 'Snabbstart: Identifiera tal- C# , (UWP) – tal-service'
titleSuffix: Azure Cognitive Services
description: I den här artikeln ska du skapa ett C#-baserat UWP-program (Universal Windows Platform) med hjälp av Cognitive Services Speech SDK. Du transkriberar tal till text i realtid från enhetens mikrofon. Programmet har skapats med tal SDK NuGet-paketet och Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382286"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i en UWP-app med hjälp av Speech SDK

Snabb Starter är också tillgängliga för [tal syntes](quickstart-text-to-speech-csharp-uwp.md), [tal översättning](quickstart-translate-speech-uwp.md)och [röst för första virtuella assistent](quickstart-virtual-assistant-csharp-uwp.md).

Om du vill kan du välja ett annat programmeringsspråk och/eller miljö:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln utvecklar du ett C# universell Windows-plattform-program (UWP) med hjälp av COGNITIVE Services [Speech SDK](speech-sdk.md). Programmet utvärderar tal till text i real tid från enhetens mikrofon. Programmet har skapats med [tal SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2019 (alla versioner).

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Öppnakäll filen `MainPage.xaml.cs`bakomliggande kod i Solution Explorer. (Det är grupperat `MainPage.xaml`under.)

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Leta upp strängen `YourSubscriptionKey`i käll `SpeechRecognitionFromMicrophone_ButtonClicked` filens hanterare och ersätt den med din prenumerations nyckel.

1. Leta upp strängen `YourServiceRegion`i [](regions.md) hanteraren och ersätt den med den region som är associerad med din prenumeration `SpeechRecognitionFromMicrophone_ButtonClicked` . (Använd `westus` till exempel för den kostnads fria prov prenumerationen.)

1. Välj **Arkiv** > **Spara alla** på Meny raden för att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel program för tal igenkänning i C# UWP-snabb start](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Välj **aktivera mikrofon**och när åtkomst behörighets förfrågan öppnas, Välj **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Välj **Speech recognition with microphone input** (Taligenkänning med mikrofonindata) och tala in en fras eller mening på engelska i enhetens mikrofon. Ditt tal överförs till tal tjänsterna och skrivs till text, som visas i fönstret.

   ![Användar gränssnitt för tal igenkänning](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Snabbstart: Översätt tal med talet SDK för C# (UWP)](quickstart-translate-speech-uwp.md)
- [Träna en modell för Custom Speech](how-to-custom-speech-train-model.md)
