---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 0f5570f1c80fdecb4db6c8baf22ad05f1e4a32a6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266662"
---
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en Azure-konto- och taltjänstprenumeration. Om du inte har ett konto och en prenumeration [provar du taltjänsten utan kostnad](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera Tal-SDK. Beroende på din plattform följer du instruktionerna under avsnittet <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Hämta tal-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i artikeln Tal-SDK.

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen `using` i den här artikeln tar du med följande satser högst upp i *Program.cs* filen.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Känsliga data- och miljövariabler

Exempelkällkoden i den här artikeln beror på miljövariabler för lagring av känsliga data, till exempel prenumerationsnyckeln talresurs och region. Klassen `Program` innehåller två `static readonly string` värden som tilldelas från värddatorernas `SPEECH__SUBSCRIPTION__KEY` miljövariabler, nämligen och `SPEECH__SERVICE__REGION`. Båda dessa fält är i klassen omfattning, vilket gör dem tillgängliga inom metod organ i klassen. Mer information om miljövariabler finns i [miljövariabler och programkonfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
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

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Ändra källspråk

En gemensam uppgift för talöversättning är att ange indataspråk (eller källspråk). Låt oss ta en titt på hur du skulle ändra inmatningsspråket till italienska. I koden interagerar [`SpeechTranslationConfig`][config] du med instansen `SpeechRecognitionLanguage` och tilldelar egenskapen.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

Egenskapen [`SpeechRecognitionLanguage`][recognitionlang] förväntar sig en språkspråksformatsträng. Du kan ange valfritt värde i kolumnen **Språk i** listan över [språk/språk](../../../language-support.md)som stöds .

## <a name="add-translation-language"></a>Lägga till översättningsspråk

En annan gemensam uppgift för talöversättning är att ange målöversättningsspråk, minst ett krävs men multiplar stöds. I följande kodavsnitt, både franska och tyska som översättning språk mål.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Vid varje [`AddTargetLanguage`][addlang]samtal till anges ett nytt målöversättningsspråk. Med andra ord, när tal känns igen från källspråket, är varje målöversättning tillgänglig som en del av den resulterande översättningen.

## <a name="initialize-a-translation-recognizer"></a>Initiera en översättningsre recognizeer

När du har [`SpeechTranslationConfig`][config]skapat en är nästa steg [`TranslationRecognizer`][recognizer]att initiera en . När du initierar en [`TranslationRecognizer`][recognizer]måste du skicka `translationConfig`den till . Konfigurationsobjektet innehåller de autentiseringsuppgifter som taltjänsten kräver för att validera din begäran.

Så här ser du ut så här [`TranslationRecognizer`][recognizer] om du känner igen tal med enhetens standardmikrofon:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Om du vill ange ljudinmatningsenheten måste du [`AudioConfig`][audioconfig] skapa en `audioConfig` och ange [`TranslationRecognizer`][recognizer]parametern när du initierar .

> [!TIP]
> [Läs om hur du hämtar enhets-ID:et för ljudinmatningsenheten](../../../how-to-select-audio-input-devices.md).

Först ska du referera `AudioConfig` till objektet på följande sätt:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Om du vill tillhandahålla en ljudfil i stället för att använda `audioConfig`en mikrofon måste du fortfarande ange en . Men när du [`AudioConfig`][audioconfig]skapar en `FromDefaultMicrophoneInput`, i stället `FromWavFileInput` för `filename` att ringa , kommer du att anropa och skicka parametern.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Översätt tal

Om du vill översätta tal förlitar sig Speech SDK på en mikrofon eller en ljudfilsingång. Taligenkänning sker före talöversättning. När alla objekt har initierats, anropa funktionen recognize-once och få resultatet.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Mer information om tal-till-text finns [i grunderna i taligenkänning](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Syntetisera översättningar

Efter en lyckad taligenkänning och översättning innehåller resultatet alla översättningar i en ordlista. Ordlistenyckeln [`Translations`][translations] är målöversättningsspråket och värdet är den översatta texten. Igenkänd tal kan översättas och sedan syntetiseras på ett annat språk (tal till tal).

### <a name="event-based-synthesis"></a>Händelsebaserad syntes

Objektet `TranslationRecognizer` exponerar `Synthesizing` en händelse. Händelsen utlöses flera gånger och ger en mekanism för att hämta det syntetiserade ljudet från resultatet för översättningsigenkänning. Om du översätter till flera språk läser du [manuell syntes](#manual-synthesis). Ange syntesrösten genom [`VoiceName`][voicename] att tilldela en och `Synthesizing` ange en händelsehanterare för händelsen, hämta ljudet. I följande exempel sparas det översatta ljudet som en *WAV-fil.*

> [!IMPORTANT]
> Den händelsebaserade syntesen fungerar bara med en enda översättning, lägg **inte** till flera målöversättningsspråk. Dessutom bör [`VoiceName`][voicename] det vara samma språk som målöversättningsspråket, till exempel. `"de"` kunde mappa till `"de-DE-Hedda"`.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Manuell syntes

Ordlistan [`Translations`][translations] kan användas för att syntetisera ljud från översättningstexten. Iterera genom varje översättning och syntetisera översättningen. När du `SpeechSynthesizer` skapar `SpeechConfig` en instans måste [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] objektet ha egenskapen inställd på önskad röst. Följande exempel översätts till fem språk, och varje översättning syntetiseras sedan till en ljudfil på motsvarande neurala språk.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Mer information om talsyntes [finns i grunderna i talsyntes](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
