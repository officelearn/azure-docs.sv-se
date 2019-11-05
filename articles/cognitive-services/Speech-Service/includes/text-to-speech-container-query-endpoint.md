---
title: Slut punkt för frågans text till tal-behållare
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491143"
---
Behållaren tillhandahåller [rest-baserade slut punkts-API: er](../rest-text-to-speech.md). Det finns många [exempel på käll kods projekt](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) för plattforms-, ramverk-och språkvarianter som är tillgängliga.

Med *standard text till tal* -behållaren bör du förlita dig på språket och rösten för den avbildnings tagg som du laddade ned. Om du till exempel har hämtat `latest`-taggen är standard språket `en-US` och `JessaRUS` rösten. Argumentet `{VOICE_NAME}` skulle då [`en-US-JessaRUS`](../language-support.md#standard-voices). Se exempel SSML nedan:

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
* Anger ett `Accept` huvud för `audio/*`
* Anger ett `Content-Type` sidhuvud för `application/ssml+xml`. mer information finns i [brödtext för begäran](../rest-text-to-speech.md#request-body).
* Anger ett `X-Microsoft-OutputFormat` rubrik för `riff-16khz-16bit-mono-pcm`, för fler alternativ, se [ljud uppspelning](../rest-text-to-speech.md#audio-outputs).
* Skickar [SSML-begäran (Speech syntes Markup Language)](../speech-synthesis-markup.md) till den `{VOICE_NAME}` till slut punkten.