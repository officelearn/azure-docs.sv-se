---
title: 'Snabbstart: Syntetisera tal, C# (UWP) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln ska du skapa ett C#-baserat UWP-program (Universal Windows Platform) med hjälp av Cognitive Services Speech SDK. Du syntetiserar tal från text i realtid till enhetens högtalare. Programmet är byggt med Speech SDK NuGet Package och Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 1633586a77465d9d5f605b17727f2531430d1cbd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671608"
---
> [!NOTE]
> Med Universell Windows Platform kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Lägg till exempelkod

Lägg nu till XAML-koden som definierar programmets användargränssnitt och lägg till C#-implementeringen för bakom-kod.

1. Öppna i Solution `MainPage.xaml` **Explorer**.

1. I designerns XAML-vy infogar du följande XAML-kodavsnitt `<Grid>` i `</Grid>` **rutnätstaggen** (mellan och ):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. Öppna **Solution Explorer**den bakomgående källfilen `MainPage.xaml.cs`för kod bakom i Solution Explorer . (Den är grupperad `MainPage.xaml`under .)

1. Ersätt all kod i den med följande utdrag:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. Leta reda på `Speak_ButtonClicked` strängen `YourSubscriptionKey`i källfilens hanterare och ersätt den med prenumerationsnyckeln.

1. Leta `Speak_ButtonClicked` reda på strängen `YourServiceRegion`i hanteraren och ersätt den med den [region som](~/articles/cognitive-services/Speech-Service/regions.md) är associerad med prenumerationen. (Använd `westus` till exempel för den kostnadsfria provprenumerationen.)

1. På menyraden väljer du**Spara alla** **för** > att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **Bygg** > **bygglösning på** menyraden för att skapa programmet. Koden bör nu kompileras utan fel.

1. Välj **Felsökning** > **Avsökstarts felsökning** (eller tryck på **F5**) för att starta programmet. **Helloworld fönstret** visas.

   ![Exempel på UWP-program för talsyntes i C# - snabbstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Skriv in text i textrutan och klicka på **Läs upp**. Texten överförs till taltjänsten och syntetiseras till tal, som spelas upp på högtalaren.

    ![Användargränssnitt för talsyntes](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röstexempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
