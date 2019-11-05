---
title: Installera tal behållare
titleSuffix: Azure Cognitive Services
description: Mer information finns i tal parasollet Helm Chart Configuration Options.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523979"
---
### <a name="speech-umbrella-chart"></a>Tal (paraply diagram)

Värdena i den översta nivån "parasoll"-diagrammet åsidosätter motsvarande värden i det underordnade diagrammet. Därför bör alla lokala anpassade värden läggas till här.

|Parameter|Beskrivning|Standard|
| -- | -- | -- | -- |
| `speechToText.enabled` | Om tjänsten för **tal till text** är aktive rad. | `true` |
| `speechToText.verification.enabled` | Om `helm test` funktionen för **tal-till-text** -tjänsten är aktive rad. | `true` |
| `speechToText.verification.image.registry` | Den Docker-avbildnings lagrings plats som `helm test` använder för att testa **tal-till-text-** tjänsten. Helm skapar separata Pod i klustret för att testa och hämtar *test-use-* avbildningen från det här registret. | `docker.io` |
| `speechToText.verification.image.repository` | Den Docker-avbildnings lagrings plats som `helm test` använder för att testa **tal-till-text-** tjänsten. Helm test Pod använder den här lagrings platsen för att hämta *test-use-* avbildningen. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Den Docker-avbildnings tag som används med `helm test` för **tal-till-text-** tjänsten. Helm test Pod använder den här taggen för att hämta *test-use-* avbildningen. | `latest` |
| `speechToText.verification.image.pullByHash` | Om *test-use-* avbildningen ska hämtas via hash. Om `true`ska `speechToText.verification.image.hash` läggas till, med ett giltigt hash-värde för avbildning. | `false` |
| `speechToText.verification.image.arguments` | De argument som används för att köra Docker-avbildningen för *test-use* . Helm test Pod skickar dessa argument till behållaren när `helm test`körs. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Om **text till tal** -tjänsten är aktive rad. | `true` |
| `textToSpeech.verification.enabled` | Om `helm test` funktionen för **tal-till-text** -tjänsten är aktive rad. | `true` |
| `textToSpeech.verification.image.registry` | Den Docker-avbildnings lagrings plats som `helm test` använder för att testa **tal-till-text-** tjänsten. Helm skapar separata Pod i klustret för att testa och hämtar *test-use-* avbildningen från det här registret. | `docker.io` |
| `textToSpeech.verification.image.repository` | Den Docker-avbildnings lagrings plats som `helm test` använder för att testa **tal-till-text-** tjänsten. Helm test Pod använder den här lagrings platsen för att hämta *test-use-* avbildningen. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Den Docker-avbildnings tag som används med `helm test` för **tal-till-text-** tjänsten. Helm test Pod använder den här taggen för att hämta *test-use-* avbildningen. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Om *test-use-* avbildningen ska hämtas via hash. Om `true`ska `textToSpeech.verification.image.hash` läggas till, med ett giltigt hash-värde för avbildning. | `false` |
| `textToSpeech.verification.image.arguments` | Argumenten som ska köras med *Hjälp* av Docker-avbildningen. Helm test-Pod skickar dessa argument till container när `helm test`körs. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |