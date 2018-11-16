---
title: Använda Text till tal i Speech-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder Text till tal i Speech-tjänsten.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: erhopf
ms.openlocfilehash: 16b7de909ce57a7f8d3cea54a59996dc6f0a6ba0
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712015"
---
# <a name="use-text-to-speech-in-speech-service"></a>Använd ”Text till tal” i Speech Service

Med taltjänsten innehåller Text till tal-funktioner via en enkel HTTP-begäran. Du `POST` text som ska talas till lämplig slutpunkt och tjänsten returnerar en ljudfil (`.wav`) som innehåller syntetiskt tal. Programmet kan sedan använda den här ljud som den gillar.

Begäran om programvaruprinciptext för INLÄGGET Text till tal kan vara oformaterad text (ASCII eller UTF8) eller ett [SSML](speech-synthesis-markup.md) dokumentet. Oformaterad text begäranden läses med en standard-röst. I de flesta fall som du vill använda en SSML brödtext. HTTP-begäran måste innehålla en [auktorisering](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#authentication) token.

Regionala Text till tal-slutpunkter visas här. Använd en lämplig till din prenumeration.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Ange en röst

Om du vill ange en röst, använda den `<voice>` [SSML](speech-synthesis-markup.md) tagg. Exempel:

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Se [Text till tal röster](language-support.md#text-to-speech) en lista över tillgängliga röster och deras namn.

## <a name="make-a-request"></a>Skapa en begäran

En Text till tal HTTP-begäran skickas i POST-läge med texten som ska talas i brödtexten i begäran. Den maximala längden på HTTP-begärandetexten är 1024 tecken. Begäran måste ha följande huvuden:

Huvud|Värden|Kommentarer
-|-|-
|`Content-Type` | `application/ssml+xml` | Indata-text-format.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`riff-16khz-16bit-mono-pcm`<br>`raw-8khz-8bit-mono-mulaw`<br>`riff-8khz-8bit-mono-mulaw`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Ljudformatet utdata.
|`User-Agent`   |Programnamn | Programnamnet är obligatoriskt och måste vara färre än 255 tecken.
| `Authorization`   | Auktoriseringstoken som erhålls genom att presentera din prenumerationsnyckel till token-tjänsten. Varje token är giltig i tio minuter. Se [REST API: er: autentisering](rest-apis.md#authentication).

> [!NOTE]
> Om din valda röst- och utdataformat har olika bithastigheter, samplas ljudet efter behov.

En exempelförfrågan visas nedan.

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

Svarstexten med statusen 200 innehåller ljud i angivet utdataformat.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Om ett fel inträffar, används statuskoder nedan. Svarstexten för felet innehåller också en beskrivning av problemet.

|Kod|Beskrivning|Problem|
|-|-|-|
400 |Felaktig begäran |En obligatorisk parameter är tom, null eller saknas. Eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. Ett vanligt problem är en rubrik som är för lång.
401|Behörighet saknas |Begäran har inte behörighet. Kontrollera att din prenumerationsnyckel eller token är giltig.
413|Begäran om entiteten är för stor|Inkommande SSML är för stor eller innehåller fler än 3 `<voice>` element.
429|För många begäranden|Du har överskridit kvoten eller antalet begäranden som tillåts för din prenumeration.
|502|Felaktig gateway    | Problem med nätverket eller servern. Kan också vara ogiltiga sidhuvuden.

Mer information om Text till tal REST-API finns i [REST API: er](rest-apis.md#text-to-speech-api).

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Känna igen tal i C++](quickstart-cpp-windows.md)
- [Identifiera tal i C#](quickstart-csharp-dotnet-windows.md)
- [Känna igen tal i Java](quickstart-java-android.md)
