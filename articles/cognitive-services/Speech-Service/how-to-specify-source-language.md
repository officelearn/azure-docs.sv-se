---
title: Så här anger du källspråk för tal till text
titleSuffix: Azure Cognitive Services
description: Med Tal-SDK kan du ange källspråket när du konverterar tal till text. I den här artikeln beskrivs hur du använder metoderna FromConfig och SourceLanguageConfig för att låta taltjänsten veta källspråket och ange ett anpassat modellmål.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235986"
---
# <a name="specify-source-language-for-speech-to-text"></a>Ange källspråk för tal till text

I den här artikeln får du lära dig hur du anger källspråket för en ljudinmatning som skickas till Tal-SDK för taligenkänning. Dessutom finns exempelkod för att ange en anpassad talmodell för förbättrad igenkänning.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Så här anger du källspråk i C #

I det här exemplet anges källspråket `SpeechRecognizer` uttryckligen som en parameter med hjälp av konstruktion.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

I det här exemplet anges `SourceLanguageConfig`källspråket med . Sedan skickas `sourceLanguageConfig` den som en `SpeechRecognizer` parameter för att konstruera.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

I det här exemplet anges källspråket `SourceLanguageConfig`och den anpassade slutpunkten med . Sedan skickas `sourceLanguageConfig` den som en `SpeechRecognizer` parameter för att konstruera.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`och `EndpointId` set-metoder är inaktuella `SpeechConfig` från klassen i C#. Användningen av dessa metoder avskräcks och bör inte `SpeechRecognizer`användas när du konstruerar en .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Så här anger du källspråk i C++

I det här exemplet anges källspråket uttryckligen `FromConfig` som en parameter med hjälp av metoden.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

I det här exemplet anges `SourceLanguageConfig`källspråket med . Sedan skickas `sourceLanguageConfig` den som en `FromConfig` parameter `recognizer`till när du skapar .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

I det här exemplet anges källspråket `SourceLanguageConfig`och den anpassade slutpunkten med . Skickas `sourceLanguageConfig` som en parameter `FromConfig` till `recognizer`när du skapar .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`och `SetEndpointId` är inaktuella metoder `SpeechConfig` från klassen i C++ och Java. Användningen av dessa metoder avskräcks och bör inte `SpeechRecognizer`användas när du konstruerar en .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Så här anger du källspråk i Java

I det här exemplet anges källspråket `SpeechRecognizer`uttryckligen när du skapar ett nytt .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

I det här exemplet anges `SourceLanguageConfig`källspråket med . Sedan skickas `sourceLanguageConfig` den som en parameter `SpeechRecognizer`när du skapar en ny .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

I det här exemplet anges källspråket `SourceLanguageConfig`och den anpassade slutpunkten med . Sedan skickas `sourceLanguageConfig` den som en parameter `SpeechRecognizer`när du skapar en ny .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`och `setEndpointId` är inaktuella metoder `SpeechConfig` från klassen i C++ och Java. Användningen av dessa metoder avskräcks och bör inte `SpeechRecognizer`användas när du konstruerar en .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Så här anger du källspråk i Python

I det här exemplet anges källspråket `SpeechRecognizer` uttryckligen som en parameter med hjälp av konstruktion.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

I det här exemplet anges `SourceLanguageConfig`källspråket med . Sedan skickas `SourceLanguageConfig` den som en `SpeechRecognizer` parameter för att konstruera.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

I det här exemplet anges källspråket `SourceLanguageConfig`och den anpassade slutpunkten med . Sedan skickas `SourceLanguageConfig` den som en `SpeechRecognizer` parameter för att konstruera.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`och `endpoint_id` egenskaper är inaktuella `SpeechConfig` från klassen i Python. Användningen av dessa egenskaper avråds och bör inte användas `SpeechRecognizer`när du konstruerar en .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Så här anger du källspråk i Javascript

Det första steget är `SpeechConfig`att skapa en:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Ange sedan källspråket för `speechRecognitionLanguage`ditt ljud med:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Om du använder en anpassad modell för igenkänning `endpointId`kan du ange slutpunkten med:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Så här anger du källspråk i Objective-C

Det första steget är `speechConfig`att skapa en:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Ange sedan källspråket för `speechRecognitionLanguage`ditt ljud med:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Om du använder en anpassad modell för igenkänning `endpointId`kan du ange slutpunkten med:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Se även

* En lista över språk och språk som stöds för tal till text finns i [Språkstöd](language-support.md).

## <a name="next-steps"></a>Nästa steg

* [Referensdokumentation för Tal SDK](speech-sdk.md)
