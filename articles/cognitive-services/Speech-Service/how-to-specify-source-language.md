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
ms.date: 10/26/2019
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 94b8fb026b61b52e8096cf54e1db30a6c260c04b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109950"
---
# <a name="specify-source-language-for-speech-to-text"></a>Ange käll språk för tal till text

I den här artikeln får du lära dig hur du anger käll språket för ljud indata som skickas till tal-SDK för tal igenkänning. Dessutom tillhandahålls exempel kod för att ange en anpassad tal modell för bättre igenkänning.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Ange käll språk iC#

Det första steget är att skapa en `SpeechConfig`:

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Ange sedan käll språket för ditt ljud med `SpeechRecognitionLanguage`:

```csharp
speechConfig.SpeechRecognitionLanguage = "de-DE";
```

Om du använder en anpassad modell för igenkänning kan du ange slut punkten med `EndpointId`:

```csharp
speechConfig.EndpointId = "The Endpoint ID for your custom model.";
```

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Ange käll språk iC++

I det här exemplet tillhandahålls käll språket explicit som en parameter med hjälp av metoden `FromConfig`.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

I det här exemplet tillhandahålls käll språket med hjälp av `SourceLanguageConfig`. Sedan skickas `sourceLanguageConfig` som en parameter för att `FromConfig` när du skapar `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

I det här exemplet tillhandahålls käll språket och den anpassade slut punkten med hjälp av `SourceLanguageConfig`. `sourceLanguageConfig` skickas som en parameter för att `FromConfig` när du skapar `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` och `SetEndpointId` är inaktuella metoder från `SpeechConfig`-klassen i C++ och Java. Användningen av dessa metoder rekommenderas inte och bör inte användas när du skapar en `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Ange käll språk i Java

I det här exemplet anges käll språket explicit när du skapar en ny `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

I det här exemplet tillhandahålls käll språket med hjälp av `SourceLanguageConfig`. Sedan skickas `sourceLanguageConfig` som en parameter när du skapar en ny `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

I det här exemplet tillhandahålls käll språket och den anpassade slut punkten med hjälp av `SourceLanguageConfig`. Sedan skickas `sourceLanguageConfig` som en parameter när du skapar en ny `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` och `setEndpointId` är inaktuella metoder från `SpeechConfig`-klassen i C++ och Java. Användningen av dessa metoder rekommenderas inte och bör inte användas när du skapar en `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Ange käll språk i python

Det första steget är att skapa en `speech_config`:

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

Ange sedan käll språket för ditt ljud med `speech_recognition_language`:

```Python
speech_config.speech_recognition_language="de-DE"
```

Om du använder en anpassad modell för igenkänning kan du ange slut punkten med `endpoint_id`:

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

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
