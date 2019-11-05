---
title: 'Snabb start: syntetiskt tal C# , (UWP) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln ska du skapa ett C#-baserat UWP-program (Universal Windows Platform) med hjälp av Cognitive Services Speech SDK. Du kan syntetisera tal från text i real tid till enhetens högtalare. Programmet har skapats med tal SDK NuGet-paketet och Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 3a88f4fc14286a60feb9d72676827b0cce91eb25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503131"
---
> [!NOTE]
> Med Universell Windows Platform kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>Lägg till exempelkod

Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till C# koden för bakomliggande implementering.

1. Öppna `MainPage.xaml`i **Solution Explorer**.

1. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen (mellan `<Grid>` och `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. Öppna käll filen för bakomliggande kod `MainPage.xaml.cs`i **Solution Explorer**. (Den grupperas under `MainPage.xaml`.)

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. Leta upp strängen `YourSubscriptionKey`i käll filens `Speak_ButtonClicked` hanterare och ersätt den med din prenumerations nyckel.

1. Leta upp strängen `YourServiceRegion`i `Speak_ButtonClicked` hanteraren och ersätt den med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration. (Använd till exempel `westus` för den kostnads fria prov prenumerationen.)

1. I meny raden väljer du **arkiv** > **Spara alla** för att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet genom att välja **felsök** > **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP tal syntes program C# i snabb start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Skriv lite text i text rutan och klicka på **tala**. Texten skickas till tal tjänsterna och syntetiskt till tal, som spelas upp på din talare.

    ![Användar gränssnitt för tal syntes](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röst exempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
