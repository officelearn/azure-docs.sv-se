---
title: Microsoft taligenkänning WebSocket-protokollet | Microsoft Docs
description: Dokumentationen för protokollet för tal tjänsten baserat på WebSockets
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 17954536e8bdb49c09204c2e522586b79cb1bef5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352317"
---
# <a name="speech-service-websocket-protocol"></a>Tal Service WebSocket-protokollet

  Tal Service är en molnbaserad plattform som innehåller de mest avancerade algoritmerna för konvertering tal till text. Tal Service-protokollet definierar den [anslutningsinställningar](#connection-establishment) mellan program och tjänsten och tal recognition meddelanden som utbyts mellan motsvarigheter ([klienten kommer meddelanden](#client-originated-messages)och [service-genererade meddelanden](#service-originated-messages)). Dessutom [telemetri meddelanden](#telemetry-schema) och [felhantering](#error-handling) beskrivs.

## <a name="connection-establishment"></a>Anslutningsupprättande

Protokollet tal tjänsten följer specifikationen WebSocket standard [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). En WebSocket-anslutning startar som en HTTP-begäran som innehåller HTTP-huvuden som anger klientens önskan att uppgradera anslutningen till en WebSocket istället för att använda HTTP-semantik. Servern anger dess vilja att delta i WebSocket-anslutningen genom att returnera ett HTTP `101 Switching Protocols` svar. Efter utbyte av den här handskakningen både klient- och fortsätta socket och börja använda ett message-baserat protokoll skicka och ta emot information.

Om du vill börja WebSocket-handskakningen skickar klientprogrammet en HTTPS-GET-begäran till tjänsten. Den omfattar standard uppgradera WebSocket-huvuden tillsammans med andra huvuden som är specifika för tal.

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

Alla tal du vill använda den [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) kryptering. Använd okrypterad tal begäranden stöds inte. Följande TLS version stöds:

* TLS 1.2

### <a name="connection-identifier"></a>Anslutningsidentifierare

Dikterings-tjänsten kräver att alla klienter innehåller ett unikt ID för att identifiera anslutningen. Klienter *måste* inkluderar den *X ConnectionId* huvud när de startar en WebSocket-handskakning. Den *X ConnectionId* -huvudet måste vara en [unique identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID) värdet. Uppgradera WebSocket-begäranden som inte innehåller den *X ConnectionId*, inte ange ett värde för den *X-ID* sidhuvud, eller innehåller inte en giltig UUID värdet avvisas av tjänsten med en HTTP `400 Bad Request` svar.

### <a name="authorization"></a>Auktorisering

Utöver standard WebSocket handskakning huvuden tal begäranden kräver en *auktorisering* huvud. Anslutningsbegäranden utan det här sidhuvudet avvisas av tjänsten med en HTTP `403 Forbidden` svar.

Den *auktorisering* huvudet måste innehålla en JSON-Webbtoken (JWT) åtkomst-token.

Information om hur du prenumerera på och hämta API-nycklar som används för att hämta giltiga JWT-åtkomsttoken finns i [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/) sidan.

API-nyckeln har skickats till tjänsten token. Exempel:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Informationen i följande huvudet måste anges för åtkomst-token.

| Namn | Format | Beskrivning |
|----|----|----|
| OCP-Apim-prenumeration-nyckel | ASCII | Din prenumerationsnyckel |

Tokentjänsten som returnerar JWT åtkomst-token som `text/plain`. Sedan av JWT skickas som en `Base64 access_token` till handskakningen som en *auktorisering* huvud med strängen prefixet `Bearer`. Exempel:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookies

Klienter *måste* stöder HTTP-cookies som anges i [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>HTTP-omdirigering

Klienter *måste* stöd för omdirigering av standard-mekanismer som anges av den [http-protokollspecifikationen](http://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Dikterings-slutpunkter

Klienter *måste* använder en lämplig slutpunkt för tal-tjänsten. Slutpunkten är baserad på recognition läge och språk. Tabellen visar några exempel.

| Läge | Sökväg | Tjänstens URI |
| -----|-----|-----|
| Interaktiv | /Speech/Recognition/Interactive/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Konversation | /Speech/Recognition/Conversation/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Dictation | /Speech/Recognition/Dictation/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Mer information finns i [tjänst-URI för](../GetStarted/GetStartedREST.md#service-uri) sidan.

### <a name="report-connection-problems"></a>Rapporten anslutningsproblem

Klienter bör omedelbart rapportera alla problem som uppstod när en anslutning upprättas. Meddelandeprotokollet för reporting misslyckade anslutningar beskrivs i [anslutning fel telemetri](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Anslutningen varaktighet begränsningar

Jämfört med vanliga web service HTTP-anslutningar för senaste WebSocket-anslutningar en *lång* tid. Tal Service placeras begränsningar för WebSocket-anslutningar till tjänsten varaktighet:

 * Maximal varaktighet för en aktiv WebSocket-anslutning är 10 minuter. En anslutning är aktiv om tjänsten eller klienten skickar WebSocket-meddelanden över den anslutningen. Tjänsten avslutas anslutningen utan varning när gränsen har nåtts. Klienter ska utveckla användarscenarier som inte kräver anslutning ska vara aktiv eller nära maximal livslängd.

 * Maximal varaktighet för en inaktiv WebSocket-anslutning är 180 sekunder. En anslutning är inaktiv om varken tjänsten eller klienten skickade ett WebSocket-meddelande via anslutningen. När den maximala inaktiva livstiden har nåtts avslutas tjänsten inaktiva WebSocket-anslutningen.

## <a name="message-types"></a>Meddelandetyper

När en WebSocket-anslutning har upprättats mellan klienten och tjänsten är kan både klienten och tjänsten skicka meddelanden. Det här avsnittet beskriver formatet för dessa WebSocket-meddelanden.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) anger att WebSocket-meddelanden kan överföra data med hjälp av en text eller binär kodning. Två kodningar använder olika under överföring format. Varje format är optimerad för kodning, överföring och avkodning av nyttolast meddelande.

### <a name="text-websocket-messages"></a>WebSocket textmeddelanden

WebSocket textmeddelanden utföra en nyttolast textinformation som består av en del av huvuden och en brödtext avgränsade med välbekanta double vagnretur ny rad-par som används för HTTP-meddelanden. Och som HTTP-meddelanden WebSocket textmeddelanden anger huvuden i *namn: värde* format avgränsade med ett enda vagnretur ny rad-par. Den text som ingår i ett textmeddelande för WebSocket *måste* använder [UTF-8](https://tools.ietf.org/html/rfc3629) kodning.

WebSocket textmeddelanden måste ange ett meddelande i huvudet *sökvägen*. Värdet för det här sidhuvudet måste vara ett tal protokollet meddelandetyper definieras senare i det här dokumentet.

### <a name="binary-websocket-messages"></a>Binär WebSocket-meddelanden

Binär WebSocket meddelanden utföra en binär nyttolast. I tal Service-protokollet, ljud skickas till och tas emot från tjänsten med binär WebSocket-meddelanden. Alla andra meddelanden är WebSocket textmeddelanden. 

Binär WebSocket-meddelanden består av en rubrik och en brödtext som WebSocket textmeddelanden. Första 2 byte binära WebSocket-meddelandet ange i [big endian](https://en.wikipedia.org/wiki/Endianness) ordning 16-bitars heltal storlek i sidhuvudet. Avsnittet är minsta huvud 0 byte. Den maximala storleken är 8 192 byte. Texten i den binära WebSocket meddelanden *måste* använder [US-ASCII](https://tools.ietf.org/html/rfc20) kodning.

Huvuden i meddelandet binära WebSocket kodas på samma sätt som WebSocket textmeddelanden. Den *namn: värde* format avgränsas med ett enda vagnretur ny rad-par. Binär WebSocket-meddelanden måste ange ett meddelande i huvudet *sökvägen*. Värdet för det här sidhuvudet måste vara ett tal protokollet meddelandetyper definieras senare i det här dokumentet.

Både text och binära WebSocket-meddelanden används i tal Service-protokollet. 

## <a name="client-originated-messages"></a>Klienten kommer meddelanden

När anslutningen har upprättats kan både klienten och tjänsten börja skicka meddelanden. Det här avsnittet beskriver format och nyttolasten för meddelanden som klientprogram skickar till tal-tjänsten. Avsnittet [Service-genererade meddelanden](#service-originated-messages) visar de meddelanden som har sitt ursprung i tal Service och skickas till klientprogrammen.

Huvudsakliga meddelanden skickas från klienten till tjänsterna som är `speech.config`, `audio`, och `telemetry` meddelanden. Innan vi anser att varje meddelande i detalj krävs vanliga beskrivs meddelandehuvuden för alla dessa meddelanden.

### <a name="required-message-headers"></a>Nödvändiga meddelandehuvuden

Följande huvuden krävs för alla klient-genererade meddelanden.

| Sidhuvud | Värde |
|----|----|
| Sökväg | Sökvägen till meddelandet som anges i det här dokumentet |
| X-RequestId | UUID formatet ”Nej dash” |
| X-tidsstämpel | Klienten UTC klockan tidsstämpeln i ISO 8601-format |

#### <a name="x-requestid-header"></a>Huvudet X-RequestId

Klient-genererade begäranden identifieras unikt med den *X RequestId* meddelandehuvudet. Detta huvud måste anges för alla klient-genererade meddelanden. Den *X RequestId* huvudets värde måste vara en UUID i form av ”Nej dash”, till exempel *123e4567e89b12d3a456426655440000*. Den *kan* i kanonisk form *123e4567-e89b-12d3-a456-426655440000*. Begär utan en *X RequestId* sidhuvud eller med ett värde från ett huvud med fel format för UUID: er orsak tjänsten för att avsluta WebSocket-anslutningen.

#### <a name="x-timestamp-header"></a>Huvudet X-tidsstämpel

Varje meddelande som skickas till tal-tjänsten genom att ett klientprogram *måste* inkluderar en *X tidsstämpel* huvud. Värdet för det här sidhuvudet är den tid när klienten skickar meddelandet. Begär utan en *X tidsstämpel* sidhuvud eller med ett värde från ett huvud med fel format gör att tjänsten för att avsluta WebSocket-anslutningen.

Den *X tidsstämpel* huvudets värde måste vara i formatet ”åååå'-'MM'-'dd'T' HH': 'mm':'ss '.' fffffffZ' där 'fffffff' är en del av en sekund. Till exempel innebär '12,5' '12 + 5/10 sekunder och '12.526' innebär ”12 plus 526/1000 sekunder'. Formatet följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och till skillnad från vanliga HTTP- *datum* sidhuvud, den kan ge millisekunder lösning. Klientprogram kan avrunda tidsstämplar till närmaste millisekunder. Klientprogram måste du se till att enheten klockan korrekt spårar tid med hjälp av en [Network Time Protocol (NTP) server](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>meddelande `speech.config`

Tal tjänsten behöver veta egenskaper för programmet att ge bästa möjliga taligenkänning. Obligatoriska egenskaper data innehåller information om enheten och Operativsystemet som används av ditt program. Du kan ange den här informationen i den `speech.config` meddelande.

Klienter *måste* skicka en `speech.config` visas direkt när de upprättar anslutningen till tal-tjänsten och innan de skickar någon `audio` meddelanden. Du måste skicka en `speech.config` visas bara en gång per anslutning.

| Fält | Beskrivning |
|----|----|
| Kodning av WebSocket-meddelande | Text |
| Innehåll | Nyttolasten som en JSON-struktur |

#### <a name="required-message-headers"></a>Nödvändiga meddelandehuvuden

| Huvudets namn | Värde |
|----|----|
| Sökväg | `speech.config` |
| X-tidsstämpel | Klienten UTC klockan tidsstämpeln i ISO 8601-format |
| Innehållstyp | Application/json; charset = utf-8 |

Precis som med alla klient-genererade meddelanden i tal Service-protokollet den `speech.config` meddelandet *måste* inkluderar en *X tidsstämpel* huvud som registrerar den UTC klockan klienttid när meddelandet har skickats till tjänsten. Den `speech.config` meddelandet *inte* kräver en *X RequestId* huvudet eftersom det här meddelandet är inte kopplad till en viss tal-begäran.

#### <a name="message-payload"></a>Storleken på meddelandets innehåll
Nyttolasten för den `speech.config` meddelandet är en JSON-struktur som innehåller information om programmet. I följande exempel visas den här informationen. Klient- och sammanhangsinformation ingår i den *kontexten* element i JSON-strukturen. 

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

##### <a name="system-element"></a>Systemelement

Elementet system.version i den `speech.config` meddelande innehåller versionen av tal SDK-programvara som används av klientprogram eller enhet. Värdet är formatet *major.minor.build.branch*. Du kan hoppa över den *gren* komponenten om den inte är tillämplig.

##### <a name="os-element"></a>OS-element

| Fält | Beskrivning | Användning |
|-|-|-|
| OS.Platform | Den OS-plattformen som är värd för programmet, till exempel Windows, Android, iOS eller Linux |Krävs |
| OS.Name | OS-produktnamn, till exempel Debian eller Windows 10 | Krävs |
| OS.version | Vilken version av Operativsystemet i formuläret *major.minor.build.branch* | Krävs |

##### <a name="device-element"></a>Enheten element

| Fält | Beskrivning | Användning |
|-|-|-|
| Device.Manufacturer | Enhetstillverkare för maskinvara | Krävs |
| Device.Model | Enhetsmodellen | Krävs |
| Device.version | Enheten programvaruversion som tillhandahålls av tillverkaren av enheten. Det här värdet anger en version av den enhet som kan spåras av tillverkaren. | Krävs |

### <a name="message-audio"></a>meddelande `audio`

Dikterings-aktiverade program skicka ljud till tal-tjänsten genom att konvertera ljudströmmen till en serie av ljud segment. Varje segment av ljud innebär ett segment i tal som ska vara kopierats av tjänsten. Den maximala storleken för ett enda ljud segment är 8 192 byte. Ljudström meddelanden är *binära WebSocket meddelanden*.

Klienter använder den `audio` meddelandet för att skicka ett ljud segmentet till tjänsten. Klienter Läs ljud från mikrofon i segment och skicka dessa segment till tal-tjänsten för skrivfel. Först `audio` meddelandet måste innehålla ett giltigt huvud som korrekt anger ljuduppspelningen uppfyller något av kodningsformat som stöds av tjänsten. Ytterligare `audio` meddelanden innehåller endast binära ljuduppspelningen strömma data läses från mikrofonen.

Klienter kan du skicka ett `audio` meddelande med längden noll brödtext. Detta meddelande om den tjänst som klienten vet att användaren stoppats tala, utterance är klar och mikrofonen är avstängd.

Dikterings-tjänsten använder först `audio` meddelande som innehåller ett unikt begäran-ID och signalerar till början av en ny cykel för frågor och svar eller *aktivera*. När tjänsten har tagit emot en `audio` meddelande med en ny begäran identifierare, ignoreras alla köade eller ej skickade meddelanden som är associerade med alla tidigare Stäng.

| Fält | Beskrivning |
|-------------|----------------|
| Kodning av WebSocket-meddelande | Binär |
| Innehåll | Binära data för ljud segmentet. Maximal storlek är 8 192 byte. |

#### <a name="required-message-headers"></a>Nödvändiga meddelandehuvuden

Följande huvuden krävs för alla `audio` meddelanden.

| Sidhuvud         |  Värde     |
| ------------- | ---------------- |
| Sökväg | `audio` |
| X-RequestId | UUID formatet ”Nej dash” |
| X-tidsstämpel | Klienten UTC klockan tidsstämpeln i ISO 8601-format |
| Innehållstyp | Ljud innehållstyp. Typen måste vara antingen *ljud/x-wav* (PCM) eller *ljud/silke* (NATURSILKE). |

#### <a name="supported-audio-encodings"></a>Ljud teckenkodningar som stöds

Det här avsnittet beskrivs ljud codec som stöds av tal-tjänsten.

##### <a name="pcm"></a>PCM

Tal tjänsten accepterar okomprimerade pulse kod modulering (PCM) ljud. Ljud skickas till tjänsten i [WAV](https://en.wikipedia.org/wiki/WAV) filformat, så att det första ljudet dela in *måste* innehåller en giltig [Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF)-huvud. Om en klient initierar en Stäng med ett ljud segment som har *inte* innehåller ett giltigt RIFF-huvud, tjänsten att avslå förfrågan och avslutar WebSocket-anslutningen.

PCM ljud *måste* att sampla vid 16 kHz med 16 bitar per prov och en kanal (*riff-16khz-16-bitars-mono-pcm*). Tal tjänsten stöder inte stereo ljudströmmar och avvisar ljudströmmar som inte använder den angivna bithastighet, samplingsfrekvens eller antal kanaler.

##### <a name="opus"></a>Opus

Opus är öppen, royaltyfri, mycket flexibel ljud-codec. Tal tjänsten stöder Opus med en konstant bithastighet av `32000` eller `16000`. Endast den `OGG` behållare för Opus stöds för närvarande som anges av den `audio/ogg` MIME-typen.

Om du vill använda Opus ändra den [JavaScript exempel](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) och ändra den `RecognizerSetup` metod för att returnera.

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

Människor inte signalera explicit när de är klar tal. Alla program som accepterar tal som indata har två alternativ för hantering i slutet av tal i en ljudström: tjänsten slutet av tal identifiering och klienten slutet av tal identifiering. Med dessa två alternativ tillhandahåller tjänsten slutet av tal identifiering vanligtvis en bättre användarupplevelse.

##### <a name="service-end-of-speech-detection"></a>Identifiering av tjänsten slutet av tal

För att skapa perfekt handsfree tal experience tillåts program tjänsten för att identifiera när användaren är klar med tal. Klienter skickar ljud från dig som *ljud* blocken förrän tjänsten identifierar tystnad och svarar igen med en `speech.endDetected` meddelande.

##### <a name="client-end-of-speech-detection"></a>Klienten slutet av tal identifiering

Klientprogram som används att skicka en signal slutet av tal på något sätt också kan ge tjänsten som signal. Ett klientprogram kan till exempel ha en ”stopp” eller ”tyst” knapp som användaren kan trycka på. Om du vill skicka en signal slutet av tal, klientprogram skicka en *ljud* segment-meddelande med längden noll brödtext. Tal tjänsten tolkar meddelandet i slutet av inkommande ljudström.

### <a name="message-telemetry"></a>meddelande `telemetry`

Klientprogram *måste* bekräftar i slutet av varje aktivera genom att skicka telemetri om Aktivera till tal-tjänsten. Aktivera slutpunkt bekräftelse kan tal tjänsten så att alla meddelanden som är nödvändig för slutförandet av begäran och dess svar har tagits emot av klienten. Aktivera slutpunkt bekräftelse kan också tal-tjänsten för att kontrollera att klientprogrammen fungerar som förväntat. Den här informationen är ovärderlig om du behöver hjälp med att felsöka ditt tal-aktiverade program.

Klienter måste bekräfta slutet av en aktivera genom att skicka en `telemetry` meddelande strax efter ta emot en `turn.end` meddelande. Klienter använder för att bekräfta den `turn.end` så snart som möjligt. Om ett klientprogram inte kan bekräfta aktivera slutet, kan tjänsten tal avslutar anslutningen med ett fel. Klienter måste skicka ett `telemetry` meddelande för varje förfrågan och svar som identifieras av den *X RequestId* värde.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `telemetry` |
| X-tidsstämpel | Klienten UTC klockan tidsstämpeln i ISO 8601-format |
| Innehållstyp | `application/json` |
| Innehåll | En JSON-struktur som innehåller information om klienter om Aktivera |

Schemat för innehållet i den `telemetry` meddelande har definierats i den [telemetri schemat](#telemetry-schema) avsnitt.

#### <a name="telemetry-for-interrupted-connections"></a>Telemetri för avbryts anslutningar

Om nätverksanslutningen misslyckas av någon anledning under ett drag och klienten inte *inte* ta emot en `turn.end` meddelande från tjänsten, skickar klienten en `telemetry` meddelande. Detta meddelande beskriver den misslyckade begäranden nästa gång klienten ansluter till tjänsten. Klienterna behöver inte omedelbart försöka ansluta till skicka den `telemetry` meddelande. Meddelandet kan buffras på klienten och skickas via en framtida användaren begärde anslutning. Den `telemetry` meddelande för den misslyckade begäranden *måste* använder den *X RequestId* värdet från den misslyckade begäranden. Den kan skickas till tjänsten som en anslutning har upprättats, utan att skicka eller ta emot för andra meddelanden.

## <a name="service-originated-messages"></a>Tjänsten kommer meddelanden

Det här avsnittet beskrivs de meddelanden som har sitt ursprung i tal Service och skickas till klienten. Tal tjänsten upprätthåller ett register över klientfunktioner som och genererar meddelanden som krävs för varje klient så inte alla klienter får alla meddelanden som beskrivs här. Planeringsaspekter, meddelanden refererar till värdet för den *sökväg* huvud. Till exempel vi refererar till ett WebSocket-SMS med den *sökväg* värdet `speech.hypothesis` som ett speech.hypothesis-meddelande.

### <a name="message-speechstartdetected"></a>meddelande `speech.startDetected`

Den `speech.startDetected` meddelandet anger att tal tjänsten påträffas tal i ljudströmmen.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `speech.startDetected` |
| Innehållstyp | Application/json; charset = utf-8 |
| Innehåll | JSON-struktur som innehåller information om villkor när upptäcktes i början av tal. Den *Offset* fält i den här strukturen anger förskjutningen (i 100 nanosekunder enheter) när tal påträffades i ljudström i förhållande till början av dataströmmen. |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>meddelande `speech.hypothesis`

Taligenkänning genererar tal Service regelbundet hypoteser om orden tjänsten kändes igen. Tal tjänsten skickar dessa hypoteser till klienten ungefär var 300 millisekunder. Den `speech.hypothesis` passar *endast* för att förbättra användarupplevelsen för tal. I dessa meddelanden måste du inte utföra eventuella beroenden av innehåll eller korrektheten i texten.

 Den `speech.hypothesis` meddelandet gäller för de klienter som har vissa text återgivning kapaciteten och vill ge nära realtid feedback för pågående recognition till den person som tal.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `speech.hypothesis` |
| X-RequestId | UUID formatet ”Nej dash” |
| Innehållstyp | application/json |
| Innehåll | Tal hypotesen JSON-strukturen |

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

Den *Offset* element anger förskjutningen (i 100 nanosekunder enheter) när frasen identifierades i förhållande till början av ljudströmmen.

Den *varaktighet* element varaktighet (i 100 nanosekunder enheter) för frasen tal.

Klienter måste inte göra några antaganden om frekvens, tidsinställning eller texten i en hypotes tal eller text i alla två tal hypoteser konsekvens. Hypoteser är bara ögonblicksbilder i processen skrivfel i tjänsten. De representerar inte en stabil anhopning av skrivfel. Till exempel en första tal hypotes kan innehålla orden ”bra roliga” och andra hypotesen kan innehålla orden ”hitta roliga”. Dikterings-tjänsten efterbearbetning inte eventuella (till exempel versaler, skiljetecken) på texten i hypotesen tal.

### <a name="message-speechphrase"></a>meddelande `speech.phrase`

När tal tjänsten anger att det finns tillräckligt med information för att skapa en recognition resultatet som inte ändras tjänsten ger en `speech.phrase` meddelande. Tal Service producerar resultaten efter att användaren är klar med en fras eller ett par meningar.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `speech.phrase` |
| Innehållstyp | application/json |
| Innehåll | Tal frasen JSON-strukturen |

JSON-schema tal frasen omfattar följande fält: `RecognitionStatus`, `DisplayText`, `Offset`, och `Duration`. Mer information om dessa fält finns [skrivfel svar](../concepts.md#transcription-responses).

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

### <a name="message-speechenddetected"></a>meddelande `speech.endDetected`

Den `speech.endDetected` meddelandet anger att klientprogrammet ska avbrytas direktuppspelning av ljud till tjänsten.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `speech.endDetected` |
| Innehåll | JSON-struktur som innehåller förskjutningen när slutet av tal upptäcktes. Förskjutningen representeras i 100 nanosekunder enheter förskjutning från början av ljud som används för godkännande. |
| Innehållstyp | Application/json; charset = utf-8 |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

Den *Offset* element anger förskjutningen (i 100 nanosekunder enheter) när frasen identifierades i förhållande till början av ljudströmmen.

### <a name="message-turnstart"></a>meddelande `turn.start`

Den `turn.start` följs av en Stäng för tjänsten. Den `turn.start` meddelandet är alltid den första svar du får för varje begäran. Om du inte får en `turn.start` visas, förutsätter att status för tjänst-anslutningen är ogiltig.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `turn.start` |
| Innehållstyp | Application/json; charset = utf-8 |
| Innehåll | JSON-strukturen |

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

Innehållet i den `turn.start` meddelandet är en JSON-struktur som innehåller kontext för start av aktivera. Den *kontexten* elementet innehåller ett *serviceTag* egenskapen. Den här egenskapen anger ett Taggvärde som tjänsten är associerad med aktivera. Det här värdet kan användas av Microsoft om du behöver hjälp med felsökning av fel i programmet.

### <a name="message-turnend"></a>meddelande `turn.end`

Den `turn.end` signalerar till slutet av en Stäng för tjänsten. Den `turn.end` meddelandet är alltid den senaste svar för varje begäran. Klienter kan använda mottagaren av meddelandet som en signal för rensningsaktiviteter och övergår i ett inaktivt tillstånd. Om du inte får en `turn.end` visas, förutsätter att status för tjänst-anslutningen är ogiltig. I sådana fall stänger du den befintliga anslutningen till tjänsten och återansluta.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `turn.end` |
| Innehåll | Ingen |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Telemetri schema

Innehållet i den *telemetri* meddelandet är en JSON-struktur som innehåller information om klienter om en Stäng eller ett anslutningsförsök. Strukturen består av klienten tidsstämplar som registrerar när klienthändelser inträffar. Varje tidsstämpel måste vara i formatet ISO 8601 enligt beskrivningen i avsnittet ”X tidsstämpel huvudet”. *Telemetri* meddelanden som inte anger alla obligatoriska fält i JSON-strukturen eller som inte använder rätt Tidsstämpelformat kan leda till att tjänsten för att avsluta anslutningen till klienten. Klienter *måste* ange giltiga värden för alla obligatoriska fält. Klienter *bör* anger värden för valfria fält när så är lämpligt. Värdena som visas i prover i det här avsnittet är endast för jämförelseändamål.

Telemetri schemat är indelad i följande delar: tog emot meddelande tidsstämplar och mått. Formatera och användning av varje del anges i följande avsnitt.

### <a name="received-message-time-stamps"></a>Mottaget meddelande tidsstämplar

Klienter måste innehålla tid för inleverans värden för alla meddelanden som de får när du har anslutit till tjänsten. Dessa värden måste registrera tid när klienten *emot* varje meddelande från nätverket. Värdet ska registreras inte något annat tillfälle. Klienten bör till exempel inte registrera tid när det *fattat* i meddelandet. De mottagna meddelandet tidsstämplarna har angetts i en matris med *namn: värde* par. Anger namnet på paret den *sökväg* värdet för meddelandet. Värdet för paret anger tid för klient när meddelandet mottogs. Eller, om mer än ett meddelande av de angivna togs emot värdet för paret är en matris med tidsstämplar som anger när dessa meddelanden togs emot.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Klienter *måste* bekräfta mottagandet av alla meddelanden som skickas av tjänsten genom att inkludera tidsstämplar för dessa meddelanden i JSON-meddelandetext. Om en klient inte kan bekräfta mottagning av ett meddelande, kan tjänsten avslutar anslutningen.

### <a name="metrics"></a>Mått

Klienter måste innehålla information om händelser som inträffade under livslängden för en begäran. Har stöd för följande mått: `Connection`, `Microphone`, och `ListeningTrigger`.

### <a name="metric-connection"></a>Mått `Connection`

Den `Connection` mått anger information om anslutningsförsök av klienten. Måtten måste innehålla tidsstämplar när WebSocket-anslutningen har startats och avslutats. Den `Connection` mått krävs *endast för en anslutning först aktivera*. Efterföljande aktiverar krävs inte för att inkludera den här informationen. Om en klient gör flera anslutningsförsök innan en anslutning har upprättats, information om *alla* anslutningsförsöken ska tas med. Mer information finns i [anslutning fel telemetri](#connection-failure-telemetry).

| Fält | Beskrivning | Användning |
| ----- | ----------- | ----- |
| Namn | `Connection` | Krävs |
| Id | Värdet för identifieraren anslutningen som användes i den *X ConnectionId* huvud för den här begäran om anslutning | Krävs |
| Start | Den tid när klienten skickade anslutningsbegäran om | Krävs |
| Slut | Tid när klienten fått ett meddelande om att anslutningen har upprättats har eller i fel fall, avvisade vägrade eller misslyckades | Krävs |
| Fel | En beskrivning av felet som uppstod, om sådana finns. Om en anslutning har upprättats bör klienter ignorera det här fältet. Den maximala längden på det här fältet är 50 tecken. | Krävs för fall utelämnas annars fel |

Felbeskrivningen får innehålla högst 50 tecken och vi är en av de värden som anges i följande tabell. Om felet inte matchar ett av dessa värden, klienter kan använda en kortfattad beskrivning av felet med hjälp av [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) utan blanksteg. Möjlighet att skicka en *telemetri* meddelande kräver en anslutning till tjänsten, så bara tillfälligt eller tillfälliga fel rapporteras i den *telemetri* meddelande. Fel villkor som *permanent* blockera en klient från att upprätta en anslutning till tjänsten hindra klienten från att skicka ett meddelande till tjänsten, inklusive *telemetri* meddelanden.

| Fel | Användning |
| ----- | ----- |
| DNSfailure | Klienten kunde inte ansluta till tjänsten på grund av ett DNS-fel i nätverksstacken. |
| NoNetwork | Klienten skulle en anslutning, men nätverksstacken rapporterade att det fanns inga fysiska nätverk. |
| NoAuthorization | Klientanslutningen misslyckades vid försök att erhålla en Autentiseringstoken för anslutningen. |
| NoResources | Klienten fick slut på en lokal resurs (till exempel minne) när du försöker upprätta en anslutning. |
| Förbjudna | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade HTTP `403 Forbidden` statuskod på WebSocket-uppgraderingsbegäran. |
| Behörighet saknas | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade HTTP `401 Unauthorized` statuskod på WebSocket-uppgraderingsbegäran. |
| BadRequest | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade HTTP `400 Bad Request` statuskod på WebSocket-uppgraderingsbegäran. |
| ServerUnavailable | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade HTTP `503 Server Unavailable` statuskod på WebSocket-uppgraderingsbegäran. |
| ServerError | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade ett `HTTP 500` interna felkoden på WebSocket-uppgraderingsbegäran. |
| Timeout | Klientens anslutningsbegäran tidsgränsen utan ett svar från tjänsten. Den *End* fältet innehåller den tidpunkt då klienten tidsgränsen och stoppats och väntar på anslutningen. |
| ClientError | Anslutningen avbröts på grund av ett fel i intern klient i klienten. | 

### <a name="metric-microphone"></a>Mått `Microphone`

Den `Microphone` mått måste anges för alla tal aktiverar. Mätvärdet mäter tid under vilken ljudinsignal som aktivt används för en begäran om tal.

Använd följande exempel som riktlinjer för registrering *starta* tid-värden för den `Microphone` mått i ditt klientprogram:

* Ett klientprogram kräver att en användare måste trycka på en fysisk knapp för att starta mikrofonen. Efter tryck på knappen klientprogrammet läser indata från mikrofonens och skickar den till tal-tjänsten. Den *starta* värde för den `Microphone` mått registrerar tiden för när knappen push när mikrofonen är initierad och är redo att ange indata. Den *slutet* värde för den `Microphone` mått registrerar den tid när klientprogrammet stoppats direktuppspelat ljud till tjänsten när den har tagits emot av `speech.endDetected` meddelande från tjänsten.

* Ett klientprogram använder en nyckelordet spotter som lyssnar ”always”. Endast när nyckelordet spotter identifierar en fras talade utlösaren klientprogrammet samla in indata från mikrofonens och skicka den till tal-tjänsten. Den *starta* värde för den `Microphone` mått registrerar den tid när nyckelordet spotter meddelas klienten att börja använda indata från mikrofonens. Den *slutet* värde för den `Microphone` mått registrerar den tid när klientprogrammet stoppats direktuppspelat ljud till tjänsten när den har tagits emot av `speech.endDetected` meddelande från tjänsten.

* Ett klientprogram har åtkomst till ett konstant ljudström och utför tystnad/tal identifiering på ljud dataströmmen i en *tal modulen för villkorsidentifiering*. Den *starta* värde för den `Microphone` mått registrerar tid när den *tal modulen för villkorsidentifiering* meddelas klienten att börja använda indata från ljudströmmen. Den *slutet* värde för den `Microphone` mått registrerar den tid när klientprogrammet stoppats direktuppspelat ljud till tjänsten när den har tagits emot av `speech.endDetected` meddelande från tjänsten.

* Ett klientprogram behandlar andra Stäng av flera Stäng begäran och ett service svarsmeddelande Aktivera mikrofon att samla in indata för andra Stäng informeras om. Den *starta* värde för den `Microphone` mått registrerar den tid när klientprogrammet kan mikrofonens och börja använda indata från källan ljud. Den *slutet* värde för den `Microphone` mått registrerar den tid när klientprogrammet stoppats direktuppspelat ljud till tjänsten när den har tagits emot av `speech.endDetected` meddelande från tjänsten.

Den *End* tid värde för den `Microphone` mått registrerar den tid när klientprogrammet upphört ljudinsignal. I de flesta fall kan den här händelsen inträffar strax efter klienten tog emot den `speech.endDetected` meddelande från tjänsten. Klientprogram kan kontrollera att de korrekt som överensstämmer med protokollet genom att säkerställa att den *End* tid värde för den `Microphone` mått inträffar senare än inleverans time-värdet för den `speech.endDetected` meddelande. Och eftersom det är vanligtvis en fördröjning mellan slutet av en Stäng och starta av en annan Stäng, klienter kan kontrollera protokollet överensstämmelse genom att säkerställa att den *starta* tid på den `Microphone` mått för alla efterföljande Stäng korrekt registrerar tid när klienten *igång* med hjälp av mikrofon till dataströmmen ljudinsignal till tjänsten.

| Fält | Beskrivning | Användning |
| ----- | ----------- | ----- |
| Namn | Mikrofon | Krävs |
| Start | Tiden när klienten startas med ljudinsignal från mikrofonens eller andra ljudström eller tog emot en utlösare från nyckelordet spotter | Krävs |
| Slut | Den tid när klienten har slutat använda dataströmmen mikrofon eller ljud | Krävs |
| Fel | En beskrivning av felet som uppstod, om sådana finns. Om mikrofon åtgärderna lyckades ska klienter utesluta det här fältet. Den maximala längden på det här fältet är 50 tecken. | Krävs för fall utelämnas annars fel |

### <a name="metric-listeningtrigger"></a>Mått `ListeningTrigger`
Den `ListeningTrigger` mätvärdet mäter den tid när användaren kör åtgärden som initierar talindata. Den `ListeningTrigger` mått är valfritt, men klienter som kan ge mätvärdet uppmanas att göra detta.

Använd följande exempel som riktlinjer för registrering *starta* och *End* tid-värden för den `ListeningTrigger` mått i ditt klientprogram.

* Ett klientprogram kräver att en användare måste trycka på en fysisk knapp för att starta mikrofonen. Den *starta* värdet för det här måttet registrerar tidpunkten för knappen push. Den *End* värdet registrerar den tid när knappen push är klar.

* Ett klientprogram använder en nyckelordet spotter som lyssnar ”always”. Efter nyckelordet spotter identifierar en talade utlösaren fras klientprogrammet läser indata från mikrofonens och skickar den till tal-tjänsten. Den *starta* värdet för det här måttet registrerar när nyckelordet spotter emot ljud som sedan har identifierats som utlösare frasen. Den *End* värdet registrerar den tid när det sista ordet i frasen utlösaren har talas av användaren.

* Ett klientprogram har åtkomst till ett konstant ljudström och utför tystnad/tal identifiering på ljud dataströmmen i en *tal modulen för villkorsidentifiering*. Den *starta* värdet för det här måttet registrerar tid som den *tal modulen för villkorsidentifiering* emot ljud som sedan har identifierats som tal. Den *End* värdet registrerar tid när den *tal modulen för villkorsidentifiering* upptäckte tal.

* Ett klientprogram behandlar andra Stäng av flera Stäng begäran och ett service svarsmeddelande Aktivera mikrofon att samla in indata för andra Stäng informeras om. Klientprogrammet bör *inte* inkluderar en `ListeningTrigger` mått på den här tur.

| Fält | Beskrivning | Användning |
| ----- | ----------- | ----- |
| Namn | ListeningTrigger | Valfri |
| Start | Tidpunkten då klienten lyssnande utlösaren startades | Krävs |
| Slut | Tidpunkten då klienten lyssnande utlösaren avslutades | Krävs |
| Fel | En beskrivning av felet som uppstod, om sådana finns. Om utlösaråtgärden lyckades bör klienter ignorera det här fältet. Den maximala längden på det här fältet är 50 tecken. | Krävs för fall utelämnas annars fel |

#### <a name="sample-message"></a>Exempelmeddelande

I följande exempel visas ett meddelande som telemetri med både ReceivedMessages och mått delar:

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

Det här avsnittet beskrivs vilka typer av felmeddelanden och villkor som programmet behöver hantera.

### <a name="http-status-codes"></a>Statuskoder för HTTP

Under den WebSocket-uppgraderingsbegäran tal Service kan returnera ett av standard statuskoder för HTTP som `400 Bad Request`osv. Programmet måste korrekt hantera dessa felvillkor.

#### <a name="authorization-errors"></a>Auktoriseringsfel

Om felaktiga har angetts under uppgraderingen WebSocket tal tjänsten returnerar ett HTTP `403 Forbidden` statuskod. Bland de villkor som kan utlösa den här felkoden finns:

* Saknas *auktorisering* sidhuvud

* Ogiltig autentiseringstoken

* Utgångna autentiseringstoken

Den `403 Forbidden` felmeddelande anger inte ett problem med tal. Det här felmeddelandet indikerar ett problem med klientprogrammet.

### <a name="protocol-violation-errors"></a>Protokollfel överträdelse

Om tal tjänsten upptäcker eventuella överträdelser för protokollet från en klient, tjänsten avslutas WebSocket-anslutningen när du återvänder en *statuskod* och *orsak* för avslutning. Klientprogram kan använda den här informationen för att felsöka och lösa överträdelser.

#### <a name="incorrect-message-format"></a>Felaktigt meddelandeformat

Om en klient skickar en text eller binära meddelandet till tjänsten inte har kodats i rätt format i den här specifikationen, tjänsten stängs anslutningen med en *1007 ogiltig nyttolasten* statuskod. 

Tjänsten returnerar statuskoden för en mängd olika orsaker, som visas i följande exempel:

* ”Felaktig meddelandeformat. Binära meddelandet har ogiltigt huvud Storleksprefix ”. Klienten skickade en binär meddelanden som har ett ogiltigt huvud Storleksprefix.

* ”Felaktig meddelandeformat. Binära meddelandet har ogiltig huvudstorlek ”. Klienten har skickat en binära meddelandet som angetts ett ogiltigt huvud-storlek.

* ”Felaktig meddelandeformat. Binär meddelanderubriker i UTF-8-avkodning misslyckades ”. Klienten har skickat ett binärt meddelande som innehåller huvuden som inte korrekt kodad i UTF-8.

* ”Felaktig meddelandeformat. Textmeddelandet innehåller inga data ”. Klienten har skickat ett textmeddelande som inte innehåller några data i brödtexten.

* ”Felaktig meddelandeformat. Textmeddelande avkoda i UTF-8 misslyckades ”. Klienten har skickat ett SMS inte har kodats korrekt i UTF-8.

* ”Felaktig meddelandeformat. Textmeddelandet innehåller inga sidhuvud avgränsare ”. Klienten har skickat ett textmeddelande som inte innehöll huvudet avgränsare eller fel sidhuvud avgränsare som används.

#### <a name="missing-or-empty-headers"></a>Huvuden saknas eller är tomt

Om en klient skickar ett meddelande som inte har de nödvändiga huvudena *X RequestId* eller *sökväg*, tjänsten stängs anslutningen med en *1002 protokollfel* statuskod. Meddelandet är ”huvudet saknas/tomt. {Huvud name}.

#### <a name="requestid-values"></a>Begärande-ID-värden

Om en klient skickar ett meddelande som anger en *X RequestId* huvud med ett felaktigt format tjänsten stänger anslutningen och returnerar ett *1002 protokollfel* status. Meddelandet är ”ogiltig begäran. X-RequestId huvudvärde har inte angetts i nr dash UUID-format ”.

#### <a name="audio-encoding-errors"></a>Ljud kodningsfel

Om en klient skickar ett ljud segment som initierar en tur och ljudformatet eller kodning stämmer inte överens specifikationen som krävs, tjänsten stänger anslutningen och returnerar ett *1007 ogiltig nyttolasten* statuskod. Meddelandet anger formatet kodning felkälla.

#### <a name="requestid-reuse"></a>RequestId återanvändning

När en tur är klar, om en klient skickar ett meddelande som återanvänder begärande-ID från att aktivera, tjänsten stänger anslutningen och returnerar ett *1002 protokollfel* statuskod. Meddelandet är ”ogiltig begäran. Återanvändning av begäran-ID: n tillåts inte ”.

## <a name="connection-failure-telemetry"></a>Anslutningen misslyckades telemetri

För att säkerställa bästa möjliga användarupplevelsen klienter måste informera tal tjänst av tidsstämplarna för viktiga kontrollpunkter i en anslutning med hjälp av den *telemetri* meddelande. Det är även viktigt att klienter informera tjänsten anslutningar som har försökt men misslyckades.

För varje anslutningsförsöket misslyckades, skapar en *telemetri* meddelande med ett unikt *X RequestId* huvudvärde. Eftersom klienten kunde inte upprätta en anslutning i *ReceivedMessages* fält i JSON-meddelandetext kan utelämnas. Endast den `Connection` post i den *mått* fältet ingår. Den här posten innehåller början och slutet tidsstämplar som felet inträffade.

### <a name="connection-retries-in-telemetry"></a>Anslutningsförsök i telemetri

Klienter ska skilja *återförsök* från *flera anslutningsförsök* som händelsen som utlöser anslutningsförsöket. Anslutningsförsök som utförs via programmering utan några användarindata är återförsök. Flera anslutningsförsök som utförs som svar på indata från användaren är flera anslutningsförsök. Klienter får varje användare utlöst anslutningsförsöket ett unikt *X RequestId* och *telemetri* meddelande. Klienter återanvända den *X RequestId* för programmässiga återförsök. Om flera försök gjordes för ett enda anslutningsförsök varje nytt försök ingår som en `Connection` post i den *telemetri* meddelande.

Anta exempelvis att en användare som talar nyckelordet utlösaren att starta en anslutning och det första anslutningsförsöket misslyckas på grund av DNS-fel. Dock lyckas ett andra försök som görs via programmering av klienten. Eftersom klienten igen anslutningen utan ytterligare indata från användaren, använder klienten en enda *telemetri* meddelande med flera `Connection` poster att beskriva anslutningen.

Ett annat exempel anta att en användare talar nyckelordet utlösaren att starta en anslutning och den här anslutningsförsök misslyckas efter tre försök. Klienten ger dig stoppar försöker ansluta till tjänsten och informerar användaren om något gick fel. Användaren läser sedan upp nyckelordet utlösaren igen. Den här tiden kan anta att klienten ansluter till tjänsten. Efter anslutning, skickar klienten omedelbart ett *telemetri* meddelandet till tjänsten som innehåller tre `Connection` poster som beskriver anslutningsfel. Efter mottagandet av `turn.end` meddelande, skickar klienten en annan *telemetri* meddelande som beskriver anslutningen.

## <a name="error-message-reference"></a>Referens för felmeddelanden

### <a name="http-status-codes"></a>Statuskoder för HTTP

| HTTP-statuskod | Beskrivning | Felsökning |
| - | - | - |
| 400 Felaktig förfrågan | Klienten har skickat en begäran om WebSocket-anslutning som var felaktig. | Kontrollera att du har angett alla obligatoriska parametrar och HTTP-huvuden och att värden är korrekta. |
| 401 obehörig | Klienten innehöll inte den obligatoriska auktoriseringsinformationen. | Kontrollera att du skickar den *auktorisering* huvudet i WebSocket-anslutningen. |
| 403 Nekad | Klienten har skickat auktoriseringsinformation, men var ogiltigt. | Kontrollera att du inte skickar ett värde som har upphört att gälla eller är ogiltigt den *auktorisering* huvud. |
| 404 Hittades inte | Klienten försökte komma åt en URL-sökväg som inte stöds. | Kontrollera att du använder rätt URL för WebSocket-anslutningen. |
| 500 serverfel | Tjänsten påträffade ett internt fel och det gick inte att utföra den begärda åtgärden. | I de flesta fall är det här felet är tillfälligt. Gör om begäran. |
| 503 Tjänsten är inte tillgänglig | Tjänsten kunde inte hantera begäran. | I de flesta fall är det här felet är tillfälligt. Gör om begäran. |

### <a name="websocket-error-codes"></a>WebSocket-felkoder

| WebSocketsStatus kod | Beskrivning | Felsökning |
| - | - | - |
| 1000 normal avslutas | Tjänsten stängt WebSocket-anslutningen utan fel. | Läs i dokumentationen för att säkerställa att du förstår hur och när tjänsten kan avsluta WebSocket-anslutningen om stängningen WebSocket förväntades. |
| 1002 protokollfel | Klienten kunde inte följer protokollet krav. | Se till att du förstår dokumentationen protokollet och är tydliga om kraven. Läs föregående dokumentationen om fel skäl att se om du brott mot protokollet krav. |
| 1007 Ogiltig nyttolast Data | Klienten skickade en ogiltig nyttolast i ett protokollmeddelande. | Kontrollera det sista meddelandet som du skickade till tjänsten för fel. Läs föregående dokumentationen om nyttolast fel. |
| 1011 serverfel | Tjänsten påträffade ett internt fel och det gick inte att utföra den begärda åtgärden. | I de flesta fall är det här felet är tillfälligt. Gör om begäran. |

## <a name="related-topics"></a>Relaterade ämnen

Se en [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) som är en implementering av protokollet WebSocket-baserad tjänst som tal.
