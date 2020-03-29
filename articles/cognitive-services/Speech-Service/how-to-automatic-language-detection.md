---
title: Så här använder du automatisk språkidentifiering för tal till text
titleSuffix: Azure Cognitive Services
description: Tal-SDK stöder automatisk språkidentifiering för tal till text. När du använder den här funktionen jämförs ljudet mot en lista med språk som tillhandahålls och den mest sannolika matchningen bestäms. Det returnerade värdet kan sedan användas för att välja den språkmodell som används för tal till text.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5592fc3e50db892c6abb09fc2516b8e1c03f0f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239604"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatisk språkidentifiering för tal till text

Automatisk språkidentifiering används för att avgöra den mest sannolika matchningen för ljud som skickas till Tal-SDK jämfört med en lista över angivna språk. Värdet som returneras av automatisk språkidentifiering används sedan för att välja språkmodell för tal till text, vilket ger dig en mer exakt transkription. Information om vilka språk som är tillgängliga finns i [Språkstöd](language-support.md).

I den här artikeln får du `AutoDetectSourceLanguageConfig` lära `SpeechRecognizer` dig hur du använder för att skapa ett objekt och hämta det identifierade språket.

> [!IMPORTANT]
> Den här funktionen är endast tillgänglig för Speech SDK för C#, C++, Java och Python.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatisk språkidentifiering med Tal-SDK

Automatisk språkidentifiering har för närvarande en gräns på två språk per identifiering på tjänstsidan. Ha denna begränsning i `AudoDetectSourceLanguageConfig` åtanke när du bygger ditt objekt. I exemplen nedan ska du `AutoDetectSourceLanguageConfig`skapa en och `SpeechRecognizer`sedan använda den för att skapa en .

> [!TIP]
> Du kan också ange en anpassad modell som ska användas när du utför tal till text. Mer information finns i [Använda en anpassad modell för automatisk språkidentifiering](#use-a-custom-model-for-automatic-language-detection).

Följande kodavsnitt illustrerar hur du använder automatisk språkidentifiering i dina appar:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Använda en anpassad modell för automatisk språkidentifiering

Förutom språkidentifiering med taltjänstmodeller kan du ange en anpassad modell för förbättrad igenkänning. Om en anpassad modell inte tillhandahålls använder tjänsten standardspråkmodellen.

Kodavsnitten nedan visar hur du anger en anpassad modell i anropet till taltjänsten. Om det identifierade `en-US`språket är används standardmodellen. Om det identifierade `fr-FR`språket är används slutpunkten för den anpassade modellen:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>Nästa steg

- [Referensdokumentation för Tal SDK](speech-sdk.md)
