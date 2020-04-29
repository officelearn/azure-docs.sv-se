---
title: Slut punkt för frågans text till tal-behållare
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81275585"
---
Behållaren tillhandahåller [rest-baserade slut punkts-API: er](../rest-text-to-speech.md). Det finns många [exempel på käll kods projekt](https://github.com/Azure-Samples/Cognitive-Speech-TTS) för plattforms-, ramverk-och språkvarianter som är tillgängliga.

Med *standard text till tal* -behållaren bör du förlita dig på språket och rösten för den avbildnings tagg som du laddade ned. Om du till exempel har hämtat `latest` taggen är `en-US` standard språket och `JessaRUS` rösten. `{VOICE_NAME}` Argumentet skulle då vara [`en-US-JessaRUS`](../language-support.md#standard-voices). Se exempel SSML nedan:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

För *anpassad text till tal* måste du dock Hämta **rösten/modellen** från den [anpassade röst portalen](https://aka.ms/custom-voice-portal). Namnet på den anpassade modellen är synonymt med röst namnet. Gå till sidan **utbildning** och kopiera den **röst/modell** som ska användas som `{VOICE_NAME}` argument.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Anpassad röst modell – röst namn":::

Se exempel SSML nedan:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Nu ska vi skapa en HTTP POST-begäran, vilket ger några rubriker och en data nytto Last. Ersätt `{VOICE_NAME}` plats hållaren med ditt eget värde.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Det här kommandot:

* Skapar en HTTP POST-begäran för `speech/synthesize/cognitiveservices/v1` slut punkten.
* Anger ett `Accept` sidhuvud för`audio/*`
* Anger ett `Content-Type` huvud för `application/ssml+xml`mer information finns i brödtext för [begäran](../rest-text-to-speech.md#request-body).
* Anger ett `X-Microsoft-OutputFormat` sidhuvud för `riff-16khz-16bit-mono-pcm`fler alternativ se [ljud uppspelning](../rest-text-to-speech.md#audio-outputs).
* Skickar [SSML-begäran (Speech syntes Markup Language)](../speech-synthesis-markup.md) `{VOICE_NAME}` till slut punkten.