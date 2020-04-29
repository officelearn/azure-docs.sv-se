---
title: 'Snabb start: syntetisera tal, C# (UWP) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln ska du skapa ett C#-baserat UWP-program (Universal Windows Platform) med hjälp av Cognitive Services Speech SDK. Du kan syntetisera tal från text i real tid till enhetens högtalare. Programmet har skapats med tal SDK NuGet-paketet och Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89fc5fddffb291942b8f3a4db3dfdf4ccd6cf46a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275251"
---
> [!NOTE]
> Med Universell Windows Platform kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

## <a name="prerequisites"></a>Krav

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Lägg till exempelkod

Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till koden för C#-bakomliggande implementering.

1. Öppna **Solution Explorer** `MainPage.xaml`i Solution Explorer.

1. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen ( `<Grid>` mellan `</Grid>`och):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. Öppna **Solution Explorer**käll filen `MainPage.xaml.cs`bakomliggande kod i Solution Explorer. (Det är grupperat `MainPage.xaml`under.)

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. Leta upp strängen `YourSubscriptionKey`i käll `Speak_ButtonClicked` filens hanterare och ersätt den med din prenumerations nyckel.

1. Leta upp `Speak_ButtonClicked` strängen `YourServiceRegion`i hanteraren och ersätt den med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration. (Använd `westus` till exempel för den kostnads fria prov prenumerationen.)

1. Välj **Arkiv** > **Spara alla** på Meny raden för att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP tal syntes program i C# – snabb start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Skriv lite text i text rutan och klicka på **tala**. Texten skickas till tal tjänsten och syntetiskt till tal som spelas upp på din talare.

    ![Användar gränssnitt för tal syntes](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röst exempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
