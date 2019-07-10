---
title: Installera tal behållare
titleSuffix: Azure Cognitive Services
description: Information om konfigurationsalternativen för tal samlingsnamnet helm-diagram.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711817"
---
### <a name="speech-umbrella-chart"></a>Tal (samlingsnamnet diagram)

Värden i diagrammet översta ”samlingsnamnet” åsidosätter värdena som motsvarande underordnade diagrammet. Därför alla lokala anpassade värden ska läggas till här.

|Parameter|Beskrivning|Standard|
| -- | -- | -- | -- |
| `speechToText.enabled` | Om den **tal till text** -tjänsten är aktiverad. | `true` |
| `speechToText.verification.enabled` | Om den `helm test` funktion för **tal till text** -tjänsten är aktiverad. | `true` |
| `speechToText.verification.image.registry` | Lagringsplatsen för docker-avbildningen som `helm test` använder för att testa **tal till text** service. Helm skapar separata pod i klustret för att testa och hämtar den *test-användning* avbildningen från registret. | `docker.io` |
| `speechToText.verification.image.repository` | Lagringsplatsen för docker-avbildningen som `helm test` använder för att testa **tal till text** service. Helm test pod använder den här lagringsplatsen för att hämta *test-användning* bild. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Docker-bildtagg som används med `helm test` för **tal till text** service. Helm test pod använder den här taggen för att hämta *test-användning* bild. | `latest` |
| `speechToText.verification.image.pullByHash` | Om den *test-användning* docker-avbildningen hämtas-hash. Om `true`, `speechToText.verification.image.hash` ska läggas till, med giltig bild hash-värde. | `false` |
| `speechToText.verification.image.arguments` | Argument som används för att köra den *test-användning* docker-avbildning. Helm test pod skickar de här argumenten till behållaren när du kör `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Om den **text till tal** -tjänsten är aktiverad. | `true` |
| `textToSpeech.verification.enabled` | Om den `helm test` funktion för **tal till text** -tjänsten är aktiverad. | `true` |
| `textToSpeech.verification.image.registry` | Lagringsplatsen för docker-avbildningen som `helm test` använder för att testa **tal till text** service. Helm skapar separata pod i klustret för att testa och hämtar den *test-användning* avbildningen från registret. | `docker.io` |
| `textToSpeech.verification.image.repository` | Lagringsplatsen för docker-avbildningen som `helm test` använder för att testa **tal till text** service. Helm test pod använder den här lagringsplatsen för att hämta *test-användning* bild. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Docker-bildtagg som används med `helm test` för **tal till text** service. Helm test pod använder den här taggen för att hämta *test-användning* bild. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Om den *test-användning* docker-avbildningen hämtas-hash. Om `true`, `textToSpeech.verification.image.hash` ska läggas till, med giltig bild hash-värde. | `false` |
| `textToSpeech.verification.image.arguments` | Argumenten för att köra med den *test-användning* docker-avbildning. Helm test pod skickar de här argumenten till behållaren när du kör `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |