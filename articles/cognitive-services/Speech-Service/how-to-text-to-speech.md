---
title: Använd Text till tal med tal services | Microsoft Docs
description: Lär dig använda använda Text till tal i tjänsten tal.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 1ed2ee73b32f71d2e1ca34c6de9d1cb2649d7f0c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355644"
---
# <a name="use-text-to-speech-in-speech-service"></a>Använd ”Text till tal” i tal service

Tjänsten tal innehåller Text till tal-funktion via en enkel HTTP-begäran. Du efter texten som ska läsas till lämplig slutpunkten och tjänsten returnerar en ljudfil (`.wav`) som innehåller syntetiskt tal. Programmet kan sedan använda den här ljud som den gillar.

Begäran om programvaruprinciptext för efter Text till tal kan vara oformaterad text (ASCII- eller UTF8) eller ett [SSML](speech-synthesis-markup.md) dokumentet. Oformaterad text begäranden talas med en standard-röst. I de flesta fall som du vill använda en SSML-brödtext. HTTP-begäran måste innehålla en autentiseringstoken. 

Regional Text till tal-slutpunkter som visas här. Använd en lämplig till din prenumeration.

Region| Slutpunkt
-|-
Västra USA| `https://westus.tts.speech.microsoft.com/cognitiveservices/v1`
Östasien| `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1`
Norra Europa| `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1`

> [!NOTE]
> Om du har skapat en anpassad röst teckensnitt, kan du använda den slutpunkt som du har skapat för i stället för som ovan.

## <a name="specify-a-voice"></a>Ange en röst

Om du vill ange en röst använder den `<voice>` [SSML](speech-synthesis-markup.md) tagg. Exempel:

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Se [Text till tal-röster](supported-languages.md#text-to-speech) en lista över tillgängliga röster och deras namn.

## <a name="make-a-request"></a>Gör en förfrågan

En Text till tal HTTP-begäran görs i POST-läge med texten som ska talas i brödtexten i begäran. Den maximala längden på texten på HTTP-begäran är 1024 tecken. Förfrågan måste ha följande huvuden: 

Sidhuvud|Värden|Kommentarer
-|-|-
|`Content-Type` | `application/ssml+xml` | Indata-format.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Ljudformatet utdata.
|`User-Agent`   |Programnamn | Programnamn krävs och måste vara färre än 255 tecken.
| `Authorization`   | Autentiseringstoken som erhålls genom att presentera din prenumeration nyckel till tjänsten token. Varje token är giltig i tio minuter. Se [REST API: er: autentisering](rest-apis.md#authentication).

> [!NOTE]
> Om din rösten och utdataformat har olika bithastigheter, samplas ljuduppspelningen efter behov. 24khz röster stöder inte `audio-16khz-16kbps-mono-siren` och `riff-16khz-16kbps-mono-siren` utdataformat. 

Ett exempel på begäran visas nedan.

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

Svarstexten med status 200 innehåller ljud i angivet utdataformat.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Om ett fel inträffar, används statuskoder nedan. Svarstexten efter fel innehåller också en beskrivning av problemet.

|Kod|Beskrivning|Problem|
|-|-|-|
400 |Felaktig förfrågan |En obligatorisk parameter är tom, null eller saknas. Eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. Ett vanligt problem som är en rubrik som är för lång.
401|Behörighet saknas |Begäran är inte auktoriserad. Kontrollera att din prenumeration nyckel eller token är giltig.
413|Om Begärandeenheten är för stor|SSML-indata är längre än 1024 tecken.
|502|Felaktig gateway    | Problem med nätverket eller servern. Kan också indikera ogiltig huvuden.

Mer information om Text till tal REST-API finns [REST API: er](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Nästa steg

- [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
- [Identifiera tal i C#](quickstart-csharp-windows.md)
