---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421978"
---
Om du vill strömma i ett komprimerat ljud format till tal-tjänsten `SPXPullAudioInputStream` skapar `SPXPushAudioInputStream`du en eller.

Följande fragment visar hur du skapar en `SPXAudioConfiguration` från en instans av en `SPXPushAudioInputStream`och hur du anger en MP3-fil som komprimerings formatet för data strömmen.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Nästa kodfragment visar hur komprimerade ljud data kan läsas från en fil och pumpas till `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
