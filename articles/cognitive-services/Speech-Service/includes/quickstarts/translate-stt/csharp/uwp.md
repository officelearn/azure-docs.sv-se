---
title: 'Snabbstart: Översätt tal-till-text, C# (UWP) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: 62993b2e553630edd228228b4faa82de44997063
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671242"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Lägg till exempelkod

Lägg nu till XAML-koden som definierar programmets användargränssnitt och lägg till C#-implementeringen för bakom-kod.

1. Öppna i Solution `MainPage.xaml` **Explorer**.

1. I designerns XAML-vy infogar du följande XAML-kodavsnitt `<Grid>` i `</Grid>` **rutnätstaggen** (mellan och ):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. Öppna **Solution Explorer**den bakomgående källfilen `MainPage.xaml.cs`för kod bakom i Solution Explorer . (Den är grupperad `MainPage.xaml`under .)

1. Ersätt all kod i den med följande utdrag:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. Leta `SpeechTranslationFromMicrophone_ButtonClicked` reda på strängen `YourSubscriptionKey`i hanteraren i den här filen och ersätt den med prenumerationsnyckeln.

1. Leta `SpeechTranslationFromMicrophone_ButtonClicked` reda på strängen `YourServiceRegion`i hanteraren och ersätt den med den [region som](~/articles/cognitive-services/Speech-Service/regions.md) är associerad med prenumerationen. (Använd `westus` till exempel för den kostnadsfria provprenumerationen.)

1. På menyraden väljer du**Spara alla** **för** > att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **Bygg** > **bygglösning på** menyraden för att skapa programmet. Koden bör nu kompileras utan fel.

1. Välj **Felsökning** > **Avsökstarts felsökning** (eller tryck på **F5**) för att starta programmet. **Helloworld fönstret** visas.

   ![Exempel på UWP-översättningsprogram i C# - snabbstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Välj **Aktivera mikrofon**och när åtkomstbehörighetsbegäran dyker upp väljer du **Ja**.

   ![Begäran om åtkomstbehörighet för mikrofon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Välj **Översätt tal från mikrofoningången**och tala en engelsk fras eller mening i enhetens mikrofon. Ansökan överför ditt tal till taltjänsten, vilket översätter talet till text på ett annat språk (i det här fallet tyska). Taltjänsten skickar tillbaka den översatta texten till programmet, som visar översättningen i fönstret.

   ![Användargränssnitt för talöversättning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
