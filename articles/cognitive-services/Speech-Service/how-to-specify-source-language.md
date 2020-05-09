---
title: Ange käll språk för tal till text
titleSuffix: Azure Cognitive Services
description: Med tal-SDK kan du ange käll språket när du konverterar tal till text. Den här artikeln beskriver hur du använder metoderna FromConfig och SourceLanguageConfig för att låta röst tjänsten känna till käll språket och tillhandahålla ett anpassat modell mål.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 07ca4c519a04910e7e126d3481afb17d2b5dc350
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977727"
---
# <a name="specify-source-language-for-speech-to-text"></a>Ange käll språk för tal till text

I den här artikeln får du lära dig hur du anger käll språket för ljud indata som skickas till tal-SDK för tal igenkänning. Dessutom tillhandahålls exempel kod för att ange en anpassad tal modell för bättre igenkänning.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Ange käll språk i C #

I det här exemplet tillhandahålls käll språket explicit som en parameter med hjälp av `SpeechRecognizer` konstruktionen.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

I det här exemplet anges käll språket med hjälp av `SourceLanguageConfig`. Sedan skickas den `sourceLanguageConfig` som en parameter för att `SpeechRecognizer` konstruera.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

I det här exemplet tillhandahålls käll språket och den anpassade slut punkten med `SourceLanguageConfig`hjälp av. Sedan skickas den `sourceLanguageConfig` som en parameter för att `SpeechRecognizer` konstruera.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`och `EndpointId` set-metoder är föråldrade `SpeechConfig` från klassen i C#. Användningen av dessa metoder rekommenderas inte och bör inte användas när du skapar en `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Ange käll språk i C++

I det här exemplet tillhandahålls käll språket explicit som en parameter med `FromConfig` metoden.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

I det här exemplet anges käll språket med hjälp av `SourceLanguageConfig`. Sedan skickas den `sourceLanguageConfig` som en parameter till `FromConfig` när du `recognizer`skapar.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

I det här exemplet tillhandahålls käll språket och den anpassade slut punkten med `SourceLanguageConfig`hjälp av. `sourceLanguageConfig` Skickas som en parameter till `FromConfig` när du `recognizer`skapar.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`och `SetEndpointId` är föråldrade metoder från `SpeechConfig` -klassen i C++ och Java. Användningen av dessa metoder rekommenderas inte och bör inte användas när du skapar en `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Ange käll språk i Java

I det här exemplet anges käll språket explicit när du skapar en ny `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

I det här exemplet anges käll språket med hjälp av `SourceLanguageConfig`. Sedan skickas den `sourceLanguageConfig` som en parameter när du skapar en ny `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

I det här exemplet tillhandahålls käll språket och den anpassade slut punkten med `SourceLanguageConfig`hjälp av. Sedan skickas den `sourceLanguageConfig` som en parameter när du skapar en ny `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`och `setEndpointId` är föråldrade metoder från `SpeechConfig` -klassen i C++ och Java. Användningen av dessa metoder rekommenderas inte och bör inte användas när du skapar en `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Ange käll språk i python

I det här exemplet tillhandahålls käll språket explicit som en parameter med hjälp av `SpeechRecognizer` konstruktionen.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

I det här exemplet anges käll språket med hjälp av `SourceLanguageConfig`. Sedan skickas den `SourceLanguageConfig` som en parameter för att `SpeechRecognizer` konstruera.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

I det här exemplet tillhandahålls käll språket och den anpassade slut punkten med `SourceLanguageConfig`hjälp av. Sedan skickas den `SourceLanguageConfig` som en parameter för att `SpeechRecognizer` konstruera.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`och `endpoint_id` egenskaper är inaktuella från `SpeechConfig` klassen i python. Användningen av dessa egenskaper rekommenderas inte och bör inte användas när du skapar en `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-javascript"

## <a name="how-to-specify-source-language-in-javascript"></a>Ange käll språk i Java Script

Det första steget är att skapa en `SpeechConfig`:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Ange sedan käll språket för ditt ljud med `speechRecognitionLanguage`:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Om du använder en anpassad modell för igenkänning kan du ange slut punkten med `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```
::: zone-end

::: zone pivot="programming-language-objectivec"

## <a name="how-to-specify-source-language-in-objective-c"></a>Ange käll språk i mål-C

Det första steget är att skapa en `speechConfig`:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Ange sedan käll språket för ditt ljud med `speechRecognitionLanguage`:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Om du använder en anpassad modell för igenkänning kan du ange slut punkten med `endpointId`:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Se även

* En lista över språk som stöds och nationella inställningar för tal till text finns i [språk stöd](language-support.md).

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om tal SDK-referens](speech-sdk.md)
