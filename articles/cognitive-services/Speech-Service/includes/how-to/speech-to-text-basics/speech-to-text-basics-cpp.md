---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: e39486f0daf1c865de3e53372d32a44fdcb8eb99
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399802"
---
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en Azure-konto- och taltjänstprenumeration. Om du inte har ett konto och en prenumeration [provar du taltjänsten utan kostnad](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera Tal-SDK. Beroende på din plattform använder du följande instruktioner:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">Macos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Om du vill anropa taltjänsten med tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig). Den här klassen innehåller information om din prenumeration, till exempel din nyckel och associerade region, slutpunkt, värd eller auktoriseringstoken.

> [!NOTE]
> Oavsett om du utför taligenkänning, talsyntes, översättning eller avsiktsigenkänning skapar du alltid en konfiguration.

Det finns några sätt som du [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)kan initiera en:

* Med en prenumeration: skicka in en nyckel och den associerade regionen.
* Med en slutpunkt: passera i en taltjänstslutpunkt. En nyckel eller auktoriseringstoken är valfri.
* Med en värd: skicka in en värdadress. En nyckel eller auktoriseringstoken är valfri.
* Med en auktoriseringstoken: skicka in en auktoriseringstoken och den associerade regionen.

Låt oss ta en titt [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) på hur en skapas med hjälp av en nyckel och region. Se [regionens supportsida](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regionidentifierare.

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Initiera en identifierare

När du har [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)skapat en är nästa steg [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)att initiera en . När du initierar en [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)måste du skicka `speech_config`den till . Detta ger de autentiseringsuppgifter som taltjänsten kräver för att validera din begäran.

Så här ser du ut så här [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) om du känner igen tal med enhetens standardmikrofon:

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

Om du vill ange ljudinmatningsenheten måste du [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) skapa en `audioConfig` och ange [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)parametern när du initierar .

> [!TIP]
> [Läs om hur du hämtar enhets-ID:et för ljudinmatningsenheten](../../../how-to-select-audio-input-devices.md).

Lägg först till `using namespace` följande `#include` uttalande efter definitionerna.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

Därefter kan du referera till `AudioConfig` objektet på följande sätt:

```cpp
auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

Om du vill tillhandahålla en ljudfil i stället för att använda `audioConfig`en mikrofon måste du fortfarande ange en . Men när du [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig)skapar en `FromDefaultMicrophoneInput`, i stället `FromWavFileOutput` för `filename` att ringa , kommer du att anropa och skicka parametern.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>Identifiera tal

[Klassen Recognizer](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) för tal-SDK för C++ visar några metoder som du kan använda för taligenkänning.

* Erkännande av ett slag (async) – Utför igenkänning i ett icke-blockerande (asynkront) läge. Detta kommer att känna igen ett enda uttryck. Slutet på ett enda uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunder av ljud bearbetas.
* Kontinuerlig igenkänning (async) - Asynkront initierar kontinuerlig igenkänning. Användaren måste ansluta för att hantera händelsen för att få igenkänningsresultat. Anropa om du vill stoppa asynkron kontinuerlig igenkänning anropar du [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync).

> [!NOTE]
> Läs mer om hur [du väljer ett taligenkänningsläge](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Erkännande av ett skott

Här är ett exempel på asynkron enkelbildsigenkänning med: [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync)

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Du måste skriva en kod för att hantera resultatet. I detta exempel [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason)utvärderas:

* Skriver ut igenkänningsresultatet:`ResultReason::RecognizedSpeech`
* Om det inte finns någon igenkänningsmatchning informerar du användaren:`ResultReason::NoMatch`
* Om ett fel påträffas skriver du ut felmeddelandet:`ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Kontinuerligt erkännande

Kontinuerlig igenkänning är lite mer involverad än single-shot erkännande. Det kräver att du `Recognizing` `Recognized`prenumererar `Canceled` på , och händelser för att få igenkänningsresultaten. Om du vill stoppa igenkänningen måste du anropa [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Här är ett exempel på hur kontinuerlig igenkänning utförs på en ljudinmatningsfil.

Låt oss börja med att definiera indata och initiera en: [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Låt oss sedan skapa en variabel för att hantera taligenkänningstillståndet. Till att börja med `promise<void>`kommer vi att förklara en , eftersom i början av erkännandet kan vi lugnt anta att det inte är klar.

```cpp
promise<void> recognitionEnd;
```

Vi prenumererar på de händelser [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)som skickas från .

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Signal för händelser som innehåller resultat för mellanliggande igenkänning.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): Signal för händelser som innehåller slutliga igenkänningsresultat (som anger ett lyckat igenkänningsförsök).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Signal för händelser som anger slutet på en igenkänningssession (operation).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): Signal för händelser som innehåller annullerade igenkänningsresultat (som anger ett igenkänningsförsök som avbröts som ett resultat eller en direkt begäran om annullering eller, alternativt, ett transport- eller protokollfel).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Med allt klart kan [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync)vi ringa.

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Dikteringsläge

När du använder kontinuerlig igenkänning kan du aktivera dikteringsbearbetning med hjälp av motsvarande "aktivera dikteringsfunktion". Det här läget gör att talkonfigurationsförekomsten tolkar ordbeskrivningar av meningsstrukturer, till exempel interpunktion. Till exempel skulle uttrycket "Bor du i stan frågetecken" tolkas som texten "Bor du i stan?".

Om du vill aktivera dikteringsläge använder du [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) metoden på din [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Ändra källspråk

En vanlig uppgift för taligenkänning är att ange indataspråk (eller källspråk). Låt oss ta en titt på hur du skulle ändra inmatningsspråket till tyska. I din kod, [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)hitta din , lägg sedan till den här raden direkt under den.

```cpp
config->SetSpeechRecognitionLanguage("fr-FR");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage)är en parameter som tar en sträng som ett argument. Du kan ange valfritt värde i listan över [språk/språk](../../../language-support.md)som stöds .

## <a name="improve-recognition-accuracy"></a>Förbättra igenkänningsnoggrannheten

Det finns några sätt att förbättra igenkänningsnoggrannheten med Tal-SDK. Låt oss ta en titt på fraslistor. Fraslistor används för att identifiera kända fraser i ljuddata, till exempel en persons namn eller en viss plats. Enstaka ord eller fullständiga fraser kan läggas till i en fraslista. Under igenkänningen används en post i en fraslista om en exakt matchning för hela frasen ingår i ljudet. Om det inte går att hitta en exakt matchning till frasen assisteras inte igenkänningen.

> [!IMPORTANT]
> Funktionen Fraslista är endast tillgänglig på engelska.

Om du vill använda en [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) fraslista skapar du först [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase)ett objekt och lägger sedan till specifika ord och fraser med .

Alla ändringar [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) som börjar gälla på nästa igenkänning eller efter en återanslutning till taltjänsten.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Om du behöver rensa din fraslista: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkänningsnoggrannheten

Fraslistor är bara ett alternativ för att förbättra igenkänningsnoggrannheten. Du kan också: 

* [Förbättra noggrannheten med anpassat tal](../../../how-to-custom-speech.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)