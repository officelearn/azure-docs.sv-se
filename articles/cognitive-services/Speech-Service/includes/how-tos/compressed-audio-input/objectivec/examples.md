---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943747"
---
Om du vill strömma i ett komprimerat ljud format till tal tjänsten skapar du en `SPXPullAudioInputStream` eller `SPXPushAudioInputStream`.

Följande fragment visar hur du skapar en `SPXAudioConfiguration` från en instans av en `SPXPushAudioInputStream`och hur du anger en MP3-fil som komprimerings formatet för data strömmen.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Nästa kodfragment visar hur komprimerade ljud data kan läsas från en fil och pumpas till `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
