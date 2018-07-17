---
title: Exempel för taligenkänning | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Här är ett exempel för taligenkänning.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 4cf3bbfa24e102c544b0e3215a20b73d323f15df
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070968"
---
# <a name="sample-for-intent-recognition"></a>Exempel för taligenkänning

Skaffa en prenumerationsnyckel först. Till skillnad från andra tjänster som stöds av Cognitive Service tal SDK avsikt igenkänning av tjänsterna kräver en viss prenumeration-nyckel. [Här](https://www.luis.ai) du hittar mer information om teknik för taligenkänning, samt information om hur du skaffa en prenumerationsnyckel. Ersätt dina egna prenumerationsnyckel för Language Understanding den [regionen för din prenumeration](regions.md), och AppId för din avsikt modell i lämpliga platser i exemplen.

## <a name="top-level-declarations"></a>Översta deklarationer

I följande översta deklarationerna bör finnas på plats för alla exemplen nedan:

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>Taligenkänning med mikrofon

Kodfragmentet nedan visar hur avsiktsigenkänning från mikrofonen indata på standardspråket (`en-US`).

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Taligenkänning med mikrofon i ett angivet språk

Kodfragmentet nedan visar hur avsiktsigenkänning från mikrofonen indata i ett angivet språk i det här fallet på tyska (`de-de`).

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>Taligenkänning från en fil med hjälp av händelser

Kodfragmentet visar hur avsiktsigenkänning på standardspråket (`en-US`) på en kontinuerlig sätt. Den här koden ger tillgång till ytterligare information, t.ex mellanresultat. Indata hämtas från från en ljudfil, format är en kanal (mono) WAV / PCM med en samplingsfrekvensen 16 kHz.

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

- [Taligenkänning](./speech-to-text-sample.md)

- [Översättning](./translation.md)
