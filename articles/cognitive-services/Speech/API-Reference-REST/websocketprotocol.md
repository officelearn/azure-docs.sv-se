---
title: Taligenkänning i Bing WebSocket-protokoll | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Protokoll dokumentation för Taligenkänning i Bing som baseras på WebSockets
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e7f51d49624d5019bec058a2d12f6ca2f1366938
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966892"
---
# <a name="bing-speech-websocket-protocol"></a>Taligenkänning i Bing WebSocket-protokoll

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Taligenkänning i Bing är en molnbaserad plattform som innehåller de mest avancerade algoritmerna som är tillgängliga för att konvertera talade ljud till text. Taligenkänning i Bing-protokollet definierar [anslutnings konfigurationen](#connection-establishment) mellan klient program och tjänsten och de tal igenkännings meddelanden som utbyts mellan motparter ([klientbaserade meddelanden](#client-originated-messages) och meddelanden som [skapats av tjänsten](#service-originated-messages) ). Dessutom beskrivs [telemetri](#telemetry-schema) och [fel hantering](#error-handling) .

## <a name="connection-establishment"></a>Anslutnings etablering

Speech service-protokollet följer WebSocket standard Specification [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). En WebSocket-anslutning börjar ut som en HTTP-begäran som innehåller HTTP-huvuden som visar att klienten vill uppgradera anslutningen till en WebSocket i stället för att använda HTTP-semantik. Servern anger att den är villiga att delta i WebSocket-anslutningen genom att returnera `101 Switching Protocols` ett HTTP-svar. Efter utbytet av denna hand skakning behåller både klienten och tjänsten socketen öppen och börjar använda ett meddelande baserat protokoll för att skicka och ta emot information.

För att påbörja WebSocket-handskakningen skickar klient programmet en HTTPS GET-begäran till tjänsten. Den innehåller en standard uppgraderings rubrik för WebSocket tillsammans med andra huvuden som är speciella för tal.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

Tjänsten svarar med:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Alla tal begär Anden kräver [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) -kryptering. Användning av okrypterade tal begär Anden stöds inte. Följande TLS-version stöds:

* TLS 1.2

### <a name="connection-identifier"></a>Anslutnings identifierare

Tal tjänsten kräver att alla klienter innehåller ett unikt ID för att identifiera anslutningen. Klienterna *måste* inkludera *X-ConnectionId* -huvudet när de startar en WebSocket-handskakning. *X-ConnectionId* -huvudet måste vara ett UUID-värde ( [Universal Unique Identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier) ). Begär Anden om uppgradering av WebSocket som inte innehåller *x-ConnectionId*, ange inte något värde för *x-ConnectionId* -sidhuvudet eller inkludera inte ett giltigt UUID-värde avvisas av tjänsten med ett http `400 Bad Request` -svar.

### <a name="authorization"></a>Authorization

Förutom standard rubrikerna för WebSocket-handskakning kräver tal begär Anden ett *Authorization* -huvud. Anslutnings begär Anden utan denna rubrik avvisas av tjänsten med ett `403 Forbidden` http-svar.

*Authorization* -huvudet måste innehålla en JSON Web token-ÅTKOMSTTOKEN (JWT).

Information om hur du prenumererar på och skaffar API-nycklar som används för att hämta giltiga JWT-åtkomsttoken finns på sidan [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/) .

API-nyckeln skickas till token-tjänsten. Exempel:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Följande rubrik information krävs för åtkomst till token.

| Name | Format | Beskrivning |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Din prenumerationsnyckel |

Token-tjänsten returnerar JWT-åtkomsttoken som `text/plain`. Därefter skickas JWT som en `Base64 access_token` till hand skakningen som ett *Authorization* -huvud med strängen. `Bearer` Exempel:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookies

Klienterna *måste ha* stöd för http-cookies enligt vad som anges i [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>HTTP-omdirigering

Klienterna *måste ha* stöd för standardmekanismer för omdirigering som anges i [specifikationen för http-protokollet](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Tal slut punkter

Klienterna *måste* använda en lämplig slut punkt för tal tjänsten. Slut punkten baseras på tolknings läge och språk. I tabellen visas några exempel.

| Läge | `Path` | Tjänstens URI |
| -----|-----|-----|
| Interaktiv | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Konversation | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Diktering | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Mer information finns på sidan för [tjänst-URI](../GetStarted/GetStartedREST.md#service-uri) .

### <a name="report-connection-problems"></a>Rapportera anslutnings problem

Klienterna bör omedelbart rapportera alla problem som påträffas när en anslutning upprättas. Meddelande protokollet för att rapportera misslyckade anslutningar beskrivs i [telemetri om anslutnings fel](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Begränsningar för anslutningens varaktighet

Jämfört med vanliga HTTP-anslutningar för webb tjänster är WebSocket-anslutningar en *längre* tid. Tal tjänsten placerar begränsningar för WebSocket-anslutningarnas varaktighet till tjänsten:

 * Maximal varaktighet för alla aktiva WebSocket-anslutningar är 10 minuter. En anslutning är aktiv om antingen tjänsten eller klienten skickar WebSocket-meddelanden över anslutningen. Tjänsten avslutar anslutningen utan varning när gränsen nås. Klienterna bör utveckla användar scenarier som inte kräver att anslutningen förblir aktiv vid eller närmast högsta anslutnings livs längd.

 * Maximal varaktighet för inaktiva WebSocket-anslutningar är 180 sekunder. En anslutning är inaktiv om varken tjänsten eller klienten har skickat ett WebSocket-meddelande via anslutningen. När den högsta inaktiva livs längden har nåtts avslutar tjänsten den inaktiva WebSocket-anslutningen.

## <a name="message-types"></a>Meddelande typer

När en WebSocket-anslutning har upprättats mellan klienten och tjänsten kan både klienten och tjänsten skicka meddelanden. I det här avsnittet beskrivs formatet för dessa WebSocket-meddelanden.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) anger att WebSocket-meddelanden kan överföra data med antingen en text eller en binär kodning. De två kodningarna använder olika format med kabel format. Varje format är optimerat för effektiv kodning, överföring och avkodning av meddelandets nytto Last.

### <a name="text-websocket-messages"></a>Text WebSocket-meddelanden

SMS-WebSocket-meddelanden har en nytto last för textinformation som består av ett avsnitt av sidhuvuden och en brödtext, avgränsade med det välkända rad matnings retur-paret som används för HTTP-meddelanden. Och, till exempel HTTP-meddelanden, anger text WebSocket-meddelanden huvuden i *Namn: värde* formatet avgränsat med ett retur rad matnings par. All text som ingår i ett text-WebSocket-meddelande *måste* använda [UTF-8-](https://tools.ietf.org/html/rfc3629) kodning.

Text WebSocket-meddelanden måste ange en meddelande Sök väg i rubrik *Sök vägen*. Värdet för den här rubriken måste vara ett av tal protokoll meddelande typerna som definierats senare i det här dokumentet.

### <a name="binary-websocket-messages"></a>Binär WebSocket-meddelanden

Binär WebSocket-meddelanden har en binär nytto Last. I Speech service Protocol överförs ljudet till och tas emot från tjänsten med hjälp av binär WebSocket-meddelanden. Alla andra meddelanden är text WebSocket-meddelanden.

Som text WebSocket-meddelanden består binära WebSocket-meddelanden av ett sidhuvud och ett text avsnitt. De första 2 byten i det binära WebSocket-meddelandet anger i [big-endian](https://en.wikipedia.org/wiki/Endianness) -ordningen 16-bitars heltals storlek för rubrik avsnittet. Minsta rubrik avsnitts storlek är 0 byte. Den maximala storleken är 8 192 byte. Texten i rubrikerna för binär WebSocket-meddelanden *måste* använda [US-ASCII-](https://tools.ietf.org/html/rfc20) kodning.

Rubriker i ett binärt WebSocket-meddelande kodas i samma format som i text WebSocket-meddelanden. *Namnet: värde* formatet avgränsas med ett rad matnings par med en vagn RETUR. Binär WebSocket-meddelanden måste ange en meddelande Sök väg i rubrik *Sök vägen*. Värdet för den här rubriken måste vara ett av tal protokoll meddelande typerna som definierats senare i det här dokumentet.

Både text-och binär WebSocket-meddelanden används i tal tjänst protokollet.

## <a name="client-originated-messages"></a>Klient-ursprungliga meddelanden

När anslutningen har upprättats kan både klienten och tjänsten börja skicka meddelanden. I det här avsnittet beskrivs formatet och nytto lasten för meddelanden som klient program skickar till tal tjänsten. I avsnittet [service – ursprungliga meddelanden](#service-originated-messages) visas meddelanden som kommer från i tal tjänsten och skickas till klient programmen.

Huvud meddelanden som skickas av klienten till tjänsterna är `speech.config`, `audio`och `telemetry` . Innan vi betraktar varje meddelande i detalj beskrivs de vanliga meddelande rubrikerna för alla dessa meddelanden.

### <a name="required-message-headers"></a>Obligatoriska meddelande rubriker

Följande rubriker krävs för alla klient-ursprungliga meddelanden.

| Huvud | Value |
|----|----|
| `Path` | Meddelande Sök vägen som anges i det här dokumentet |
| X-RequestId | UUID i formatet "inget-streck" |
| X-Timestamp | Tids stämpling för klientens UTC-tid i ISO 8601-format |

#### <a name="x-requestid-header"></a>X – RequestId-huvud

Klient-ursprungliga begär Anden identifieras unikt av meddelande huvudet *X-RequestId* . Den här rubriken krävs för alla klient-ursprungliga meddelanden. Huvudet *X-RequestId* måste vara ett UUID i formatet "inget-streck", till exempel *123e4567e89b12d3a456426655440000*. Den *kan inte* vara i kanoniskt format *123e4567-e89b-12d3-A456-426655440000*. Begär Anden utan ett *X-RequestId-* huvud eller med ett huvud värde som använder fel format för UUID: er gör att tjänsten avslutar WebSocket-anslutningen.

#### <a name="x-timestamp-header"></a>X – timestamp-sidhuvud

Varje meddelande som skickas till en Speech service av ett klient program *måste* innehålla ett *X-timestamp-* huvud. Värdet för den här rubriken är den tidpunkt då klienten skickar meddelandet. Begär Anden utan ett *X-timestamp-* huvud eller med ett huvud värde som använder fel format gör att tjänsten avslutar WebSocket-anslutningen.

Huvudet *X-timestamp* måste ha formatet ' YYYY'-'MM'-'dd'T'HH ': ' mm ': ' ss '. fffffffZ ' WHERE ' fffffff ' är en del av en sekund. Till exempel betyder "12,5" "12 + 5/10 sekunder" och "12,526" "12 plus 526/1000 sekunder". Det här formatet följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och, till skillnad från standard-http- *datumets* rubrik, kan du ange en millisekunds upplösning. Klient program kan runda tidsstämplar till närmaste millisekunder. Klient program måste se till att enhets klockan korrekt spårar tid med hjälp av en [NTP-server (Network Time Protocol)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Meddelande`speech.config`

Tal tjänsten måste känna till egenskaperna för ditt program för att ge bästa möjliga tal igenkänning. De obligatoriska egenskaperna innehåller information om den enhet och det operativ system som ger ditt program. Du anger den här informationen i `speech.config` meddelandet.

Klienter *måste* skicka ett `speech.config` meddelande direkt efter att de upprättat anslutningen till tal tjänsten och innan de `audio` skickar några meddelanden. Du behöver bara skicka ett `speech.config` meddelande en gång per anslutning.

| Fält | Beskrivning |
|----|----|
| Meddelande kodning för WebSocket | Text |
| Body | Nytto lasten som en JSON-struktur |

#### <a name="required-message-headers"></a>Obligatoriska meddelande rubriker

| Huvudnamn | Value |
|----|----|
| `Path` | `speech.config` |
| X-Timestamp | Tids stämpling för klientens UTC-tid i ISO 8601-format |
| Content-Type | program/JSON; charset = utf-8 |

Precis som med alla klient-ursprungliga meddelanden i tal tjänst protokollet `speech.config` *måste* meddelandet innehålla ett *X-timestamp-* huvud som registrerar klientens UTC-tid när meddelandet skickades till tjänsten. Meddelandet kräver *inte* en *X-RequestId-* rubrik eftersom det här meddelandet inte är associerat med en viss talindata-begäran. `speech.config`

#### <a name="message-payload"></a>Meddelande nytto Last
Nytto lasten för `speech.config` meddelandet är en JSON-struktur som innehåller information om programmet. I följande exempel visas den här informationen. Information om klient-och enhets kontext ingår i *kontext* ELEMENTET i JSON-strukturen.

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>System element

Elementet system. version i `speech.config` meddelandet innehåller den version av tal SDK-programvaran som används av klient programmet eller enheten. Värdet är i formatet *Major. minor. Build. gren*. Du kan utelämna *gren* komponenten om den inte är tillämplig.

##### <a name="os-element"></a>OS-element

| Fält | Beskrivning | Användning |
|-|-|-|
| OS. Platform | OS-plattformen som är värd för programmet, till exempel Windows, Android, iOS eller Linux |Obligatorisk |
| os.name | Operativ systemets produkt namn, till exempel Debian eller Windows 10 | Obligatorisk |
| os.version | Operativ systemets version i formatet *Major. minor. Build. Branch* | Obligatorisk |

##### <a name="device-element"></a>Enhets element

| Fält | Beskrivning | Användning |
|-|-|-|
| device.manufacturer | Enhetens maskin varu tillverkare | Obligatorisk |
| enhet. modell | Enhets modellen | Obligatorisk |
| device.version | Enhetens program varu version som tillhandahålls av enhets tillverkaren. Det här värdet anger en version av enheten som kan spåras av tillverkaren. | Obligatorisk |

### <a name="message-audio"></a>Meddelande`audio`

Talindata-aktiverade klient program skickar ljud till tal-tjänsten genom att konvertera ljud strömmen till en serie ljud segment. Varje ljud segment innehåller ett segment av det talade ljud som ska skrivas av tjänsten. Maximal storlek för ett enskilt ljud segment är 8 192 byte. Meddelanden om ljud strömmar är *binära WebSocket-meddelanden*.

Klienter använder `audio` meddelandet för att skicka ett ljud segment till tjänsten. Klienterna läser ljud från mikrofonen i segment och skickar dessa segment till tal service för avskrift. Det första `audio` meddelandet måste innehålla en välformulerad rubrik som korrekt anger att ljudet överensstämmer med ett av de kodnings format som stöds av tjänsten. Ytterligare `audio` meddelanden innehåller endast data Ströms data från binärfiler som läses från mikrofonen.

Klienter kan eventuellt skicka ett `audio` meddelande med en tom text längd. Det här meddelandet visar en tjänst som klienten vet att användaren slutade tala, att uttryck är klar och att mikrofonen är avstängd.

Tal tjänsten använder det första `audio` meddelandet som innehåller en unik begärande-ID för att signalera starten av en ny begäran/svars cykel eller *Aktivera*. När tjänsten har tagit emot `audio` ett meddelande med en ny ID för begäran tar den bort alla köade eller ej skickade meddelanden som är associerade med en tidigare aktivering.

| Fält | Beskrivning |
|-------------|----------------|
| Meddelande kodning för WebSocket | Binary |
| Body | Binära data för ljud segmentet. Maximal storlek är 8 192 byte. |

#### <a name="required-message-headers"></a>Obligatoriska meddelande rubriker

Följande rubriker krävs för alla `audio` meddelanden.

| Huvud         |  Value     |
| ------------- | ---------------- |
| `Path` | `audio` |
| X-RequestId | UUID i formatet "inget-streck" |
| X-Timestamp | Tids stämpling för klientens UTC-tid i ISO 8601-format |
| Content-Type | Ljudets innehålls typ. Typen måste vara antingen *ljud/x-WAV* (PCM) eller *Audio/silke* (silke). |

#### <a name="supported-audio-encodings"></a>Ljud kodning som stöds

I det här avsnittet beskrivs de ljud-codecar som stöds av tal tjänsten.

##### <a name="pcm"></a>PCM

Röst tjänsten accepterar PCM-ljud (uncompressed Code Modulation). Ljud skickas till tjänsten i [WAV](https://en.wikipedia.org/wiki/WAV) -format, så det första ljud segmentet *måste* innehålla ett giltigt riff-huvud ( [Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) ). Om en klient initierar en turn med ett ljud segment som *inte* innehåller ett giltigt riff-huvud avvisar tjänsten begäran och avslutar WebSocket-anslutningen.

PCM-ljudet *måste* samplas med 16 kHz med 16 bitar per sampel och en kanal (*riff-16khz-bitarsläge-mono-PCM*). Tal tjänsten stöder inte stereo ljud strömmar och avvisar ljud strömmar som inte använder den angivna bit hastigheten, samplings frekvensen eller antalet kanaler.

##### <a name="opus"></a>Opus

Opus är en öppen, royalty fri och mycket mångsidig ljud-codec. Tal tjänsten stöder Opus med konstant bit hastighet på `32000` eller. `16000` Endast behållaren för Opus stöds för närvarande som anges `audio/ogg` av MIME-typen. `OGG`

Om du vill använda Opus ändrar du [JavaScript-exemplet](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) och `RecognizerSetup` ändrar metoden till Return.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Identifiera slutet av tal

Människor signalerar inte uttryckligen när de har talat klart. Alla program som accepterar tal som indata har två alternativ för att hantera slutet av tal i en ljud ström: identifiering av tjänstens slut punkt för tal och identifiering av klientens slut punkt. Med de här två valen är tjänstens slut för tal identifiering vanligt vis en bättre användar upplevelse.

##### <a name="service-end-of-speech-detection"></a>Identifiering av tjänstens slut för tal

För att du ska kunna bygga den perfekta tal upplevelsen för hands versioner kan-programmen identifiera när användaren har talat klart. Klienter skickar ljud från mikrofonen som *ljud* segment tills tjänsten upptäcker tystnad och svarar igen med ett `speech.endDetected` meddelande.

##### <a name="client-end-of-speech-detection"></a>Identifiering av klientens slut på tal

Klient program som gör att användaren kan signalera slutet av tal på något sätt kan även ge tjänsten den signalen. Ett klient program kan till exempel ha en "Stop" eller "mute"-knapp som användaren kan trycka på. För att signalera slut på tal skickar klient program ett *ljud* segments meddelande med en tom text sträng. Tal tjänsten tolkar det här meddelandet som slutet av den inkommande ljud strömmen.

### <a name="message-telemetry"></a>Meddelande`telemetry`

Klient program *måste* bekräfta slutet på varje turn genom att skicka telemetri om funktionen för att gå till tal-tjänsten. Med bekräftelse på slut punkt kan röst tjänsten se till att alla meddelanden som krävs för att slutföra begäran och dess svar har tagits emot av klienten. Med bekräftelse bekräftelse kan du också kontrol lera att klient programmen fungerar som förväntat i tal tjänsten. Den här informationen är värdefull om du behöver hjälp med att felsöka ditt tal-aktiverade program.

Klienterna måste bekräfta slutet på en turn genom att skicka `telemetry` ett meddelande snart när ett `turn.end` meddelande har tagits emot. Klienterna bör försöka bekräfta `turn.end` så snart som möjligt. Om ett klient program inte kan bekräfta sin tur kan röst tjänsten avbryta anslutningen med ett fel. Klienterna måste bara skicka `telemetry` ett meddelande för varje begäran och svar som identifieras av värdet *X-RequestId* .

| Fält | Beskrivning |
| ------------- | ---------------- |
| Meddelande kodning för WebSocket | Text |
| `Path` | `telemetry` |
| X-Timestamp | Tids stämpling för klientens UTC-tid i ISO 8601-format |
| Content-Type | `application/json` |
| Body | En JSON-struktur som innehåller klient information om turn |

Schemat för `telemetry` meddelandets brödtext definieras i avsnittet [telemetri-schema](#telemetry-schema) .

#### <a name="telemetry-for-interrupted-connections"></a>Telemetri för avbrutna anslutningar

Om nätverks anslutningen Miss lyckas av någon anledning under en turn och klienten *inte* får ett `turn.end` meddelande från tjänsten, skickar klienten ett `telemetry` meddelande. Det här meddelandet beskriver den misslyckade begäran nästa gången klienten ansluter till tjänsten. Klienterna behöver inte omedelbart försöka upprätta en anslutning för att skicka `telemetry` meddelandet. Meddelandet kan vara buffrat på klienten och skickas via en framtida anslutning som krävs av användaren. Meddelandet för den misslyckade begäran *måste* använda värdet *X-RequestId* från den misslyckade begäran. `telemetry` Den kan skickas till tjänsten så snart en anslutning upprättas, utan att vänta på att skicka eller ta emot andra meddelanden.

## <a name="service-originated-messages"></a>Tjänst – meddelanden som har sitt ursprung

I det här avsnittet beskrivs meddelanden som har sitt ursprung i tal tjänsten och som skickas till klienten. Tal tjänsten underhåller ett register med klient funktioner och genererar de meddelanden som krävs av varje klient, så att alla klienter inte får alla meddelanden som beskrivs här. För det kortfattat refereras meddelanden av värdet i *Sök vägs* rubriken. Vi refererar till exempel ett WebSocket-textmeddelande med värdet `speech.hypothesis` *Path* som ett tal. hypotes-meddelande.

### <a name="message-speechstartdetected"></a>Meddelande`speech.startDetected`

`speech.startDetected` Meddelandet anger att tal tjänsten identifierade tal i ljud strömmen.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Meddelande kodning för WebSocket | Text |
| `Path` | `speech.startDetected` |
| Content-Type | program/JSON; charset = utf-8 |
| Body | JSON-strukturen som innehåller information om villkoren när början av tal identifierades. *Offset* -fältet i den här strukturen anger förskjutningen (i 100-nanosekunder-enheter) när tal identifierades i ljud strömmen, i förhållande till början av data strömmen. |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Meddelande`speech.hypothesis`

Under tal igenkänningen genererar tal tjänsten regelbundet Hypotheses om orden som tjänsten har identifierat. Tal tjänsten skickar dessa Hypotheses till klienten ungefär var 300 millisekunder. Passar bara för att förbättra upplevelsen av användarens tal. `speech.hypothesis` Du får inte ta något beroende på innehållet eller precisionen för texten i dessa meddelanden.

 `speech.hypothesis` Meddelandet gäller för de klienter som har en viss text åter givnings funktion och som du vill ge i real tid om den pågående igenkänningen till den person som talar.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Meddelande kodning för WebSocket | Text |
| `Path` | `speech.hypothesis` |
| X-RequestId | UUID i formatet "inget-streck" |
| Content-Type | application/json |
| Body | Tal hypotes JSON-strukturen |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

*Offset* -elementet anger förskjutningen (i 100-nanosekunder-enheter) när frasen identifierades, i förhållande till starten av ljud strömmen.

Elementet *duration* anger varaktigheten (i 100 – nanosekunder) för den här tal frasen.

Klienterna får inte göra några antaganden om frekvens, tidpunkt eller text som finns i en tal-eller text i tal-och Hypotheses. Hypotheses är bara ögonblicks bilder i avskrifts processen i tjänsten. De representerar inte en stabil ackumulering av avskrift. Till exempel kan en första tal-hypotes innehålla orden "fina roliga" och den andra hypotesen kan innehålla orden "hitta roligare". Tal tjänsten utför inte någon efter bearbetning (till exempel versaler, interpunktion) på texten i tal hypotesen.

### <a name="message-speechphrase"></a>Meddelande`speech.phrase`

När röst tjänsten fastställer att den har tillräckligt med information för att skapa ett igenkännings resultat som inte ändras, genererar `speech.phrase` tjänsten ett meddelande. Tal tjänsten genererar dessa resultat när den har identifierat att användaren har avslutat en mening eller fras.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Meddelande kodning för WebSocket | Text |
| `Path` | `speech.phrase` |
| Content-Type | application/json |
| Body | JSON-strukturen för tal frasen |

JSON-schemat för tal fraser innehåller följande fält: `RecognitionStatus`, `DisplayText`, `Offset`och `Duration`. Mer information om de här fälten finns i [Avskrifts svar](../concepts.md#transcription-responses).

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>Meddelande`speech.endDetected`

`speech.endDetected` Meddelandet anger att klient programmet ska stoppa strömnings ljud till tjänsten.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Meddelande kodning för WebSocket | Text |
| `Path` | `speech.endDetected` |
| Body | JSON-strukturen som innehåller förskjutningen när slutet av tal identifierades. Förskjutningen visas i 100-nanosekunder enheter förskjutning från början av ljudet som används för igenkänning. |
| Content-Type | program/JSON; charset = utf-8 |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

*Offset* -elementet anger förskjutningen (i 100-nanosekunder-enheter) när frasen identifierades, i förhållande till starten av ljud strömmen.

### <a name="message-turnstart"></a>Meddelande`turn.start`

`turn.start` Signalerar starten av en tur från tjänstens perspektiv. `turn.start` Meddelandet är alltid det första svars meddelande som du får för alla förfrågningar. Om du inte får ett `turn.start` meddelande antar du att tjänst anslutningens tillstånd är ogiltigt.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Meddelande kodning för WebSocket | Text |
| `Path` | `turn.start` |
| Content-Type | program/JSON; charset = utf-8 |
| Body | JSON-struktur |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

Bröd texten i `turn.start` meddelandet är en JSON-struktur som innehåller kontexten för början av turn. *Kontext* elementet innehåller en *serviceTag* -egenskap. Den här egenskapen anger ett tagg värde som tjänsten har associerat med turn. Det här värdet kan användas av Microsoft om du behöver hjälp med fel sökning av fel i ditt program.

### <a name="message-turnend"></a>Meddelande`turn.end`

`turn.end` Signalerar slutet av en tur från tjänstens perspektiv. `turn.end` Meddelandet är alltid det sista svars meddelandet som du får för alla förfrågningar. Klienter kan använda det här meddelandet som en signal för rensnings aktiviteter och övergår till inaktivt läge. Om du inte får ett `turn.end` meddelande antar du att tjänst anslutningens tillstånd är ogiltigt. I dessa fall stänger du den befintliga anslutningen till tjänsten och ansluter igen.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Meddelande kodning för WebSocket | Text |
| `Path` | `turn.end` |
| Body | Inga |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Telemetri-schema

Bröd texten i *telemetri* -meddelandet är en JSON-struktur som innehåller klient information om en turn eller anslutnings försök. Strukturen består av klientens tidsstämplar som registrerar när klient händelser inträffar. Varje tidsstämpel måste vara i ISO 8601-format enligt beskrivningen i avsnittet "X-timestamp-huvud". *Telemetri* meddelanden som inte anger alla obligatoriska fält i JSON-strukturen eller som inte använder rätt format för tidsstämpel kan orsaka att tjänsten avslutar anslutningen till klienten. Klienter *måste* ange giltiga värden för alla obligatoriska fält. Klienter *ska* ange värden för valfria fält när det är lämpligt. Värdena som visas i exemplen i det här avsnittet är endast för illustration.

Telemetri-schemat är indelat i följande delar: mottagna tidsstämplar och mått för meddelanden. Formatet och användningen av varje del anges i följande avsnitt.

### <a name="received-message-time-stamps"></a>Mottagna tidsstämplar för meddelande

Klienterna måste ta med tids inleveransdatum för alla meddelanden som de får när de har anslutit till tjänsten. Dessa värden måste registrera den tid då klienten *fick* varje meddelande från nätverket. Värdet ska inte registrera någon annan tid. Klienten ska till exempel inte registrera den tidpunkt då den *handlade* meddelandet. De mottagna tidsstämplarna för meddelandet anges i en matris med *Namn: värde* -par. Namnet på paret anger meddelandets *sökvägar* . Värdet för paret anger klient tiden då meddelandet togs emot. Eller, om fler än ett meddelande med det angivna namnet togs emot, är värdet för paret en matris med tidsstämplar som anger när dessa meddelanden togs emot.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Klienterna *måste* bekräfta att alla meddelanden som skickas av tjänsten har inkluderats, inklusive tidsstämplar för dessa meddelanden i JSON-texten. Om en klient inte kan bekräfta att ett meddelande har mottagits kan tjänsten avsluta anslutningen.

### <a name="metrics"></a>Mått

Klienter måste innehålla information om händelser som inträffat under en begär ande livs längd. Följande mått stöds: `Connection`, `Microphone`och `ListeningTrigger`.

### <a name="metric-connection"></a>Kostnads`Connection`

`Connection` Måttet anger information om anslutnings försök av klienten. Måttet måste innehålla tidsstämplar för när WebSocket-anslutningen startades och avslutades. Måttet krävs *bara för den första uppkopplingen av en anslutning.* `Connection` Efterföljande sväng krävs inte för att inkludera den här informationen. Om en klient gör flera anslutnings försök innan en anslutning upprättas, ska information om *alla* anslutnings försök inkluderas. Mer information finns i [telemetri om anslutnings problem](#connection-failure-telemetry).

| Fält | Beskrivning | Användning |
| ----- | ----------- | ----- |
| Name | `Connection` | Obligatorisk |
| ID | Värdet för anslutnings-ID som användes i *X-ConnectionId* -huvudet för denna anslutningsbegäran | Obligatorisk |
| Start | Tiden då klienten skickade anslutningsbegäran | Obligatorisk |
| slutpunkt | Den tidpunkt då klienten fick ett meddelande om att anslutningen upprättats eller, i fel fall, nekad, nekad eller misslyckades | Obligatorisk |
| Fel | En beskrivning av felet som har inträffat, om några. Om anslutningen lyckades utelämnar klienterna det här fältet. Den maximala längden för det här fältet är 50 tecken. | Krävs för fel, utelämnad i annat fall |

Fel beskrivningen får innehålla högst 50 tecken och bör helst vara ett av de värden som anges i följande tabell. Om fel villkoret inte matchar något av dessa värden kan klienterna använda en kortfattad beskrivning av fel tillståndet med [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) utan blank steg. Möjligheten att skicka ett *telemetri* -meddelande kräver en anslutning till tjänsten, så endast tillfälliga eller tillfälliga fel villkor kan rapporteras i *telemetri* meddelandet. Fel villkor som *permanent* blockerar en klient från att upprätta en anslutning till tjänsten hindrar klienten från att skicka meddelanden till tjänsten, inklusive *telemetri* -meddelanden.

| Fel | Användning |
| ----- | ----- |
| DNSfailure | Klienten kunde inte ansluta till tjänsten på grund av ett DNS-problem i nätverks stacken. |
| NoNetwork | Klienten försökte upprätta en anslutning, men nätverks stacken rapporterade att inget fysiskt nätverk var tillgängligt. |
| Noauthorization | Klient anslutningen misslyckades vid försök att hämta en autentiseringstoken för anslutningen. |
| Inga resurser | Klienten fick slut på en lokal resurs (till exempel minne) när den försökte upprätta en anslutning. |
| Förbjudna | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade en HTTP-statuskod i `403 Forbidden` begäran om WebSocket-uppgraderingen. |
| Behörighet saknas | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade en HTTP-statuskod i `401 Unauthorized` begäran om WebSocket-uppgraderingen. |
| BadRequest | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade en HTTP-statuskod i `400 Bad Request` begäran om WebSocket-uppgraderingen. |
| ServerUnavailable | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade en HTTP-statuskod i `503 Server Unavailable` begäran om WebSocket-uppgraderingen. |
| ServerError | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade en `HTTP 500` intern fel status kod i begäran om WebSocket-uppgraderingen. |
| Timeout | Klientens anslutningsbegäran nådde sin tids gräns utan svar från tjänsten. *End* -fältet innehåller den tidpunkt då klienten nådde tids gränsen och slutade vänta på anslutningen. |
| ClientError | Klienten avslutade anslutningen på grund av ett internt klient fel. |

### <a name="metric-microphone"></a>Kostnads`Microphone`

`Microphone` Måttet krävs för alla tal. Det här måttet mäter tiden på klienten där ljud indata används aktivt för en tal förfrågan.

Använd följande exempel som rikt linjer för att registrera *Start* tids värden för `Microphone` måttet i klient programmet:

* Ett klient program kräver att en användare måste trycka på en fysisk knapp för att starta mikrofonen. När du trycker på knappen läser klient programmet InInformationen från mikrofonen och skickar den till tal tjänsten. Startvärdet för `Microphone` måttet registrerar tiden efter att knappen skickades när mikrofonen initieras och är redo att tillhandahålla inmatade *värden.* Slutvärdet för `Microphone` måttet *registrerar den tidpunkt* då klient programmet slutade strömma ljud till tjänsten `speech.endDetected` när meddelandet togs emot från tjänsten.

* Ett klient program använder en nyckelords spotter som är "Always"-lyssning. Endast efter att nyckelordet spotter identifierar en talade utlösnings fras samlar klient programmet in inmatarna från mikrofonen och skickar dem till tal tjänsten. *Startvärdet* för `Microphone` måttet registrerar den tidpunkt då nyckelordet spotter meddelat att klienten börjar använda inmatad från mikrofonen. Slutvärdet för `Microphone` måttet *registrerar den tidpunkt* då klient programmet slutade strömma ljud till tjänsten `speech.endDetected` när meddelandet togs emot från tjänsten.

* Ett klient program har åtkomst till en konstant ljud ström och utför tystnads-/tal identifiering på den ljud strömmen i en *modul för tal identifiering*. *Startvärdet* för `Microphone` måttet registrerar tiden då modulen för *tal identifiering* informerade klienten om att börja använda indata från ljud strömmen. Slutvärdet för `Microphone` måttet *registrerar den tidpunkt* då klient programmet slutade strömma ljud till tjänsten `speech.endDetected` när meddelandet togs emot från tjänsten.

* Ett klient program bearbetar den andra turn-begäran och informeras om ett tjänst svars meddelande för att slå på mikrofonen för att samla in indata för den andra turn-processen. *Startvärdet* för `Microphone` måttet registrerar den tidpunkt då klient programmet aktiverar mikrofonen och börjar använda indata från den ljud källan. Slutvärdet för `Microphone` måttet *registrerar den tidpunkt* då klient programmet slutade strömma ljud till tjänsten `speech.endDetected` när meddelandet togs emot från tjänsten.

Värdet för *slutdatumet* för `Microphone` måttet registrerar den tidpunkt då klient programmet slutade strömmad ljud inspelning. I de flesta fall inträffar den här händelsen strax efter att klienten har `speech.endDetected` tagit emot meddelandet från tjänsten. Klient program kan verifiera att de överensstämmer med protokollet genom att se till att *slut* tid svärdet för `Microphone` måttet inträffar senare än värdet för mottagnings `speech.endDetected` tid för meddelandet. Och eftersom det vanligt vis är en fördröjning mellan slutet av en turn och starten av en annan Turn, kan klienterna verifiera protokollets överensstämmelse genom att se till att *Start* tiden `Microphone` för måttet för efterföljande inaktive ras korrekt registrerar tiden då klienten *startade* med mikrofonen för att strömma ljud indata till tjänsten.

| Fält | Beskrivning | Användning |
| ----- | ----------- | ----- |
| Name | Mikrofon | Obligatorisk |
| Start | Den tidpunkt då klienten började använda ljud indata från mikrofonen eller andra ljud strömmar eller tog emot en utlösare från nyckelordet spotter | Obligatorisk |
| slutpunkt | Tiden då klienten slutade använda mikrofonen eller ljud strömmen | Obligatorisk |
| Fel | En beskrivning av felet som har inträffat, om några. Om mikrofon åtgärderna lyckades utelämnar klienterna det här fältet. Den maximala längden för det här fältet är 50 tecken. | Krävs för fel, utelämnad i annat fall |

### <a name="metric-listeningtrigger"></a>Kostnads`ListeningTrigger`
`ListeningTrigger` Måttet mäter den tidpunkt då användaren utför den åtgärd som initierar tal indata. `ListeningTrigger` Måttet är valfritt, men klienter som kan tillhandahålla detta mått rekommenderas att göra det.

Använd följande exempel som rikt linjer för inspelning av `ListeningTrigger` värden för *Start* -och *slut* tid för måttet i klient programmet.

* Ett klient program kräver att en användare måste trycka på en fysisk knapp för att starta mikrofonen. *Startvärdet* för det här måttet registrerar tiden för knappen push. *End* -värdet registrerar tiden när knappen push är klar.

* Ett klient program använder en nyckelords spotter som är "Always"-lyssning. När nyckelordet spotter identifierar en talade utlösnings fras läser klient programmet in inmatarna från mikrofonen och skickar dem till tal tjänsten. *Startvärdet* för det här måttet registrerar den tidpunkt då nyckelordet spotter tog emot ljud som sedan identifierades som utlösnings frasen. *End* -värdet registrerar tiden då det sista ordet i utlösnings frasen talade av användaren.

* Ett klient program har åtkomst till en konstant ljud ström och utför tystnads-/tal identifiering på den ljud strömmen i en *modul för tal identifiering*. Startvärdet för det här måttet registrerar den tid som *modulen för tal igenkänning* tog emot ljud som sedan identifierats som tal. *End* -värdet registrerar tidpunkten då tal igenkännings *modulen* identifierade tal.

* Ett klient program bearbetar den andra turn-begäran och informeras om ett tjänst svars meddelande för att slå på mikrofonen för att samla in indata för den andra turn-processen. Klient programmet bör *inte* innehålla något `ListeningTrigger` mått för den här funktionen.

| Fält | Beskrivning | Användning |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | Valfritt |
| Start | Den tidpunkt då klient avlyssnings utlösaren startade | Obligatorisk |
| slutpunkt | Tiden då klient avlyssnings utlösaren avslutades | Obligatorisk |
| Fel | En beskrivning av felet som har inträffat, om några. Om utlösarens åtgärd lyckades utelämnar klienterna det här fältet. Den maximala längden för det här fältet är 50 tecken. | Krävs för fel, utelämnad i annat fall |

#### <a name="sample-message"></a>Exempelmeddelande

Följande exempel visar ett telemetri-meddelande med både ReceivedMessages och mått delar:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Felhantering

I det här avsnittet beskrivs de typer av fel meddelanden och villkor som programmet behöver hantera.

### <a name="http-status-codes"></a>HTTP-statuskoder

Under WebSocket-uppgraderingen kan tal tjänsten returnera någon av standard status koderna för http, till exempel `400 Bad Request`, osv. Programmet måste hantera dessa fel villkor korrekt.

#### <a name="authorization-errors"></a>Auktoriseringsfel

Om du har angett ett felaktigt tillstånd under WebSocket-uppgraderingen, returnerar tal `403 Forbidden` service en HTTP-statuskod. Bland de villkor som kan utlösa den här felkoden är:

* *Authorization* -huvud saknas

* Ogiltig autentiseringstoken

* Token för förfallen auktorisering

`403 Forbidden` Fel meddelandet indikerar inte ett problem med tal tjänsten. Det här fel meddelandet indikerar ett problem med klient programmet.

### <a name="protocol-violation-errors"></a>Fel vid protokoll fel

Om Speech service identifierar eventuella protokoll överträdelser från en klient, avslutar tjänsten WebSocket-anslutningen när den har returnerat en *status kod* och *orsak* för uppsägningen. Klient program kan använda den här informationen för att felsöka och åtgärda överträdelser.

#### <a name="incorrect-message-format"></a>Felaktigt meddelande format

Om en klient skickar ett text-eller binärt meddelande till tjänsten som inte är kodad i rätt format som anges i den här specifikationen, stänger tjänsten anslutningen med en 1007 ogiltig status kod för *nytto Last data* .

Tjänsten returnerar denna status kod av olika orsaker, som du ser i följande exempel:

* "Felaktigt meddelande format. Det binära meddelandet har ett ogiltigt prefix för header-storlek. " Klienten skickade ett binärt meddelande som har ett ogiltigt prefix för sidhuvud storlek.

* "Felaktigt meddelande format. Det binära meddelandet har en ogiltig rubrik storlek. " Klienten skickade ett binärt meddelande som angav en ogiltig huvud storlek.

* "Felaktigt meddelande format. Det gick inte att avkoda binära meddelandehuvuden i UTF-8. " Klienten skickade ett binärt meddelande som innehåller huvuden som inte kodades korrekt i UTF-8.

* "Felaktigt meddelande format. Textmeddelandet innehåller inga data. " Klienten skickade ett textmeddelande som inte innehåller några bröd text data.

* "Felaktigt meddelande format. Det gick inte att avkoda textmeddelanden i UTF-8. " Klienten skickade ett SMS-meddelande som inte kodades korrekt i UTF-8.

* "Felaktigt meddelande format. Textmeddelandet innehåller ingen huvud avgränsare. " Klienten skickade ett textmeddelande som inte innehöll någon huvud avgränsare eller använde fel huvud avgränsare.

#### <a name="missing-or-empty-headers"></a>Huvuden som saknas eller är tomma

Om en klient skickar ett meddelande som inte har de begärda huvudena *X-RequestId* eller- *sökvägen*, stängs anslutningen med en status kod för *1002-protokoll fel* . Meddelandet är sidhuvudet saknas/tomt. {Rubrik namn}. "

#### <a name="requestid-values"></a>RequestId-värden

Om en klient skickar ett meddelande som anger ett *X-RequestId* -huvud med ett felaktigt format stänger tjänsten anslutningen och returnerar en *1002 protokoll fel* status. Meddelandet är "Ogiltig begäran". X-RequestId-huvud värde angavs inte i UUID-format utan streck. "

#### <a name="audio-encoding-errors"></a>Ljud kodnings fel

Om en klient skickar ett ljud segment som initierar en turn och ljud formatet eller kodningen inte överensstämmer med den specifikation som krävs stänger tjänsten anslutningen och returnerar en 1007 ogiltig status kod för *nytto Last data* . Meddelandet anger formatet kodnings fel källa.

#### <a name="requestid-reuse"></a>Återanvänd RequestId

Om en klient skickar ett meddelande som återanvänder sig av den här funktionen, stängs anslutningen och returnerar en status kod för *1002-protokoll fel* när en aktive ras. Meddelandet är "Ogiltig begäran". Åter användning av begär ande identifierare tillåts inte. "

## <a name="connection-failure-telemetry"></a>Telemetri om anslutnings problem

För att säkerställa bästa möjliga användar upplevelse måste klienterna informera tal tjänsten om tidsstämplar för viktiga kontroll punkter i en anslutning med hjälp av *telemetri* -meddelandet. Det är lika viktigt att klienterna meddelar tjänsten om anslutningar som försökts, men som misslyckats.

För varje anslutnings försök som inte kunde skapas skapar klienter ett *telemetri* med ett unikt *X-RequestId-* huvud-värde. Eftersom klienten inte kunde upprätta en anslutning kan fältet *ReceivedMessages* i JSON-innehållet utelämnas. Endast posten i fältet mått är inkluderad. `Connection` Posten innehåller både start-och slut tid och det fel tillstånd som påträffades.

### <a name="connection-retries-in-telemetry"></a>Anslutnings försök i telemetri

Klienterna bör särskilja *försök* från *flera anslutnings försök* vid den händelse som utlöser anslutnings försöket. Anslutnings försök som utförs program mässigt utan att några användarindata är nya försök. Flera anslutnings försök som utförs som svar på användarindata är flera anslutnings försök. Klienter ger varje användare utlöst anslutning ett unikt meddelande om *X-RequestId* och *telemetri* . Klienterna återanvänder *X-RequestId* för nya program mässiga försök. Om flera återförsök har gjorts för ett enda anslutnings försök inkluderas varje nytt försök som en `Connection` post i *telemetri* meddelandet.

Anta till exempel att en användare pratar med nyckelords utlösaren för att starta en anslutning och det första anslutnings försöket Miss lyckas på grund av DNS-fel. Ett andra försök som görs via programmering av klienten lyckas dock. Eftersom klienten försökte ansluta på nytt utan ytterligare indata från användaren, använder klienten ett enda *telemetri* -meddelande med flera `Connection` poster för att beskriva anslutningen.

Anta till exempel att en användare pratar med nyckelords utlösaren för att starta en anslutning och det här anslutnings försöket Miss lyckas efter tre återförsök. Klienten ger sedan upp, stoppar försök att ansluta till tjänsten och informerar användaren om att något gick fel. Användaren pratar sedan om nyckelords utlösaren igen. Den här gången antar vi att klienten ansluter till tjänsten. Efter anslutningen skickar klienten omedelbart ett *telemetri* -meddelande till tjänsten som innehåller tre `Connection` poster som beskriver anslutnings felen. När `turn.end` meddelandet har tagits emot skickar klienten ett annat *telemetri* som beskriver den lyckade anslutningen.

## <a name="error-message-reference"></a>Fel meddelande referens

### <a name="http-status-codes"></a>HTTP-statuskoder

| HTTP-statuskod | Beskrivning | Felsökning |
| - | - | - |
| 400 Felaktig begäran | Klienten skickade en begäran om WebSocket-anslutning som var felaktig. | Kontrol lera att du har angett alla obligatoriska parametrar och HTTP-huvuden och att värdena är korrekta. |
| 401 obehörig | Klienten inkluderade inte nödvändig autentiseringsinformation. | Kontrol lera att du skickar ditt *Authorization* -huvud i WebSocket-anslutningen. |
| 403 förbud | Klienten skickade auktoriseringsinformation, men var ogiltig. | Kontrol lera att du inte skickar ett inaktuellt eller ogiltigt värde i *Authorization* -huvudet. |
| 404 Kunde ej hittas | Klienten försökte få åtkomst till en URL-sökväg som inte stöds. | Kontrol lera att du använder rätt URL för WebSocket-anslutningen. |
| 500-server fel | Tjänsten påträffade ett internt fel och kunde inte uppfylla begäran. | I de flesta fall är det här felet tillfälligt. Gör om begäran. |
| 503 Tjänsten är inte tillgänglig | Tjänsten var inte tillgänglig för att hantera begäran. | I de flesta fall är det här felet tillfälligt. Gör om begäran. |

### <a name="websocket-error-codes"></a>WebSocket-felkoder

| WebSocketsStatus-kod | Beskrivning | Felsökning |
| - | - | - |
| 1000 normal avslutning | Tjänsten stängde WebSocket-anslutningen utan ett fel. | Om WebSocket-stängningen var oväntad läser du dokumentationen för att säkerställa att du förstår hur och när tjänsten kan avsluta WebSocket-anslutningen. |
| 1002-protokoll fel | Klienten kunde inte uppfylla protokoll kraven. | Se till att du förstår protokoll dokumentationen och är tydliga om kraven. Läs den tidigare dokumentationen om fel orsaker för att se om du bryter mot protokoll kraven. |
| 1007 ogiltiga nytto Last data | Klienten skickade en ogiltig nytto Last i ett protokoll meddelande. | Kontrol lera det senaste meddelandet som du skickade till tjänsten för fel. Läs den tidigare dokumentationen om nytto Last fel. |
| 1011-Server fel | Tjänsten påträffade ett internt fel och kunde inte uppfylla begäran. | I de flesta fall är det här felet tillfälligt. Gör om begäran. |

## <a name="related-topics"></a>Relaterade ämnen

Se ett [Java Script SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) som är en implementering av det WebSocket-baserade Speech service-protokollet.
