---
title: Välj ett taligenkänningsläge med Tal-SDK
titleSuffix: Azure Cognitive Services
description: Läs om hur du väljer det bästa igenkänningsläget när du använder Tal-SDK.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d997cb592d9d648998f2b44d9f61f465f05faeb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79079830"
---
# <a name="choose-a-speech-recognition-mode"></a>Välj ett taligenkänningsläge

När man överväger tal-till-text-igenkänningsåtgärder innehåller [Tal-SDK](speech-sdk.md) flera lägen för bearbetning av tal. Begreppsmässigt, ibland kallas *igenkänningsläge*. I den här artikeln jämförs de olika tolkningslägena.

## <a name="recognize-once"></a>Känn igen en gång

Om du vill bearbeta varje uttryck en "mening" i taget, använd "känna igen en gång"-funktionen. Den här metoden identifierar ett erkänt uttryck från indata som börjar i början av det identifierade talet tills nästa paus. Vanligtvis markerar en paus slutet på en mening eller tankegång.

I slutet av ett erkänt uttryck slutar tjänsten att bearbeta ljud från den begäran. Den maximala gränsen för igenkänning är en strafflängd på 20 sekunder.

::: zone pivot="programming-language-csharp"

Mer information om `RecognizeOnceAsync` hur du använder funktionen finns i [.NET Speech SDK docs](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Mer information om `RecognizeOnceAsync` hur du använder funktionen finns i [C++ Speech SDK docs](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Mer information om `recognizeOnceAsync` hur du använder funktionen finns i [Java Speech SDK docs](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Mer information om `recognize_once` hur du använder funktionen finns i [Python Speech SDK-dokument](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Fler språk finns i [tal-SDK-referensdokumenten](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Kontinuerlig igenkänning

Om du behöver långvarig igenkänning använder du start- och motsvarande stoppfunktioner för kontinuerlig igenkänning. Startfunktionen startar och fortsätter att bearbeta alla yttranden tills du anropar stoppfunktionen, eller tills för mycket tid i tystnad har passerat. När du använder det kontinuerliga läget, se till att registrera dig till de olika händelser som kommer att skjuta vid förekomst. Händelsen "igenkänd" utlöses till exempel när taligenkänning inträffar. Du måste ha en händelsehanterare på plats för att hantera igenkänning.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end
::: zone pivot="programming-language-more"

Fler språk finns i [tal-SDK-referensdokumenten](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Diktamen

När du använder kontinuerlig igenkänning kan du aktivera dikteringsbearbetning med hjälp av motsvarande "aktivera dikteringsfunktion". Det här läget gör att talkonfigurationsförekomsten tolkar ordbeskrivningar av meningsstrukturer, till exempel interpunktion. Till exempel skulle uttrycket "Bor du i stan frågetecken" tolkas som texten "Bor du i stan?".

::: zone pivot="programming-language-csharp"

Mer information om `EnableDictation` hur du använder funktionen finns i [.NET Speech SDK docs](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Mer information om `EnableDictation` hur du använder funktionen finns i [C++ Speech SDK docs](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Mer information om `enableDictation` hur du använder funktionen finns i [Java Speech SDK docs](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Mer information om `enable_dictation` hur du använder funktionen finns i [Python Speech SDK-dokument](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Fler språk finns i [tal-SDK-referensdokumenten](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska ytterligare Tal SDK-exempel på GitHub](https://aka.ms/csspeech/samples)
