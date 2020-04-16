---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421978"
---
Om du vill strömma i komprimerat ljudformat `SPXPullAudioInputStream` `SPXPushAudioInputStream`till taltjänsten skapar du en eller .

Följande kodavsnitt visar hur du `SPXAudioConfiguration` skapar en `SPXPushAudioInputStream`från en förekomst av en , och anger en MP3 som komprimeringsformat för strömmen.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Nästa utdrag visar hur komprimerade ljuddata kan läsas från en `SPXPushAudioInputStream`fil och pumpas in i .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
