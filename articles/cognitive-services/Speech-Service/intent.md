---
title: Exempel för Avsiktshantering Recognition | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Här är ett exempel på avsiktshantering redovisning.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1c9c1e2d54ccb200ef009be3566f6da9ced01175
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111175"
---
# <a name="sample-for-intent-recognition"></a>Exempel på avsiktshantering redovisning

> [!NOTE]
> Anvisningar att hämta det här exemplet och andra finns [prover för tal SDK](samples.md).

> [!NOTE]
> Skaffa en prenumeration för först. Till skillnad från andra tjänster som stöds av kognitiva Service tal SDK avsikt Recognition tjänster kräver en viss prenumeration nyckel. [Här](https://www.luis.ai) hittar du mer information om avsiktshantering recognition tekniken, samt information om hur du skaffar en prenumeration nyckel. Ersätt din egen prenumeration nyckel i [regionen för din prenumeration](regions.md), och AppId av avsiktshantering modellen på lämpliga platser i exemplen.

> [!NOTE]
> Följande översta förklaringar bör vara på plats för alla prover nedan:
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>Avsiktshantering bokföring med hjälp av mikrofon

Kodfragmentet nedan visar hur du identifierar avsikt från mikrofon indata på standardspråket (`en-US`).

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Avsiktshantering bokföring med hjälp av mikrofon angivet språk

Kodfragmentet nedan visar hur du identifierar avsikt från mikrofon indata i ett angivet språk i det här fallet på tyska (`de-de`).

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>Avsiktshantering recognition från en fil

Följande kodavsnitt identifierar avsikt från en ljudfil på standardspråket (`en-US`), formatet som stöds är en kanal (mono) WAV / PCM med en samplingsfrekvensen 16 kHz.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>Avsiktshantering bokföring med hjälp av händelser

Kodfragmentet visar hur att identifiera syftet i ett kontinuerligt sätt. Den här koden ger tillgång till ytterligare information, t.ex. mellanresultat. 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Exempelkod för källa

Senaste uppsättning exempel, finns det [kognitiva Services tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Nästa steg

- [Taligenkänning](./speech-to-text-sample.md)

- [Översättning](./translation.md)
