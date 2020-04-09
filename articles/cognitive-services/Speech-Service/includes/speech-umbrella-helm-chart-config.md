---
title: Installera talbehållare
titleSuffix: Azure Cognitive Services
description: Information om konfigurationsalternativen för talparaplyrdiagram.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874369"
---
### <a name="speech-umbrella-chart"></a>Tal (paraplydiagram)

Värden i det översta "paraplydiagrammet" åsidosätter motsvarande underdiagramvärden. Därför bör alla lokala anpassade värden läggas till här.

|Parameter|Beskrivning|Default|
| -- | -- | -- | -- |
| `speechToText.enabled` | Om **tal-till-text-tjänsten** är aktiverad. | `true` |
| `speechToText.verification.enabled` | Om `helm test` funktionen för **tal-till-text-tjänst** är aktiverad. | `true` |
| `speechToText.verification.image.registry` | Den dockeravbildningsdatabas som `helm test` används för att testa **tal-till-text-tjänsten.** Helm skapar separat pod inuti klustret *test-use* för testning och hämtar testanvändningsavbildningen från det här registret. | `docker.io` |
| `speechToText.verification.image.repository` | Den dockeravbildningsdatabas som `helm test` används för att testa **tal-till-text-tjänsten.** Helm test pod använder denna databas för att dra *test-användning* bild. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Docker-avbildningstaggen som används för `helm test` **tal-till-text-tjänst.** Helm test pod använder denna tagg för att dra *test-användning* bild. | `latest` |
| `speechToText.verification.image.pullByHash` | Om *dockerbilden för testanvändning* dras av hash. Om `true` `speechToText.verification.image.hash` , ska läggas till, med giltigt bildhh-värde. | `false` |
| `speechToText.verification.image.arguments` | De argument som används för att köra *dockeravbildningen för testanvändning.* Helm test pod skickar dessa argument `helm test`till behållaren när du kör . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Om **text-till-tal-tjänsten** är aktiverad. | `true` |
| `textToSpeech.verification.enabled` | Om `helm test` funktionen för **tal-till-text-tjänst** är aktiverad. | `true` |
| `textToSpeech.verification.image.registry` | Den dockeravbildningsdatabas som `helm test` används för att testa **tal-till-text-tjänsten.** Helm skapar separat pod inuti klustret *test-use* för testning och hämtar testanvändningsavbildningen från det här registret. | `docker.io` |
| `textToSpeech.verification.image.repository` | Den dockeravbildningsdatabas som `helm test` används för att testa **tal-till-text-tjänsten.** Helm test pod använder denna databas för att dra *test-användning* bild. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Docker-avbildningstaggen som används för `helm test` **tal-till-text-tjänst.** Helm test pod använder denna tagg för att dra *test-användning* bild. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Om *dockerbilden för testanvändning* dras av hash. Om `true` `textToSpeech.verification.image.hash` , ska läggas till, med giltigt bildhh-värde. | `false` |
| `textToSpeech.verification.image.arguments` | Argumenten som ska köras med *dockeravbildningen för testanvändning.* Helm-testenheten skickar dessa argument `helm test`till behållare när du kör . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |