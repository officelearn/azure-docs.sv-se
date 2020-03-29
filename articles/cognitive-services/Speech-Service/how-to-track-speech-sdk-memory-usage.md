---
title: Så här spårar du minnesanvändning för Tal SDK - Taltjänst
titleSuffix: Azure Cognitive Services
description: Taltjänsten SDK stöder många programmeringsspråk för tal-till-text- och text-till-tal-konvertering, tillsammans med talöversättning. I den här artikeln beskrivs verktyg för minneshantering som är inbyggda i SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456436"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Så här spårar du minnesanvändning för Tal SDK

Tal-SDK baseras på en inbyggd kodbas som projiceras i flera programmeringsspråk genom en serie interoperabilitetslager. Varje språkspecifik projektion har idiomatiskt korrekta funktioner för att hantera objektets livscykel. Dessutom innehåller Tal-SDK minneshanteringsverktyg för att spåra resursanvändning med objektloggning och objektgränser. 

## <a name="how-to-read-object-logs"></a>Så här läser du objektloggar

Om [Speech SDK-loggning är aktiverat](how-to-use-logging.md)skickas spårningstaggar ut för att aktivera historisk objektobservation. Dessa taggar inkluderar: 

* `TrackHandle` eller `StopTracking` 
* Objekttypen
* Det aktuella antalet objekt som spåras typen av objekt och det aktuella numret som spåras.

Här är en exempellogg: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Ange en varningströskel

Du har möjlighet att skapa en varningströskel och om tröskelvärdet överskrids (förutsatt att loggning är aktiverat) loggas ett varningsmeddelande. Varningsmeddelandet innehåller en dump av alla objekt som finns tillsammans med deras räkning. Den här informationen kan användas för att bättre förstå problem. 

Om du vill aktivera ett varningströskel måste det anges på ett `SpeechConfig` objekt. Det här objektet kontrolleras när en ny identifierare skapas. I följande exempel bör vi anta att du har `SpeechConfig` `config`skapat en förekomst av anropade:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Standardvärdet för den här egenskapen är 10 000.

## <a name="set-an-error-threshold"></a>Ange ett feltröskel 

Med tal-SDK kan du ange det maximala antalet objekt som tillåts vid en viss tidpunkt. Om den här inställningen är aktiverad misslyckas försök att skapa nya objekt. Befintliga objekt fortsätter att fungera.

Här är ett exempelfel:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Om du vill aktivera ett feltröskel måste det anges på ett `SpeechConfig` objekt. Det här objektet kontrolleras när en ny identifierare skapas. I följande exempel bör vi anta att du har `SpeechConfig` `config`skapat en förekomst av anropade:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Standardvärdet för den här egenskapen är det `size_t` plattformsspecifika högsta värdet för en datatyp. En typisk igenkänning förbrukar mellan 7 och 10 interna objekt.

## <a name="next-steps"></a>Nästa steg

* [Skaffa provprenumerationen för Taltjänsten](get-started.md)
* [Lär dig hur du känner igen tal med en mikrofon](quickstarts/speech-to-text-from-microphone.md)