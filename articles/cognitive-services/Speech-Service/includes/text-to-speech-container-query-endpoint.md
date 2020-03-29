---
title: Frågeslutpunkt för behållare för text till tal
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73491143"
---
Behållaren tillhandahåller [REST-baserade endpoint API:er](../rest-text-to-speech.md). Det finns många [exempel på källkodsprojekt](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) för plattforms-, ram- och språkvariationer.

Med standardbehållaren *för text till tal* bör du förlita dig på språken och rösten för den bildtagg du hämtade. Om du till exempel `latest` hämtade taggen är `en-US` standardspråket och rösten. `JessaRUS` Argumentet `{VOICE_NAME}` skulle då [`en-US-JessaRUS`](../language-support.md#standard-voices)vara . Se exemplet SSML nedan:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Men för *anpassad text-till-tal* måste du hämta **Voice / modellen** från den anpassade [röstportalen](https://aka.ms/custom-voice-portal). Det anpassade modellnamnet är synonymt med röstnamnet. Navigera till **sidan Utbildning** och kopiera **voice/-modellen** som ska användas som `{VOICE_NAME}` argument.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Anpassad röstmodell - röstnamn":::

Se exemplet SSML nedan:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Låt oss konstruera en HTTP POST-begäran, med några rubriker och en datanyttolast. Ersätt `{VOICE_NAME}` platshållaren med ditt eget värde.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Det här kommandot:

* Konstruerar en HTTP `speech/synthesize/cognitiveservices/v1` POST-begäran för slutpunkten.
* Anger ett `Accept` huvud på`audio/*`
* Anger ett `Content-Type` huvud `application/ssml+xml`i , mer information, se [begärandetext](../rest-text-to-speech.md#request-body).
* Anger ett `X-Microsoft-OutputFormat` huvud `riff-16khz-16bit-mono-pcm`i , för fler alternativ se [ljudutdata](../rest-text-to-speech.md#audio-outputs).
* Skickar [SSML-begäran (Speech Synthesis Markup Language)](../speech-synthesis-markup.md) som ges `{VOICE_NAME}` till slutpunkten.