---
title: 'Snabbstart: Översätta tal, C# (UWP) – tal service'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten skapar du ett Universell Windows-plattform-program (UWP) för att avbilda användar tal, översätter det till ett annat språk och skriva ut texten på kommando raden. Den här guiden är utformad för Windows-användare.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 08/19/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: e513cbbc615965ef196a830351aab8ac241c3f20
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382607"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Snabbstart: Översätt tal med Speech-SDK för C# (UWP)

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-csharp-uwp.md), [tal syntes](quickstart-text-to-speech-csharp-uwp.md)och [röst-och den första virtuella assistenten](quickstart-virtual-assistant-csharp-uwp.md).

I den här snabb starten skapar du ett Universell Windows-plattform-program (UWP) som avbildar användar tal från datorns mikrofon, översätter talet och omvandlar den översatta texten till kommando raden i real tid. Det här programmet är utformat för att köras på 64-bitars Windows, och det är byggt med [talet SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2019.

En fullständig lista över språk som är tillgängliga för talöversättning finns i [språkstöd](language-support.md).

> [!NOTE]
> Med UWP kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Öppnakäll filen `MainPage.xaml.cs`bakomliggande kod i Solution Explorer. (Det är grupperat `MainPage.xaml`under.)

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Leta upp strängen `YourSubscriptionKey`i hanterarenidenhärfilenochersättdenmeddinprenumerationsnyckel.`SpeechTranslationFromMicrophone_ButtonClicked`

1. Leta upp strängen `YourServiceRegion`i [](regions.md) hanteraren och ersätt den med den region som är associerad med din prenumeration `SpeechTranslationFromMicrophone_ButtonClicked` . (Använd `westus` till exempel för den kostnads fria prov prenumerationen.)

1. Välj **Arkiv** > **Spara alla** på Meny raden för att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP översättnings C# program i-snabb start](media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Välj **aktivera mikrofon**och när åtkomst behörighets förfrågan öppnas, Välj **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Välj **Översätt tal från mikrofon inmatningen**och tala en engelsk fras eller mening i enhetens mikrofon. Programmet skickar din röst till tal tjänsten som översätter talet till text på ett annat språk (i det här fallet tyska). Tal tjänsten skickar den översatta texten tillbaka till programmet, som visar översättningen i fönstret.

   ![Användar gränssnitt för tal Översättning](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Träna en modell för Custom Speech](how-to-custom-speech-train-model.md)
