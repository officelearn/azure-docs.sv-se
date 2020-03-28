---
title: 'Snabbstart: Känna igen tal från en mikrofon, C# (UWP) - Taltjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 93a5a29c225912a48cf99ba6126690c298dfef9d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924996"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

Om du redan har gjort det här, bra. Låt oss fortsätta.

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Börja med en standardkod

Låt oss lägga till lite kod som fungerar som ett skelett för vårt projekt.

1. Öppna i Solution `MainPage.xaml` **Explorer**.

2. I designerns XAML-vy infogar du följande XAML-kodavsnitt `<Grid>` i `</Grid>` **rutnätstaggen** (mellan och ):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. Öppna **Solution Explorer**den bakomgående källfilen `MainPage.xaml.cs`för kod bakom i Solution Explorer . (Den är grupperad `MainPage.xaml`under .)

4. Ersätt koden med följande baskod:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Innan du kan `SpeechRecognizer` initiera ett objekt måste du skapa en konfiguration som använder din prenumerationsnyckel och prenumerationsregion. Infoga den här `RecognizeSpeechAsync()` koden i metoden.

> [!NOTE]
> I det `FromSubscription()` här exemplet `SpeechConfig`används metoden för att skapa . En fullständig lista över tillgängliga metoder finns i [SpeechConfig-klassen](https://docs.microsoft.com/dotnet/api/)[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Initiera en SpeechRecognizer

Nu ska vi skapa `SpeechRecognizer`en . Det här objektet skapas inuti en medsats för att säkerställa korrekt frisläppande av ohanterat resurser. Infoga den `RecognizeSpeechAsync()` här koden i metoden, precis under talkonfigurationen.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Känna igen en fras

Från `SpeechRecognizer` objektet ska du anropa `RecognizeOnceAsync()` metoden. Med den här metoden kan taltjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta känna igen tal.

Lägg till den här koden i satsen med användningssatsen.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Visa igenkänningsresultat (eller fel)

När igenkänningsresultatet returneras av taltjänsten bör du göra något med den. Vi ska hålla det enkelt och skriva ut resultatet på statuspanelen.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **Bygg** > **bygglösning på** menyraden för att skapa programmet. Koden bör nu kompileras utan fel.

1. Välj **Felsökning** > **Avsökstarts felsökning** (eller tryck på **F5**) för att starta programmet. **Helloworld fönstret** visas.

   ![Exempel på UWP-program för taligenkänning i C# - snabbstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Välj **Aktivera mikrofon**och när åtkomstbehörighetsbegäran dyker upp väljer du **Ja**.

   ![Begäran om åtkomstbehörighet för mikrofon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Välj **Speech recognition with microphone input** (Taligenkänning med mikrofonindata) och tala in en fras eller mening på engelska i enhetens mikrofon. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i fönstret.

   ![Användargränssnitt för taligenkänning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
