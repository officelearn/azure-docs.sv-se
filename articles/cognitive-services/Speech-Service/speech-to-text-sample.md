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
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 9e0b82cc9a034f1e25ef0cc656e3770a166a6ad7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355662"
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

- [Avsiktshantering igenkänning](./intent.md)

- [Översättning](./translation.md)
