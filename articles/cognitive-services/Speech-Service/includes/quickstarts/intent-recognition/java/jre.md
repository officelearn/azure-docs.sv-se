---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 247b2a75af675c95745153e20b923d004a2f2f79
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671461"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">Installera Tal-SDK för utvecklingsmiljön och skapa<span class="docon docon-navigate-external x-hidden-focus"></span>ett tomt exempelprojekt</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Skapa en LUIS-app för avsiktskännedom

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Öppna projektet

1. Öppna önskad IDE.
2. Ladda projektet och `Main.java`öppna .

## <a name="start-with-some-boilerplate-code"></a>Börja med en standardkod

Låt oss lägga till lite kod som fungerar som ett skelett för vårt projekt.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Innan du kan `IntentRecognizer` initiera ett objekt måste du skapa en konfiguration som använder nyckeln och platsen för LUIS-förutsägelseresursen.  

Sätt in den här koden `main()`i try/ catch-blocket i . Se till att du uppdaterar dessa värden:

* Ersätt `"YourLanguageUnderstandingSubscriptionKey"` med luis-förutsägelsenyckeln.
* Ersätt `"YourLanguageUnderstandingServiceRegion"` med luis-platsen. Använda **regionidentifierare** från [region](https://aka.ms/speech/sdkregion)

>[!TIP]
> Om du behöver hjälp med att hitta dessa värden läser du [Skapa en LUIS-app för avsiktsigenkänning](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

I det `FromSubscription()` här exemplet `SpeechConfig`används metoden för att skapa . En fullständig lista över tillgängliga metoder finns i [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

Tal-SDK kommer som standard att känna igen med hjälp av en-us för språket, se [Ange källspråk för tal till text](../../../../how-to-specify-source-language.md) för information om hur du väljer källspråk.

## <a name="initialize-an-intentrecognizer"></a>Initiera en IntentRecognizer

Nu ska vi skapa `IntentRecognizer`en . Infoga den här koden precis under talkonfigurationen.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Lägga till ett språkUnderstandingModel och avsikter

Du måste associera `LanguageUnderstandingModel` en med avsiktsmedkännaren och lägga till de avsikter som du vill ska känna igen. Vi kommer att använda avsikter från den fördefinierade domänen för hemautomatisering.

Sätt i den `IntentRecognizer`här koden under . Se till att `"YourLanguageUnderstandingAppId"` du ersätter med ditt LUIS-app-ID.

>[!TIP]
> Om du behöver hjälp med att hitta det här värdet läser du [Skapa en LUIS-app för avsiktsigenkänning](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>Känna igen en avsikt

Från `IntentRecognizer` objektet ska du anropa `recognizeOnceAsync()` metoden. Med den här metoden kan taltjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta känna igen tal.

Sätt i den här koden under din modell:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Visa igenkänningsresultat (eller fel)

När igenkänningsresultatet returneras av taltjänsten bör du göra något med den. Vi ska hålla det enkelt och skriva ut resultatet till konsolen.

Infoga den här koden `recognizeOnceAsync()`under samtalet till .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Frigöra resurser

Det är viktigt att du släpper talresurserna när du är klar med dem. Sätt in denna kod i slutet av försök / catch block:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Kontrollera din kod

Nu ska koden se ut så här:

> [!NOTE]
> Vi har lagt till några kommentarer till den här versionen.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Tryck på <kbd>F11</kbd>eller välj **Kör** > **felsökning**.
Följande 15 sekunder av talindata från mikrofonen identifieras och loggas i konsolfönstret.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
