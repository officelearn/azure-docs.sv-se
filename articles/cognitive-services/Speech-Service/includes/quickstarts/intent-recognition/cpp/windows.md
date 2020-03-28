---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: bc624197930044575a46666cbb74e5fe452e96ab
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925046"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows" target="_blank">Installera Tal-SDK för utvecklingsmiljön och skapa<span class="docon docon-navigate-external x-hidden-focus"></span>ett tomt exempelprojekt</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Skapa en LUIS-app för avsiktskännedom

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Öppna sedan projektet i Visual Studio.

1. Starta Visual Studio 2019.
2. Ladda projektet och `helloworld.cpp`öppna .

## <a name="start-with-some-boilerplate-code"></a>Börja med en standardkod

Låt oss lägga till lite kod som fungerar som ett skelett för vårt projekt. Observera att du har skapat en `recognizeIntent()`asynkron metod som kallas .

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Innan du kan `IntentRecognizer` initiera ett objekt måste du skapa en konfiguration som använder nyckeln och platsen för LUIS-förutsägelseresursen.

> [!IMPORTANT]
> Startnyckeln och redigeringstangenterna fungerar inte. Du måste använda förutsägelsenyckeln och platsen som du skapade tidigare. Mer information finns i [Skapa en LUIS-app för avsiktsigenkänning](#create-a-luis-app-for-intent-recognition).

Infoga den här `recognizeIntent()` koden i metoden. Se till att du uppdaterar dessa värden:

* Ersätt `"YourLanguageUnderstandingSubscriptionKey"` med luis-förutsägelsenyckeln.
* Ersätt `"YourLanguageUnderstandingServiceRegion"` med luis-platsen.  Använd **regionidentifierare** från [region](https://aka.ms/speech/sdkregion).

>[!TIP]
> Om du behöver hjälp med att hitta dessa värden läser du [Skapa en LUIS-app för avsiktsigenkänning](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

I det `FromSubscription()` här exemplet `SpeechConfig`används metoden för att skapa . En fullständig lista över tillgängliga metoder finns i [SpeechConfig Class](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

Tal-SDK kommer som standard att känna igen med hjälp av en-us för språket, se [Ange källspråk för tal till text](../../../../how-to-specify-source-language.md) för information om hur du väljer källspråk.

## <a name="initialize-an-intentrecognizer"></a>Initiera en IntentRecognizer

Nu ska vi skapa `IntentRecognizer`en . Infoga den `recognizeIntent()` här koden i metoden, precis under talkonfigurationen.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Lägga till ett språkUnderstandingModel och avsikter

Du måste associera `LanguageUnderstandingModel` en med avsiktsmedkännaren och lägga till de avsikter som du vill ska känna igen. Vi kommer att använda avsikter från den fördefinierade domänen för hemautomatisering.

Sätt i den `IntentRecognizer`här koden under . Se till att `"YourLanguageUnderstandingAppId"` du ersätter med ditt LUIS-app-ID.

>[!TIP]
> Om du behöver hjälp med att hitta det här värdet läser du [Skapa en LUIS-app för avsiktsigenkänning](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

## <a name="recognize-an-intent"></a>Känna igen en avsikt

Från `IntentRecognizer` objektet ska du anropa `RecognizeOnceAsync()` metoden. Med den här metoden kan taltjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta känna igen tal. För enkelhetens skull väntar vi på att framtiden ska återgå till att slutföras.

Sätt i den här koden under din modell:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>Visa igenkänningsresultat (eller fel)

När igenkänningsresultatet returneras av taltjänsten bör du göra något med den. Vi ska hålla det enkelt och skriva ut resultatet till konsolen.

Sätt i `auto result = recognizer->RecognizeOnceAsync().get();`den här koden nedan:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Kontrollera din kod

Nu ska koden se ut så här:

> [!NOTE]
> Vi har lagt till några kommentarer till den här versionen.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår taligenkänning med taltjänsten.

1. **Kompilera koden** - Välj **Bygg** > **bygglösning**på menyraden i Visual Studio .
2. **Starta appen** - Välj Felsökning **Debug** > **Avsöka Startfelsökning** på menyraden eller tryck på <kbd>F5</kbd>.
3. **Starta erkännande** - Det kommer att uppmana dig att tala en fras på engelska. Ditt tal skickas till taltjänsten, transkriberas som text och återges i konsolen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
