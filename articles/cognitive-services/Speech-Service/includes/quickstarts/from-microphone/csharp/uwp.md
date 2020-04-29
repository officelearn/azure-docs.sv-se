---
title: 'Snabb start: identifiera tal från en mikrofon, C# (UWP) – tal tjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 1c631f4dea3b182c97f11f3892dff834c7681507
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275525"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

Om du redan har gjort detta är det bra. Vi fortsätter.

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt.

1. Öppna **Solution Explorer** `MainPage.xaml`i Solution Explorer.

2. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen ( `<Grid>` mellan `</Grid>`och):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. Öppna **Solution Explorer**käll filen `MainPage.xaml.cs`bakomliggande kod i Solution Explorer. (Det är grupperat `MainPage.xaml`under.)

4. Ersätt koden med följande grundläggande kod:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Innan du kan initiera ett `SpeechRecognizer` objekt måste du skapa en konfiguration som använder din prenumerations nyckel och din prenumerations region. Infoga den här koden i `RecognizeSpeechAsync()` -metoden.

> [!NOTE]
> I det här exemplet `FromSubscription()` används metoden för att `SpeechConfig`bygga. En fullständig lista över tillgängliga metoder finns i [SpeechConfig-klass](https://docs.microsoft.com/dotnet/api/)[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Initiera en SpeechRecognizer

Nu ska vi skapa en `SpeechRecognizer`. Det här objektet skapas i en using-instruktion för att säkerställa en korrekt version av ohanterade resurser. Infoga den här koden i `RecognizeSpeechAsync()` metoden, precis under din tal konfiguration.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Identifiera en fras

Från- `SpeechRecognizer` objektet kommer du att anropa- `RecognizeOnceAsync()` metoden. Med den här metoden kan röst tjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta identifiera tal.

I using-instruktionen lägger du till den här koden.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Visa tolknings resultat (eller fel)

När igenkännings resultatet returneras av tal tjänsten vill du göra något med det. Vi ska hålla det enkelt och skriva ut resultatet till status panelen.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP tal igenkännings program i C# – snabb start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Välj **aktivera mikrofon**och när åtkomst behörighets förfrågan öppnas, Välj **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Välj **Speech recognition with microphone input** (Taligenkänning med mikrofonindata) och tala in en fras eller mening på engelska i enhetens mikrofon. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i fönstret.

   ![Användar gränssnitt för tal igenkänning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

