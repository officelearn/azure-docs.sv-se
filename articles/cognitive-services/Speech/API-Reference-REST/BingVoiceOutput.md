---
title: Text till tal API för tjänsten Microsoft Speech | Microsoft Docs
description: Använda text till tal-API för att ge realtidsskydd text till tal-konvertering på en mängd olika röster och språk
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 03/16/2017
ms.author: priyar
ms.openlocfilehash: 4b633cefa37c11511a8171d5a7f61b03dfaa4466
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352518"
---
# <a name="bing-text-to-speech-api"></a>Bing text till tal-API

## <a name="Introduction"></a>Introduktion

Med den Bing text till tal API, kan programmet skicka HTTP-begäranden till en server för moln, där text är omedelbart syntetiskt till mänskliga låta tal och returneras som en ljudfil. Detta API kan användas i många olika kontexter för att ge realtidsskydd text till tal-konvertering i en mängd olika språk.

## <a name="VoiceSynReq"></a>Röst sammanfattande begäran

### <a name="Subscription"></a>Autentiseringstoken

Varje röst sammanfattande begäran kräver en JSON-Webbtoken (JWT) åtkomst-token. Åtkomsttoken JWT skickas via begärandehuvudet tal. Token har en förfallotiden 10 minuter. Information om prenumerera och skaffa API-nycklar som används för att hämta giltiga JWT-åtkomsttoken finns [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/).

API-nyckeln har skickats till tjänsten token. Exempel:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Nödvändiga huvudinformationen för tokenåtkomst är som följer.

Namn| Format | Beskrivning
----|----|----
OCP-Apim-prenumeration-nyckel | ASCII | Din prenumerationsnyckel

Tokentjänsten som returnerar JWT åtkomst-token som `text/plain`. Sedan av JWT skickas som en `Base64 access_token` till tal-slutpunkt som ett authorization-huvud med strängen prefixet `Bearer`. Exempel:

`Authorization: Bearer [Base64 access_token]`

Klienter måste använda följande slutpunkt för att få åtkomst till text till tal-tjänsten:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Tills du har köpt en åtkomst-token med din prenumeration nyckel som tidigare beskrivits kan den här länken genererar en `403 Forbidden` fel svar.

### <a name="Http"></a>HTTP-huvuden

I följande tabell visas de HTTP-huvuden som används för röst sammanfattande begäranden.

Sidhuvud |Värde |Kommentarer
----|----|----
Innehållstyp | program/ssml + xml | Inkommande innehållstyp.
X-Microsoft-OutputFormat | **1.** ssml-16 khz-16-bitars-mono-text till tal <br> **2.** raw-16 khz-16-bitars-mono-pcm <br>**3.** ljud-16 khz-16 kbit/s-mono-siren <br> **4.** riff-16 khz-16 kbit/s-mono-siren <br> **5.** riff-16 khz-16-bitars-mono-pcm <br> **6.** ljud-16 khz-128kbitrate-mono-mp3 <br> **7.** ljud-16 khz-64kbitrate-mono-mp3 <br> **8.** ljud-16 khz-32kbitrate-mono-mp3 | Ljudformatet utdata.
AppId-X-sökning | En GUID (hex endast, inga bindestreck) | Ett ID som unikt identifierar klientprogrammet. Detta kan vara lagrings-ID för appar. Om en inte är tillgänglig, kan ID: T vara användare som har genererats för ett program.
X-Sök-ClientID | En GUID (hex endast, inga bindestreck) | Ett ID som unikt identifierar en programinstans för varje installation.
Användaragent | Programnamn | Programnamn krävs och måste vara färre än 255 tecken.
Auktorisering | Autentiseringstoken |  Finns det <a href="#Subscription">autentiseringstoken</a> avsnitt.

### <a name="InputParam"></a>Indataparametrar

Begäranden till Bing text till tal API görs med HTTP POST-anrop. Huvudena har angetts i föregående avsnitt. Texten innehåller tal sammanfattande Markup Language (SSML) indata som representerar texten som ska vara syntetiskt. En beskrivning av koden som används för att styra aspekter av tal, till exempel språk och kön högtalaren finns i [SSML W3C-specifikationen](http://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Den maximala storleken på SSML-indata som stöds är 1 024 tecken, inklusive alla taggar.

###  <a name="SampleVoiceOR"></a>Exempel: röst utdatabegäran

Ett exempel på en röst utdata-begäran är följande:

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

## <a name="VoiceOutResponse"></a>Röst utgående svar

Bing text till tal API använder HTTP POST för att skicka ljud tillbaka till klienten. API-svar som innehåller ljudströmmen och codec och den matchar formatet begärda utdata. Ljud returneras för en viss begäran får inte överskrida 15 sekunder.

### <a name="SuccessfulRecResponse"></a>Exempel: lyckad sammanfattande svar

Följande kod är ett exempel på en JSON-svar på en lyckad röst sammanfattande begäran. Kommentarer och formatering av koden för det här exemplet endast och har uteslutits från det faktiska svaret.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Exempel: sammanfattande fel

Följande exempelkod visar en JSON-svar till en röst sammanfattande frågefel:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Felsvar

Fel | Beskrivning
----|----
Felaktig HTTP/400-begäran | En obligatorisk parameter är tom, null eller saknas eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. En anledning för att hämta ”ogiltig” svaret är att ett strängvärde som är längre än den tillåtna längden. En kort beskrivning av parametern problematiska ingår.
HTTP/401-Ej behörig | Begäran är inte auktoriserad.
HTTP-/ 413 RequestEntityTooLarge  | SSML-indata är större än vad som stöds.
HTTP 502/BadGateway | Det finns ett nätverksrelaterade problem eller ett problem med servern.

Ett exempel på ett felsvar är följande:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Ändra röst utdata via SSML

Microsoft text till tal-API: et stöder SSML 1.0 som definierats i W3C [tal sammanfattande Markup Language (SSML) Version 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). Det här avsnittet visas exempel på ändring av vissa egenskaper genererade röst utdata som liknar tala Betygsätt uttal etc. med hjälp av SSML-taggar.

1. Lägga till break

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. Sett förändringstakten

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. Uttal

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. Ändra volym

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. Ändra breddsteg

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. Ändra prosody profil

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> Obs ljuddata måste vara 8 kB eller 16 kB wav arkiverats i följande format: **CRC kod** (CRC-32): 4 byte (DWORD) med giltiga intervallet 0x00000000 ~ 0xFFFFFFFF; **Ljud format flaggan**: 4 byte (DWORD) med giltiga intervallet 0x00000000 ~ 0xFFFFFFFF; **Antal prov**: 4 byte (DWORD) med giltiga intervallet 0x00000000 ~ 0x7FFFFFFF; **Storleken på binära brödtext**: 4 byte (DWORD) med giltiga intervallet 0x00000000 ~ 0x7FFFFFFF; **Binära brödtext**: n byte.

## <a name="SampleApp"></a>Exempelprogrammet

Implementeringsdetaljer, finns det [Visual C# .NET text till tal exempelprogrammet](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Språk och röst teckensnitt

I följande tabell visas några av de språk som stöds och relaterade röst teckensnitt.

Nationella inställningar | Kön | Tjänstemappning
---------|--------|------------
ar T.ex * | Kvinna | ”Microsoft Server Speech Text till tal-röst (ar-T.ex, Hoda)”
ar-SA | Man | ”Microsoft Server Speech Text till tal-röst (ar-SA, Naayf)”
BG-BG | Man | ”Microsoft Server Speech Text till tal röst (bg-BG, Ivan)”
CA-ES | Kvinna | ”Microsoft Server Speech Text till tal röst (ca-ES, HerenaRUS)”
CS-CZ | Man | ”Microsoft Server Speech Text till tal-röst (cs-CZ, Jakub)”
da-DK | Kvinna | ”Microsoft Server Speech Text till tal-röst (da-DK, HelleRUS)”
Tyskland AT | Man | ”Microsoft Server Speech Text till tal-röst (Tyskland AT, Michael)”
Tyskland CH | Man | ”Microsoft Server Speech Text till tal-röst (Tyskland CH, Karsten)”
de-DE | Kvinna | ”Microsoft Server Speech Text till tal-röst (de-DE, Hedda)”
de-DE | Kvinna | ”Microsoft Server Speech Text till tal-röst (de-DE, HeddaRUS)”
de-DE | Man | ”Microsoft Server Speech Text till tal-röst (de-DE, Stefan, Apollo)”
el-GR | Man | ”Microsoft Server Speech Text till tal-röst (el-GR, Stefanos)”
SV Australien | Kvinna | ”Microsoft Server Speech Text till tal-röst (SV-AU, Catherine)”
SV Australien | Kvinna | ”Microsoft Server Speech Text till tal-röst (SV-AU, HayleyRUS)”
en Certifikatutfärdare | Kvinna | ”Microsoft Server Speech Text till tal-röst (SV-CA, Johan)”
en Certifikatutfärdare | Kvinna | ”Microsoft Server Speech Text till tal-röst (SV-CA, HeatherRUS)”
en-GB | Kvinna | ”Microsoft Server Speech Text till tal-röst (en-GB, Susan, Apollo)”
en-GB | Kvinna | ”Microsoft Server Speech Text till tal-röst (en-GB, HazelRUS)”
en-GB | Man | ”Microsoft Server Speech Text till tal-röst (en-GB, George, Apollo)”
SV IE | Man | ”Microsoft Server Speech Text till tal-röst (SV-IE, Sara)”
SV IN | Kvinna | ”Microsoft Server Speech Text till tal-röst (SV-IN-, Heera, Apollo)”
SV IN | Kvinna | ”Microsoft Server Speech Text till tal-röst (SV-IN-, PriyaRUS)”
SV IN | Man | ”Microsoft Server Speech Text till tal-röst (SV-IN-, Ravi, Apollo)”
sv-SE | Kvinna | ”Microsoft Server Speech Text till tal-röst (en-US, ZiraRUS)”
sv-SE | Kvinna | ”Microsoft Server Speech Text till tal-röst (en-US, JessaRUS)”
sv-SE | Man | ”Microsoft Server Speech Text till tal-röst (en-US, BenjaminRUS)”
es-ES | Kvinna | ”Microsoft Server Speech Text till tal röst (es-ES, Lisa, Apollo)”
es-ES | Kvinna | ”Microsoft Server Speech Text till tal röst (es-ES, HelenaRUS)”
es-ES | Man | ”Microsoft Server Speech Text till tal röst (es-ES, Pablo, Apollo)”
es-MX | Kvinna | ”Microsoft Server Speech Text till tal röst (es-MX, HildaRUS)”
es-MX | Man | ”Microsoft Server Speech Text till tal röst (es-MX, Raul, Apollo)”
fi-FI | Kvinna | ”Microsoft Server Speech Text till tal-röst (fi-FI, HeidiRUS)”
fr-CA | Kvinna | ”Microsoft Server Speech Text till tal röst (fr-CA, Caroline)”
fr-CA | Kvinna | ”Microsoft Server Speech Text till tal röst (fr-CA, HarmonieRUS)”
fr CH | Man | ”Microsoft Server Speech Text till tal röst (fr CH, Guillaume)”
fr-FR | Kvinna | ”Microsoft Server Speech Text till tal röst (fr-FR, Julia, Apollo)”
fr-FR | Kvinna | ”Microsoft Server Speech Text till tal röst (fr-FR, HortenseRUS)”
fr-FR | Man | ”Microsoft Server Speech Text till tal röst (fr-FR, Paul, Apollo)”
han IL| Man| ”Microsoft Server Speech Text till tal-röst (he-IL, Asaf)”
Hej IN | Kvinna | ”Microsoft Server Speech Text till tal-röst (hi-IN-, Kalpana, Apollo)”
Hej IN | Kvinna | ”Microsoft Server Speech Text till tal-röst (hi-IN-, Kalpana)”
Hej IN | Man | ”Microsoft Server Speech Text till tal-röst (hi-IN-, Hemant)”
HR-HR | Man | ”Microsoft Server Speech Text till tal röst (hr-HR, Matej)”
hu-HU | Man | ”Microsoft Server Speech Text till tal-röst (hu-HU, Szabolcs)”
ID-ID | Man | ”Microsoft Server Speech Text till tal-röst (id-ID, Andika)”
IT-IT | Man | ”Microsoft Server Speech Text till tal-röst (it-IT, Cosimo, Apollo)”
ja-JP | Kvinna | ”Microsoft Server Speech Text till tal röst (ja-JP, Ayumi, Apollo)”
ja-JP | Man | ”Microsoft Server Speech Text till tal röst (ja-JP, Ichiro, Apollo)”
ja-JP | Kvinna | ”Microsoft Server Speech Text till tal röst (ja-JP, HarukaRUS)”
ja-JP | Kvinna | ”Microsoft Server Speech Text till tal röst (ja-JP, LuciaRUS)”
ja-JP | Man | ”Microsoft Server Speech Text till tal röst (ja-JP, EkaterinaRUS)”
ko-KR | Kvinna | ”Microsoft Server Speech Text till tal-röst (ko-KR, HeamiRUS)”
Mina MS | Man | ”Microsoft Server Speech Text till tal röst (ms Mina Rizwan)”
NB-NO | Kvinna | ”Microsoft Server Speech Text till tal röst (nb-NO HuldaRUS)”
NL-NL | Kvinna | ”Microsoft Server Speech Text till tal röst (nl-NL, HannaRUS)”
pl-PL | Kvinna | ”Microsoft Server Speech Text till tal röst (pl-PL, PaulinaRUS)”
pt-BR | Kvinna | ”Microsoft Server Speech Text till tal röst (pt-BR, HeloisaRUS)”
pt-BR | Man | ”Microsoft Server Speech Text till tal röst (pt-BR, Mikael, Apollo)”
PT-PT | Kvinna | ”Microsoft Server Speech Text till tal röst (pt-PT, HeliaRUS)”
RO-RO | Man | ”Microsoft Server Speech Text till tal-röst (ro-RO, Andrei)”
ru-RU | Kvinna | ”Microsoft Server Speech Text till tal röst (ru-RU, Irina, Apollo)”
ru-RU | Man | ”Microsoft Server Speech Text till tal röst (ru-RU, Pavel, Apollo)”
sk-SK | Man | ”Microsoft Server Speech Text till tal röst (sk-SK, Filip)”
sl-SI | Man | ”Microsoft Server Speech Text till tal röst (sl-SI, Lado)”
SV-SE | Kvinna | ”Microsoft Server Speech Text till tal-röst (sv-SE, HedvigRUS)”
ta IN | Man | ”Microsoft Server Speech Text till tal-röst (c-IN-, Valluvar)”
TH-TH | Man | ”Microsoft Server Speech Text till tal röst (th-TH, Pattara)”
TR-TR | Kvinna | ”Microsoft Server Speech Text till tal röst (tr-TR, SedaRUS)”
Vi VN | Man | ”Microsoft Server Speech Text till tal röst (vi VN ett)”
zh-CN | Kvinna | ”Microsoft Server Speech Text till tal-röst (zh-CN, HuihuiRUS)”
zh-CN | Kvinna | ”Microsoft Server Speech Text till tal-röst (zh-CN, Yaoyao, Apollo)”
zh-CN | Man | ”Microsoft Server Speech Text till tal-röst (zh-CN, Kangkang, Apollo)”
zh-HK | Kvinna | ”Microsoft Server Speech Text till tal-röst (zh-HK Tracy, Apollo)”
zh-HK | Kvinna | ”Microsoft Server Speech Text till tal-röst (zh-HK TracyRUS)”
zh-HK | Man | ”Microsoft Server Speech Text till tal-röst (zh-HK Danny, Apollo)”
zh-TW | Kvinna | ”Microsoft Server Speech Text till tal-röst (zh-TW, Yating, Apollo)”
zh-TW | Kvinna | ”Microsoft Server Speech Text till tal-röst (zh-TW, HanHanRUS)”
zh-TW | Man | ”Microsoft Server Speech Text till tal-röst (zh-TW, Zhiwei, Apollo)”
 * ar-T.ex stöder Modern Standard arabiska (MSA).

> [!NOTE]
> Observera att tidigare tjänstnamn **Microsoft Server Speech Text till tal-röst (cs-CZ, Vit)** och **Microsoft Server Speech Text till tal-röst (SV-IE, Shaun)** inaktuell efter 3/31/2018 i för att optimera Bing Speech API-funktioner. Uppdatera din kod med uppdaterade namn.

## <a name="TrouNSupport"></a>Felsökning och support

Publicerar alla frågor och problem att den [Bing tal Service](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN-forum. Inkludera fullständig information, exempelvis:

* Ett exempel på fullständiga förfrågan.
* Om tillämpligt, logga fullständig resultatet av en misslyckad begäran som innehåller ID: N.
* Procentandelen förfrågningar som misslyckas.
