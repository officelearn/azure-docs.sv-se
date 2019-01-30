---
title: Text till tal-API för Microsoft Speech-tjänsten | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använda text till tal-API för att tillhandahålla i realtid text till tal-konvertering på en mängd olika röster och språk
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ms.openlocfilehash: 61bd1879a4b1bf8281ac03c8254fb3d48c07a139
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215868"
---
# <a name="bing-text-to-speech-api"></a>Bing text till tal-API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Introduktion

Med den Bing text till tal-API, kan programmet skicka HTTP-begäranden till en server för molnet, där texten är omedelbart syntetiskt till mänskliga standardrösttyper tal och returneras som en ljudfil. Detta API kan användas i många olika kontexter för att tillhandahålla i realtid text till tal-konvertering i en mängd olika och språk.

## <a name="VoiceSynReq"></a>Röst syntes begäran

### <a name="Subscription"></a>Autentiseringstoken

Varje röst syntes begäran kräver en åtkomsttoken för JSON Web Token (JWT). JWT-åtkomsttoken skickas i rubriken tal. Token har en förfallotid på 10 minuter. Läs om hur prenumerera och för att hämta API-nycklar som används för att hämta giltiga JWT-åtkomsttoken [Cognitive Services-prenumeration](https://azure.microsoft.com/try/cognitive-services/).

API-nyckel skickas till tjänsten token. Exempel:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Obligatorisk huvudinformationen för tokenåtkomst är som följer.

Name| Format | Beskrivning
----|----|----
OCP-Apim-Subscription-Key | ASCII | Din prenumerationsnyckel

Tokentjänsten som returnerar JWT-åtkomsttoken som `text/plain`. Sedan JWT skickas som en `Base64 access_token` till tal-slutpunkt som ingen auktoriseringsrubrik föregås av strängen `Bearer`. Exempel:

`Authorization: Bearer [Base64 access_token]`

Klienter måste använda följande slutpunkt för att komma åt tjänsten text till tal:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Tills du har köpt en åtkomst-token med din prenumerationsnyckel som tidigare beskrivits kan den här länken genererar en `403 Forbidden` svarsfel.

### <a name="Http"></a>HTTP-huvuden

I följande tabell visas de HTTP-huvuden som används för syntes röstförfrågningar.

Sidhuvud |Value |Kommentarer
----|----|----
Innehållstyp | program/ssml + xml | Inkommande innehållstyp.
X-Microsoft-OutputFormat | **1.** ssml-16 khz-16-bitars-mono-text till tal <br> **2.** raw-16 khz-16-bitars-mono-pcm <br>**3.** ljud-16 khz – 16 kbit/s-mono-siren <br> **4.** riff-16 khz – 16 kbit/s-mono-siren <br> **5.** riff-16 khz-16-bitars-mono-pcm <br> **6.** ljud-16 khz-128kbitrate-mono-mp3 <br> **7.** ljud-16 khz-64kbitrate-mono-mp3 <br> **8.** ljud-16 khz-32kbitrate-mono-mp3 | Ljudformatet utdata.
X-Search-AppId | En GUID (hex endast, inga streck) | Ett ID som unikt identifierar klientprogrammet. Detta kan vara lagrings-ID för appar. Om det inte finns, kan ID: T vara användargenererade för ett program.
X-Search-ClientID | En GUID (hex endast, inga streck) | Ett ID som unikt identifierar en programinstans för varje installation.
Användaragent | Programnamn | Programnamnet är obligatoriskt och måste vara färre än 255 tecken.
Auktorisering | Autentiseringstoken |  Se den <a href="#Subscription">auktoriseringstoken</a> avsnittet.

### <a name="InputParam"></a>Indataparametrar

Begäranden till den Bing text till tal-API som görs med hjälp av HTTP POST-anrop. Rubrikerna som anges i föregående avsnitt. Texten innehåller tal syntes Markup Language (SSML) indata som representerar texten som ska syntetiseras. En beskrivning av koden som används för att styra aspekter av tal, till exempel språket och kön talaren finns i den [SSML W3C-specifikationen](http://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Den maximala storleken för SSML indata som stöds är 1 024 tecken, inklusive alla taggar.

###  <a name="SampleVoiceOR"></a>Exempel: röst utdatabegäran

Ett exempel på en begäran för röst-utdata är följande:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Röst utdata svar

Den Bing text till tal-API använder HTTP POST för att skicka ljud tillbaka till klienten. API-svaret innehåller ljudströmmen och codec-enheten och den matchar det begärda utdataformatet. Ljud som returneras för en viss begäran får inte överskrida 15 sekunder.

### <a name="SuccessfulRecResponse"></a>Exempel: lyckad syntes svar

Följande kod är ett exempel på ett JSON-svar på en lyckad röst syntes begäran. Kommentarer och formatering av koden är för det här exemplet bara och har utelämnats från faktiska svaret.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Exempel: syntes fel

Följande exempelkod visar ett JSON-svar på en röst-syntes frågefel:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Felsvar

Fel | Beskrivning
----|----
HTTP/400 Felaktig begäran | En obligatorisk parameter är saknas, tom eller null eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. En anledning för att hämta ”ogiltig” svaret passerar ett strängvärde som är längre än den tillåtna längden. En kort beskrivning av parametern problematiska ingår.
HTTP/401 Ej behörig | Begäran har inte behörighet.
HTTP/413 RequestEntityTooLarge  | SSML-indata är större än vad som stöds.
HTTP/502 BadGateway | Det finns ett nätverksrelaterade problem eller ett problem på serversidan.

Ett exempel på ett felsvar är följande:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Ändra röst utdata via SSML

SSML 1.0 har stöd för Microsoft text till tal-API som definierats i W3C [tal syntes Markup Language (SSML) Version 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). Det här avsnittet visas exempel på vissa egenskaper för genererade röst utdata som liknar talar Betygsätt uttal osv med hjälp av SSML taggar.

1. Att lägga till break

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. Engelsktalande förändringstakten

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. Uttal av

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. Ändra volym

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. Ändra försäljningsargument

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. Ändra prosody profil

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> Obs ljuddata måste vara 8 kB eller 16 k wav som anges i följande format: **CRC kod** (CRC-32): 4 byte (DWORD) med giltiga intervallet 0x00000000 ~ 0xFFFFFFFF; **Ljud format flaggan**: 4 byte (DWORD) med giltiga intervallet 0x00000000 ~ 0xFFFFFFFF; **Antal prov**: 4 byte (DWORD) med giltiga intervallet 0x00000000 ~ 0x7FFFFFFF; **Storleken på binär brödtext**: 4 byte (DWORD) med giltiga intervallet 0x00000000 ~ 0x7FFFFFFF; **Binära brödtext**: n byte.

## <a name="SampleApp"></a>Exempelprogram

Mer information om implementering finns i den [Visual C# .NET text till tal-exempelprogrammet](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Nationella inställningar som stöds och rösttyper

I följande tabell visas några av de nationella inställningar som stöds och relaterade rösttyper.

Nationella inställningar | Kön | Tjänsten Namnmappningen
---------|--------|------------
ar-EG* | Kvinna | ”Microsoft Server tal Text till tal-röst (ar-t.ex., Hoda)”
ar-SA | Man | ”Microsoft Server tal Text till tal-röst (ar-SA, Naayf)”
BG-BG | Man | ”Microsoft Server tal Text till tal röst (bg-BG, Ivan)”
CA-ES | Kvinna | ”Microsoft Server tal Text till tal röst (ca-ES, HerenaRUS)”
CS-CZ | Man | ”Microsoft Server tal Text till tal-röst (cs-CZ, Jakub)”
da-DK | Kvinna | ”Microsoft Server tal Text till tal-röst (da-DK, HelleRUS)”
Tyskland-AT | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-AT, Michael)”
Tyskland – CH | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-CH, Karsten)”
de-DE | Kvinna | ”Microsoft Server tal Text till tal-röst (de-DE, Hedda)”
de-DE | Kvinna | ”Microsoft Server tal Text till tal-röst (de-DE, HeddaRUS)”
de-DE | Man | ”Microsoft Server tal Text till tal-röst (de-DE, Stefan, Apollo)”
el GR | Man | ”Microsoft Server tal Text till tal-röst (el-GR, Stefanos)”
SV-Australien | Kvinna | ”Microsoft Server tal Text till tal-röst (en AU, Catherine)”
SV-Australien | Kvinna | ”Microsoft Server tal Text till tal-röst (en AU, HayleyRUS)”
en CA: N | Kvinna | ”Microsoft Server tal Text till tal-röst (en CA, Johan)”
en CA: N | Kvinna | ”Microsoft Server tal Text till tal-röst (en CA, HeatherRUS)”
en-GB | Kvinna | ”Microsoft Server tal Text till tal-röst (en-GB, Susan, Apollo)”
en-GB | Kvinna | ”Microsoft Server tal Text till tal-röst (en-GB, HazelRUS)”
en-GB | Man | ”Microsoft Server tal Text till tal-röst (en-GB, George, Apollo)”
en IE | Man | ”Microsoft Server tal Text till tal-röst (en IE, Stefan)”
en Indien | Kvinna | ”Microsoft Server tal Text till tal-röst (en-IN-, Heera, Apollo)”
en Indien | Kvinna | ”Microsoft Server tal Text till tal-röst (en-IN-, PriyaRUS)”
en Indien | Man | ”Microsoft Server tal Text till tal-röst (en-IN-, Ravi, Apollo)”
en-US | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, ZiraRUS)”
en-US | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, JessaRUS)”
en-US | Man | ”Microsoft Server tal Text till tal-röst (en-US, BenjaminRUS)”
es-ES | Kvinna | ”Microsoft Server tal Text till tal röst (es-ES, Lisa, Apollo)”
es-ES | Kvinna | ”Microsoft Server tal Text till tal röst (es-ES, HelenaRUS)”
es-ES | Man | ”Microsoft Server tal Text till tal röst (es-ES, Pablo, Apollo)”
es-MX | Kvinna | ”Microsoft Server tal Text till tal röst (es-MX, HildaRUS)”
es-MX | Man | ”Microsoft Server tal Text till tal röst (es-MX, Raul, Apollo)”
fi-FI | Kvinna | ”Microsoft Server tal Text till tal-röst (fi-FI, HeidiRUS)”
fr-CA | Kvinna | ”Microsoft Server tal Text till tal röst (fr-CA, Caroline)”
fr-CA | Kvinna | ”Microsoft Server tal Text till tal röst (fr-CA, HarmonieRUS)”
fr CH | Man | ”Microsoft Server tal Text till tal röst (fr-CH, Guillaume)”
fr-FR | Kvinna | ”Microsoft Server tal Text till tal röst (fr-FR, Julia, Apollo)”
fr-FR | Kvinna | ”Microsoft Server tal Text till tal röst (fr-FR, HortenseRUS)”
fr-FR | Man | ”Microsoft Server tal Text till tal röst (fr-FR, Paul, Apollo)”
han IL| Man| ”Microsoft Server tal Text till tal-röst (he IL-Asaf)”
Hej Indien | Kvinna | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Kalpana, Apollo)”
Hej Indien | Kvinna | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Kalpana)”
Hej Indien | Man | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Hemant)”
HR-HR | Man | ”Microsoft Server tal Text till tal röst (hr-HR, Matej)”
hu-HU | Man | ”Microsoft Server tal Text till tal-röst (hu-HU, Szabolcs)”
ID-ID | Man | ”Microsoft Server tal Text till tal-röst (id-ID, Andika)”
IT-IT | Man | ”Microsoft Server tal Text till tal-röst (it-IT, Cosimo, Apollo)”
IT-IT | Kvinna | ”Microsoft Server tal Text till tal-röst (it-IT, LuciaRUS)”
ja-JP | Kvinna | ”Microsoft Server tal Text till tal röst (ja-JP, Ayumi, Apollo)”
ja-JP | Man | ”Microsoft Server tal Text till tal röst (ja-JP, Ichiro, Apollo)”
ja-JP | Kvinna | ”Microsoft Server tal Text till tal röst (ja-JP, HarukaRUS)”
ko-KR | Kvinna | ”Microsoft Server tal Text till tal-röst (ko-KR, HeamiRUS)”
MS-Mina | Man | ”Microsoft Server tal Text till tal röst (ms Mina Rizwan)”
NB-NO | Kvinna | ”Microsoft Server tal Text till tal röst (nb-NO HuldaRUS)”
NL-NL | Kvinna | ”Microsoft Server tal Text till tal röst (nl-NL, HannaRUS)”
pl-PL | Kvinna | ”Microsoft Server tal Text till tal röst (pl-PL, PaulinaRUS)”
pt-BR | Kvinna | ”Microsoft Server tal Text till tal röst (pt-BR, HeloisaRUS)”
pt-BR | Man | ”Microsoft Server tal Text till tal röst (pt-BR, Daniel, Apollo)”
PT-PT | Kvinna | ”Microsoft Server tal Text till tal röst (pt-PT, HeliaRUS)”
RO-RO | Man | ”Microsoft Server tal Text till tal-röst (ro-RO, Andrei)”
ru-RU | Kvinna | ”Microsoft Server tal Text till tal röst (ru-RU, Irina, Apollo)”
ru-RU | Man | ”Microsoft Server tal Text till tal röst (ru-RU, Pavel, Apollo)”
ru-RU | Kvinna | ”Microsoft Server tal Text till tal röst (ru-RU, EkaterinaRUS)”
sk-SK | Man | ”Microsoft Server tal Text till tal röst (sk-SK, Filip)”
sl-SI | Man | ”Microsoft Server tal Text till tal röst (sl-SI, Lado)”
SV-SE | Kvinna | ”Microsoft Server tal Text till tal-röst (sv-SE, HedvigRUS)”
ta IN | Man | ”Microsoft Server tal Text till tal-röst (ta-IN-, Valluvar)”
TH-TH | Man | ”Microsoft Server tal Text till tal röst (th-TH, Pattara)”
TR-TR | Kvinna | ”Microsoft Server tal Text till tal röst (tr-TR, SedaRUS)”
Vi VN | Man | ”Microsoft Server tal Text till tal röst (vi VN ett)”
zh-CN | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-CN, HuihuiRUS)”
zh-CN | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-CN, Yaoyao, Apollo)”
zh-CN | Man | ”Microsoft Server tal Text till tal-röst (zh-CN, Kangkang, Apollo)”
zh-HK | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-HK Tracy, Apollo)”
zh-HK | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-HK TracyRUS)”
zh-HK | Man | ”Microsoft Server tal Text till tal-röst (zh-HK Danny, Apollo)”
zh-TW | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-TW, Yating, Apollo)”
zh-TW | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-TW, HanHanRUS)”
zh-TW | Man | ”Microsoft Server tal Text till tal-röst (zh-TW, Zhiwei, Apollo)”
 * ar-t.ex har stöd för Modern Standard arabiska (MSA).

> [!NOTE]
> Observera att tidigare tjänstnamn **Microsoft Server tal Text till tal-röst (cs-CZ, Vit)** och **Microsoft Server tal Text till tal-röst (en IE, Shaun)** upphör att gälla efter 3/31 januari 2018 i för att optimera funktioner för Bing-Taligenkänning. Uppdatera din kod med uppdaterade namn.

## <a name="TrouNSupport"></a>Felsökning och support

Publicera alla frågor och problem med till den [Bing Speech Service](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN-forum. Innehåller fullständig information, till exempel:

* Ett exempel på fullständiga begäran-sträng.
* Om så är tillämpligt, logga fullständiga utdata för en misslyckad begäran som innehåller ID: N.
* Procentandelen av förfrågningar som misslyckas.
