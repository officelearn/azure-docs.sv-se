---
title: Välj ett tal igenkännings läge med tal-SDK
titleSuffix: Azure Cognitive Services
description: Lär dig hur du väljer det bästa igenkännings läget när du använder tal-SDK.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 51bf005bdad4197120fed96894ac1cdd150738ee
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75935231"
---
# <a name="choose-a-speech-recognition-mode"></a>Välj ett tal igenkännings läge

När du överväger tal-till-text-igenkänning tillhandahåller [tal-SDK](speech-sdk.md) flera lägen för tal bearbetning. Begreppsmässigt kallas ibland *igenkännings läge*. I den här artikeln jämförs de olika igenkännings lägena.

## <a name="recognize-once"></a>Identifiera en gång

Om du vill bearbeta varje uttryck en "mening" i taget använder du funktionen "identifiera en gång". Den här metoden identifierar en känd uttryck från indatamängden från början av identifierad tal tills nästa paus. Normalt markerar en paus en menings slut eller en rad-till-tanke.

I slutet av en känd uttryck slutar tjänsten att bearbeta ljudet från denna begäran. Den maximala gränsen för igenkänning är en menings varaktighet på 20 sekunder.

::: zone pivot="programming-language-csharp"

Mer information om hur du använder funktionen `RecognizeOnceAsync` finns i [.net Speech SDK-dokument](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync().ConfigureAwait(false);
```

::: zone-end
::: zone pivot="programming-language-cpp"

Mer information om hur du använder funktionen `RecognizeOnceAsync` finns i [ C++ tal SDK-dokumenten](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Mer information om hur du använder funktionen `recognizeOnceAsync` finns i [Java Speech SDK-dokument](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Mer information om hur du använder `recognize_once`-funktionen finns i [python Speech SDK-dokument](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Ytterligare språk finns i [referens dokument för tal-SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Kontinuerlig igenkänning

Om du behöver tids krävande igenkännings igenkänning använder du Start-och motsvarande stopp-funktioner för kontinuerlig igenkänning. Start funktionen startar och fortsätter att bearbeta alla yttranden tills du anropar funktionen Stop eller tills den är för lång tid i tystnaden. När du använder det kontinuerliga läget måste du registrera dig för de olika händelser som ska utlösas vid instansen. Till exempel utlöses händelsen "erkänd" när tal igenkänning sker. Du måste ha en händelse hanterare på plats för att kunna hantera igenkänning. En gräns på 10 minuter av den totala tal igenkännings tiden per session tillämpas av tal tjänsten.

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
await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
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

Ytterligare språk finns i [referens dokument för tal-SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Diktering

När du använder kontinuerlig igenkänning kan du aktivera dikterings bearbetning genom att använda motsvarande "Aktivera diktering"-funktion. Det här läget kommer att göra att tal konfigurations instansen tolkar ord beskrivningar av menings strukturer som interpunktion. Till exempel skulle uttryck "är du bor i stadens frågetecken" tolkas som texten "är du bor i staden?".

::: zone pivot="programming-language-csharp"

Mer information om hur du använder funktionen `EnableDictation` finns i [.net Speech SDK-dokument](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Mer information om hur du använder funktionen `EnableDictation` finns i [ C++ tal SDK-dokumenten](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Mer information om hur du använder funktionen `enableDictation` finns i [Java Speech SDK-dokument](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Mer information om hur du använder `enable_dictation`-funktionen finns i [python Speech SDK-dokument](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Ytterligare språk finns i [referens dokument för tal-SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska ytterligare exempel på tal-SDK på GitHub](https://aka.ms/csspeech/samples)
