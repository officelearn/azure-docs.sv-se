---
title: Text till tal-API av Microsoft Speech service | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använd text till tal-API för att tillhandahålla text till tal-konvertering i real tid i en rad olika röster och språk
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ee9b0b47fb88cba948bc06db6eb83fe9c076fe40
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966859"
---
# <a name="bing-text-to-speech-api"></a>Bing-text till tal-API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Introduktion

Med Bing-text till tal-API kan ditt program skicka HTTP-förfrågningar till en moln server, där text är direkt syntetisk till mänskligt tal och returneras som en ljudfil. Detta API kan användas i många olika kontexter för att tillhandahålla text till tal-konvertering i real tid i en rad olika röster och språk.

## <a name="VoiceSynReq"></a>Röst syntes förfrågan

### <a name="Subscription"></a>Autentiseringstoken

Varje röst syntes förfrågan kräver en JSON Web Token-åtkomsttoken (JWT). JWT-åtkomsttoken skickas genom i rubriken för taldata. Token har en förfallo tid på 10 minuter. Information om att prenumerera på och hämta API-nycklar som används för att hämta giltiga JWT-åtkomsttoken finns i [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/).

API-nyckeln skickas till token-tjänsten. Exempel:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Nödvändig huvud information för token-åtkomst är följande.

Name| Format | Beskrivning
----|----|----
OCP-Apim-Subscription-Key | ASCII | Din prenumerationsnyckel

Token-tjänsten returnerar JWT-åtkomsttoken som `text/plain`. Därefter skickas JWT som en `Base64 access_token` till tal slut punkten som ett Authorization-huvud med strängen. `Bearer` Exempel:

`Authorization: Bearer [Base64 access_token]`

Klienterna måste använda följande slut punkt för att få åtkomst till tjänsten text till tal:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Innan du har skaffat en åtkomsttoken med prenumerations nyckeln enligt beskrivningen ovan, genererar den här länken `403 Forbidden` ett svars fel.

### <a name="Http"></a>HTTP-rubriker

I följande tabell visas de HTTP-huvuden som används för röst syntes förfrågningar.

Huvud |Value |Kommentar
----|----|----
Content-Type | Application/SSML + XML | Typ av indatamängds innehåll.
X-Microsoft-OutputFormat | **1.** SSML-16khz-bitarsläge-mono-TTS <br> **2.** RAW-16khz-bitarsläge-mono-PCM <br>**3.** Audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-bitarsläge-mono-PCM <br> **6.** Audio-16khz-128kbitrate-mono-MP3 <br> **7.** Audio-16khz-64kbitrate-mono-MP3 <br> **8.** Audio-16khz-32kbitrate-mono-MP3 | Ljud formatet för utdata.
X-Search-AppId | Ett GUID (endast hex, inga bindestreck) | Ett ID som unikt identifierar klient programmet. Detta kan vara butiks-ID: t för appar. Om ett inte är tillgängligt kan ID genereras av användaren för ett program.
X-Search-ClientID | Ett GUID (endast hex, inga bindestreck) | Ett ID som unikt identifierar en program instans för varje installation.
User-Agent | Programnamn | Program namnet måste anges och måste innehålla färre än 255 tecken.
Authorization | Autentiseringstoken |  Se avsnittet <a href="#Subscription">Authorization token</a> .

### <a name="InputParam"></a>Indataparametrar

Begär anden till Bing-text till tal-API görs med HTTP POST-anrop. Rubrikerna anges i föregående avsnitt. Texten innehåller SSML-inmatare (Speech syntes Markup Language) som representerar den text som ska syntetiseras. En beskrivning av den markering som används för att styra aspekter av tal, till exempel språk och kön för högtalaren finns i [SSML W3C-specifikationen](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Den maximala storleken på SSML-indatamängden som stöds är 1 024 tecken, inklusive alla Taggar.

###  <a name="SampleVoiceOR"></a>Exempel: begäran om röst utdata

Ett exempel på en begäran om röst utdata är följande:

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

## <a name="VoiceOutResponse"></a>Svar på röst utdata

Bing-text till tal-API använder HTTP POST för att skicka tillbaka ljud till klienten. API-svaret innehåller ljud strömmen och codecen och matchar det begärda utdataformatet. Ljudet som returnerades för en specifik begäran får inte överstiga 15 sekunder.

### <a name="SuccessfulRecResponse"></a>Exempel: lyckad syntes-svar

Följande kod är ett exempel på ett JSON-svar på en lyckad röst syntes förfrågan. Kommentaren och formateringen av koden gäller endast för det här exemplet och utelämnas av det faktiska svaret.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Exempel: syntes haverie

Följande exempel kod visar ett JSON-svar på ett röst syntes fråga-problem:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Fel svar

Fel | Beskrivning
----|----
Felaktig HTTP/400-begäran | En obligatorisk parameter saknas, är tom eller null, eller värdet som skickas till en obligatorisk eller valfri parameter är ogiltigt. En orsak till att hämta "ogiltigt" svar är att skicka ett sträng värde som är längre än den tillåtna längden. En kort beskrivning av den problematiska parametern ingår.
HTTP/401 ej auktoriserad | Begäran har inte behörighet.
HTTP/413 RequestEntityTooLarge  | SSML-indatamängden är större än vad som stöds.
HTTP/502 BadGateway | Det finns ett problem med nätverket eller ett problem på Server sidan.

Ett exempel på ett fel svar är följande:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Ändra röst utdata via SSML

Microsoft text-till-Speech API stöder SSML 1,0 enligt vad som anges i W3C:S [SSML (Speech syntes Markup Language) 1,0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). I det här avsnittet visas exempel på hur du kan ändra vissa egenskaper för genererade röst utdata som tal frekvens, uttal osv. genom att använda SSML-taggar.

1. Lägger till rast

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Engelsktalande förändringstakten

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Uttal av

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Ändra volym

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Ändra försäljningsargument

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Ändra prosody-kontur

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Observera att ljud data måste vara 8k eller 16k WAV i följande format: **CRC-kod** (CRC-32): 4 byte (DWORD) med giltigt intervall 0x00000000 ~ 0xFFFFFFFF; **Ljud format flagga**: 4 byte (DWORD) med giltigt intervall 0x00000000 ~ 0xFFFFFFFF; **Antal prov**: 4 byte (DWORD) med giltigt intervall 0x00000000 ~ 0x7FFFFFFF; **Storlek på binär brödtext**: 4 byte (DWORD) med giltigt intervall 0x00000000 ~ 0x7FFFFFFF; **Binär text**: n byte.

## <a name="SampleApp"></a>Exempel program

Information om implementering finns i [Visual C#.net-exempel programmet för text till tal](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Nationella inställningar och röst teckensnitt som stöds

I följande tabell visas några av de språk som stöds och relaterade röst teckensnitt.

Nationell inställning | Kön | Tjänsten Namnmappningen
---------|--------|------------
ar-tex * | Kvinna | ”Microsoft Server tal Text till tal-röst (ar-t.ex., Hoda)”
ar-SA | Man | ”Microsoft Server tal Text till tal-röst (ar-SA, Naayf)”
BG-BG | Man | ”Microsoft Server tal Text till tal röst (bg-BG, Ivan)”
CA-ES | Kvinna | ”Microsoft Server tal Text till tal röst (ca-ES, HerenaRUS)”
CS-CZ | Man | ”Microsoft Server tal Text till tal-röst (cs-CZ, Jakub)”
da-DK | Kvinna | ”Microsoft Server tal Text till tal-röst (da-DK, HelleRUS)”
Tyskland-AT | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-AT, Michael)”
Tyskland – CH | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-CH, Karsten)”
de-DE | Kvinna | "Microsoft Server Speech Text till tal Voice (de-DE, Hedda)"
de-DE | Kvinna | ”Microsoft Server tal Text till tal-röst (de-DE, HeddaRUS)”
de-DE | Man | "Microsoft Server Speech Text till tal Voice (de-DE, Stefan, Apollo)"
el GR | Man | ”Microsoft Server tal Text till tal-röst (el-GR, Stefanos)”
SV-Australien | Kvinna | "Microsoft Server Speech Text till tal Voice (en-AU, Catherine)"
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

 \* ar-tex stöder modern standard arabiska (MSA).

> [!NOTE]
> Observera att de tidigare tjänst namnen **Microsoft Server speech text till tal Voice (CS-CZ, vit)** och **Microsoft Server Speech text till tal Voice (en-IE, Shaun)** är föråldrade efter 3/31/2018, för att optimera API för Bing-taligenkänning trådlösa. Uppdatera koden med de uppdaterade namnen.

## <a name="TrouNSupport"></a>Fel sökning och support

Publicera alla frågor och problem i MSDN [-forumet för taligenkänning i Bing service](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) . Inkludera fullständig information, till exempel:

* Ett exempel på en fullständig sträng för begäran.
* Om så är tillämpligt, är det fullständiga resultatet av en misslyckad begäran, som innehåller logg-ID.
* Procent andelen begär Anden som har misslyckats.
