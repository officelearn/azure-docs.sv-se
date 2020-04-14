---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: cd7a8c0104a903f49c5ce8ae1e1a561d53c0fbf5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266640"
---
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en Azure-konto- och taltjänstprenumeration. Om du inte har ett konto och en prenumeration [provar du taltjänsten utan kostnad](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera Tal-SDK. Beroende på din plattform följer du instruktionerna under avsnittet <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Hämta tal-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i artikeln Tal-SDK.

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen `import` i den här artikeln inkluderar du följande satser högst upp i python-kodfilen.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Känsliga data- och miljövariabler

Exempelkällkoden i den här artikeln beror på miljövariabler för lagring av känsliga data, till exempel prenumerationsnyckeln talresurs och region. Python-kodfilen innehåller två värden som tilldelas från värddatorernas `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`miljövariabler, nämligen och . Båda dessa variabler är i det globala omfånget, vilket gör dem tillgängliga inom funktionsdefinitionen av kodfilen. Mer information om miljövariabler finns i [miljövariabler och programkonfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
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

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Ändra källspråk

En gemensam uppgift för talöversättning är att ange indataspråk (eller källspråk). Låt oss ta en titt på hur du skulle ändra inmatningsspråket till italienska. I koden interagerar [`SpeechTranslationConfig`][config] du med instansen `speech_recognition_language` och tilldelar egenskapen.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

Egenskapen [`speech_recognition_language`][recognitionlang] förväntar sig en språkspråksformatsträng. Du kan ange valfritt värde i kolumnen **Språk i** listan över [språk/språk](../../../language-support.md)som stöds .

## <a name="add-translation-language"></a>Lägga till översättningsspråk

En annan gemensam uppgift för talöversättning är att ange målöversättningsspråk, minst ett krävs men multiplar stöds. I följande kodavsnitt, både franska och tyska som översättning språk mål.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Vid varje [`add_target_language`][addlang]samtal till anges ett nytt målöversättningsspråk. Med andra ord, när tal känns igen från källspråket, är varje målöversättning tillgänglig som en del av den resulterande översättningen.

## <a name="initialize-a-translation-recognizer"></a>Initiera en översättningsre recognizeer

När du har [`SpeechTranslationConfig`][config]skapat en är nästa steg [`TranslationRecognizer`][recognizer]att initiera en . När du initierar en [`TranslationRecognizer`][recognizer]måste du skicka `translation_config`den till . Konfigurationsobjektet innehåller de autentiseringsuppgifter som taltjänsten kräver för att validera din begäran.

Så här ser du ut så här [`TranslationRecognizer`][recognizer] om du känner igen tal med enhetens standardmikrofon:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Om du vill ange ljudinmatningsenheten måste du [`AudioConfig`][audioconfig] skapa en `audio_config` och ange [`TranslationRecognizer`][recognizer]parametern när du initierar .

> [!TIP]
> [Läs om hur du hämtar enhets-ID:et för ljudinmatningsenheten](../../../how-to-select-audio-input-devices.md).

Först ska du referera `AudioConfig` till objektet på följande sätt:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Om du vill tillhandahålla en ljudfil i stället för att använda `audioConfig`en mikrofon måste du fortfarande ange en . Men när du [`AudioConfig`][audioconfig]skapar en , `use_default_microphone=True`i stället för `filename="path-to-file.wav"` att `filename` ringa med , ska du ringa med och ange parametern.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Översätt tal

Om du vill översätta tal förlitar sig Speech SDK på en mikrofon eller en ljudfilsingång. Taligenkänning sker före talöversättning. När alla objekt har initierats, anropa funktionen recognize-once och få resultatet.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Mer information om tal-till-text finns [i grunderna i taligenkänning](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Syntetisera översättningar

Efter en lyckad taligenkänning och översättning innehåller resultatet alla översättningar i en ordlista. Ordlistenyckeln [`translations`][translations] är målöversättningsspråket och värdet är den översatta texten. Igenkänd tal kan översättas och sedan syntetiseras på ett annat språk (tal till tal).

### <a name="event-based-synthesis"></a>Händelsebaserad syntes

Objektet `TranslationRecognizer` exponerar `Synthesizing` en händelse. Händelsen utlöses flera gånger och ger en mekanism för att hämta det syntetiserade ljudet från resultatet för översättningsigenkänning. Om du översätter till flera språk läser du [manuell syntes](#manual-synthesis). Ange syntesrösten genom [`voice_name`][voicename] att tilldela en och `Synthesizing` ange en händelsehanterare för händelsen, hämta ljudet. I följande exempel sparas det översatta ljudet som en *WAV-fil.*

> [!IMPORTANT]
> Den händelsebaserade syntesen fungerar bara med en enda översättning, lägg **inte** till flera målöversättningsspråk. Dessutom bör [`voice_name`][voicename] det vara samma språk som målöversättningsspråket, till exempel. `"de"` kunde mappa till `"de-DE-Hedda"`.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Manuell syntes

Ordlistan [`translations`][translations] kan användas för att syntetisera ljud från översättningstexten. Iterera genom varje översättning och syntetisera översättningen. När du `SpeechSynthesizer` skapar `SpeechConfig` en instans måste [`speech_synthesis_voice_name`][speechsynthesisvoicename] objektet ha egenskapen inställd på önskad röst. Följande exempel översätts till fem språk, och varje översättning syntetiseras sedan till en ljudfil på motsvarande neurala språk.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Mer information om talsyntes [finns i grunderna i talsyntes](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
