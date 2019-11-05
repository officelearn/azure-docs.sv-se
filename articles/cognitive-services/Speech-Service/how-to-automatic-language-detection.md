---
title: 'Anvisningar: använda automatisk språk identifiering för tal till text-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: 'Tal-SDK: n stöder automatisk språk identifiering för tal till text. När du använder den här funktionen jämförs ljudet med den angivna listan med språk och den mest sannolika matchningen fastställs. Det returnerade värdet kan sedan användas för att välja den språk modell som används för tal till text.'
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: e156704d3ad54c3437f921fae536a497e1d94868
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506971"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatisk språk identifiering för tal till text

Automatisk språk identifiering används för att avgöra den mest sannolika matchningen för ljud som skickas till tal-SDK: n jämfört med en lista med angivna språk. Värdet som returneras av automatisk språk identifiering används sedan för att välja språk modell för tal till text, vilket ger en mer exakt avskrift. Information om vilka språk som är tillgängliga finns i [språk stöd](language-support.md).

I den här artikeln får du lära dig hur du använder `AutoDetectSourceLanguageConfig` för att skapa ett `SpeechRecognizer`-objekt och hämta det identifierade språket.

> [!IMPORTANT]
> Den här funktionen är endast tillgänglig för tal-SDK C++ för och talet SDK för Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatisk språk identifiering med talet SDK

Automatisk språk identifiering har för närvarande en gräns på Server sidan på två språk per identifiering. Behåll den här begränsningen i åtanke när du `AudoDetectSourceLanguageConfig`-objektet. I exemplen nedan skapar du en `AutoDetectSourceLanguageConfig`och använder den sedan för att skapa en `SpeechRecognizer`.

>[!TIP]
> Du kan också ange en anpassad modell som ska användas när du utför tal till text. Mer information finns i [använda en anpassad modell för automatisk språk identifiering](#use-a-custom-model-for-automatic-language-detection).

Följande kodfragment visar hur du använder automatisk språk identifiering i dina appar:

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Använd en anpassad modell för automatisk språk identifiering

Förutom språk identifiering med tal tjänst modeller kan du ange en anpassad modell för bättre igenkänning. Om ingen anpassad modell har angetts använder tjänsten standard språk modellen.

Kodfragmenten nedan visar hur du anger en anpassad modell i ditt anrop till röst tjänsten. Om det identifierade språket är `en-US`används standard modellen. Om det identifierade språket är `fr-FR`används slut punkten för den anpassade modellen:

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om tal SDK-referens](speech-sdk.md)
