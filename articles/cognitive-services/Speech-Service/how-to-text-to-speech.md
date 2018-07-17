---
title: Använd Text till tal med taltjänster | Microsoft Docs
description: Lär dig hur du använder använda Text till tal i Speech-tjänsten.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 5eb0839cb973ba2f8bc56dc240fc60817b24b3b6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068870"
---
# <a name="use-text-to-speech-in-speech-service"></a>Använd ”Text till tal” i Speech service

Med taltjänsten innehåller Text till tal-funktioner via en enkel HTTP-begäran. Du PUBLICERAR texten som ska läsas till lämplig slutpunkt och tjänsten returnerar en ljudfil (`.wav`) som innehåller syntetiskt tal. Programmet kan sedan använda den här ljud som den gillar.

Begäran om programvaruprinciptext för INLÄGGET Text till tal kan vara oformaterad text (ASCII eller UTF8) eller ett [SSML](speech-synthesis-markup.md) dokumentet. Oformaterad text begäranden läses med en standard-röst. I de flesta fall som du vill använda en SSML brödtext. HTTP-begäran måste innehålla en [auktorisering](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#authentication) token. 

Regionala Text till tal-slutpunkter visas här. Använd en lämplig till din prenumeration.

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Ange en röst

Om du vill ange en röst, använda den `<voice>` [SSML](speech-synthesis-markup.md) tagg. Exempel:

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Se [Text till tal röster](supported-languages.md#text-to-speech) en lista över tillgängliga röster och deras namn.

## <a name="make-a-request"></a>Gör en begäran

En Text till tal HTTP-begäran skickas i POST-läge med texten som ska talas i brödtexten i begäran. Den maximala längden på HTTP-begärandetexten är 1024 tecken. Begäran måste ha följande huvuden: 

Sidhuvud|Värden|Kommentarer
-|-|-
|`Content-Type` | `application/ssml+xml` | Indata-text-format.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Ljudformatet utdata.
|`User-Agent`   |Programnamn | Programnamnet är obligatoriskt och måste vara färre än 255 tecken.
| `Authorization`   | Auktoriseringstoken som erhålls genom att presentera din prenumerationsnyckel till token-tjänsten. Varje token är giltig i tio minuter. Se [REST API: er: autentisering](rest-apis.md#authentication).

> [!NOTE]
> Om din valda röst- och utdataformat har olika bithastigheter, samplas ljudet efter behov. 24khz röster stöder inte `audio-16khz-16kbps-mono-siren` och `riff-16khz-16kbps-mono-siren` utdataformat. 

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
413|Begäran om entiteten är för stor|SSML-indata är längre än 1024 tecken.
|502|Felaktig gateway    | Problem med nätverket eller servern. Kan också vara ogiltiga sidhuvuden.

Mer information om Text till tal REST-API finns i [REST API: er](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Känna igen tal i C++](quickstart-cpp-windows.md)
- [Känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
- [Känna igen tal i Java](quickstart-java-android.md)
