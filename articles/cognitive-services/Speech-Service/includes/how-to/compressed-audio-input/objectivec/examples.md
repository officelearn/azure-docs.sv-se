---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409668"
---
Om du vill strömma i komprimerat ljudformat `SPXPullAudioInputStream` `SPXPushAudioInputStream`till taltjänsten skapar du en eller .

Följande kodavsnitt visar hur du `SPXAudioConfiguration` skapar en `SPXPushAudioInputStream`från en förekomst av en , och anger en MP3 som komprimeringsformat för strömmen.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Nästa utdrag visar hur komprimerade ljuddata kan läsas från en `SPXPushAudioInputStream`fil och pumpas in i .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
