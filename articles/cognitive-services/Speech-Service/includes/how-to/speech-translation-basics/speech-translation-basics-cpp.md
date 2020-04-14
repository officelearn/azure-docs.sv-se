---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 7c57952d0b78271bbcc45bd282385524772f0f9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266651"
---
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en Azure-konto- och taltjänstprenumeration. Om du inte har ett konto och en prenumeration [provar du taltjänsten utan kostnad](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera Tal-SDK. Beroende på din plattform följer du instruktionerna under avsnittet <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Hämta tal-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i artikeln Tal-SDK.

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen `#include` i `using` den här artikeln inkluderar du följande och satser högst upp i C++-kodfilen.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Känsliga data- och miljövariabler

Exempelkällkoden i den här artikeln beror på miljövariabler för lagring av känsliga data, till exempel prenumerationsnyckeln talresurs och region. C++-kodfilen innehåller två strängvärden som tilldelas från värddatorernas `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`miljövariabler, nämligen och . Båda dessa fält är i klassen omfattning, vilket gör dem tillgängliga inom metod organ i klassen. Mer information om miljövariabler finns i [miljövariabler och programkonfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
```

## <a name="create-a-speech-translation-configuration"></a>Skapa en konfiguration av talöversättning

Om du vill anropa taltjänsten med tal-SDK måste du skapa en [`SpeechTranslationConfig`][config]. Den här klassen innehåller information om din prenumeration, till exempel din nyckel och associerade region, slutpunkt, värd eller auktoriseringstoken.

> [!TIP]
> Oavsett om du utför taligenkänning, talsyntes, översättning eller avsiktsigenkänning skapar du alltid en konfiguration.

Det finns några sätt som du [`SpeechTranslationConfig`][config]kan initiera en:

* Med en prenumeration: skicka in en nyckel och den associerade regionen.
* Med en slutpunkt: passera i en taltjänstslutpunkt. En nyckel eller auktoriseringstoken är valfri.
* Med en värd: skicka in en värdadress. En nyckel eller auktoriseringstoken är valfri.
* Med en auktoriseringstoken: skicka in en auktoriseringstoken och den associerade regionen.

Låt oss ta en titt [`SpeechTranslationConfig`][config] på hur en skapas med hjälp av en nyckel och region. Se [regionens supportsida](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regionidentifierare.

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Ändra källspråk

En gemensam uppgift för talöversättning är att ange indataspråk (eller källspråk). Låt oss ta en titt på hur du skulle ändra inmatningsspråket till italienska. I koden interagerar [`SpeechTranslationConfig`][config] du med `SetSpeechRecognitionLanguage` instansen och anropar metoden.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

Egenskapen [`SpeechRecognitionLanguage`][recognitionlang] förväntar sig en språkspråksformatsträng. Du kan ange valfritt värde i kolumnen **Språk i** listan över [språk/språk](../../../language-support.md)som stöds .

## <a name="add-translation-language"></a>Lägga till översättningsspråk

En annan gemensam uppgift för talöversättning är att ange målöversättningsspråk, minst ett krävs men multiplar stöds. I följande kodavsnitt, både franska och tyska som översättning språk mål.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

Vid varje [`AddTargetLanguage`][addlang]samtal till anges ett nytt målöversättningsspråk. Med andra ord, när tal känns igen från källspråket, är varje målöversättning tillgänglig som en del av den resulterande översättningen.

## <a name="initialize-a-translation-recognizer"></a>Initiera en översättningsre recognizeer

När du har [`SpeechTranslationConfig`][config]skapat en är nästa steg [`TranslationRecognizer`][recognizer]att initiera en . När du initierar en [`TranslationRecognizer`][recognizer]måste du skicka `translationConfig`den till . Konfigurationsobjektet innehåller de autentiseringsuppgifter som taltjänsten kräver för att validera din begäran.

Så här ser du ut så här [`TranslationRecognizer`][recognizer] om du känner igen tal med enhetens standardmikrofon:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Om du vill ange ljudinmatningsenheten måste du [`AudioConfig`][audioconfig] skapa en `audioConfig` och ange [`TranslationRecognizer`][recognizer]parametern när du initierar .

> [!TIP]
> [Läs om hur du hämtar enhets-ID:et för ljudinmatningsenheten](../../../how-to-select-audio-input-devices.md).

Först ska du referera `AudioConfig` till objektet på följande sätt:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Om du vill tillhandahålla en ljudfil i stället för att använda `audioConfig`en mikrofon måste du fortfarande ange en . Men när du [`AudioConfig`][audioconfig]skapar en `FromDefaultMicrophoneInput`, i stället `FromWavFileInput` för `filename` att ringa , kommer du att anropa och skicka parametern.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Översätt tal

Om du vill översätta tal förlitar sig Speech SDK på en mikrofon eller en ljudfilsingång. Taligenkänning sker före talöversättning. När alla objekt har initierats, anropa funktionen recognize-once och få resultatet.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Mer information om tal-till-text finns [i grunderna i taligenkänning](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Syntetisera översättningar

Efter en lyckad taligenkänning och översättning innehåller resultatet alla översättningar i en ordlista. Ordlistenyckeln [`Translations`][translations] är målöversättningsspråket och värdet är den översatta texten. Igenkänd tal kan översättas och sedan syntetiseras på ett annat språk (tal till tal).

### <a name="event-based-synthesis"></a>Händelsebaserad syntes

Objektet `TranslationRecognizer` exponerar `Synthesizing` en händelse. Händelsen utlöses flera gånger och ger en mekanism för att hämta det syntetiserade ljudet från resultatet för översättningsigenkänning. Om du översätter till flera språk läser du [manuell syntes](#manual-synthesis). Ange syntesrösten genom [`SetVoiceName`][voicename] att tilldela en och `Synthesizing` ange en händelsehanterare för händelsen, hämta ljudet. I följande exempel sparas det översatta ljudet som en *WAV-fil.*

> [!IMPORTANT]
> Den händelsebaserade syntesen fungerar bara med en enda översättning, lägg **inte** till flera målöversättningsspråk. Dessutom bör [`SetVoiceName`][voicename] det vara samma språk som målöversättningsspråket, till exempel. `"de"` kunde mappa till `"de-DE-Hedda"`.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Manuell syntes

Ordlistan [`Translations`][translations] kan användas för att syntetisera ljud från översättningstexten. Iterera genom varje översättning och syntetisera översättningen. När du `SpeechSynthesizer` skapar `SpeechConfig` en instans måste [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] objektet ha egenskapen inställd på önskad röst. Följande exempel översätts till fem språk, och varje översättning syntetiseras sedan till en ljudfil på motsvarande neurala språk.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Mer information om talsyntes [finns i grunderna i talsyntes](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
