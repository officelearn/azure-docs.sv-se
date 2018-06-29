---
title: Exempel för tal-till-Text | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Här är ett exempel för tal till text.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030258"
---
# <a name="sample-for-speech-to-text"></a>Exempel för tal till Text

> [!NOTE]
> Anvisningar att hämta det här exemplet och andra finns [prover för tal SDK](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> För alla exemplen nedan, ska följande översta förklaringar finnas:
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>Taligenkänning med mikrofonens

Kodfragmentet nedan visar hur du identifierar talindata från mikrofon på standardspråket (`en-US`).

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>Taligenkänning från en fil

Följande kodavsnitt identifierar talindata från en ljudfil på standardspråket (`en-US`), formatet som stöds är en kanal (mono) WAV / PCM med en samplingsfrekvensen 16 kHz.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>Taligenkänning med hjälp av en anpassad mall

Den [anpassade tal Service (CRI)](https://www.cris.ai/) tillåter anpassning av Microsofts till text till tal-motorn för ditt program. Kodfragmentet nedan visar hur du identifierar tal från en mikrofon med din CRI-modellen. Fyll i din prenumeration CRI-nyckel och din egen distribution identifiering innan den körs.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>Kontinuerlig taligenkänning

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Exempelkod för källa

Den senaste versionen av prover och mer avancerade exempel finns i ett dedikerat [GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>Nästa steg

- [Avsiktsigenkänning](./intent.md)

- [Översättning](./translation.md)
