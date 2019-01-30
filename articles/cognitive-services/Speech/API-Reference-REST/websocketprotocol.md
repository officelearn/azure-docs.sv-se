---
title: WebSocket-protokoll för Bing-taligenkänning | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Dokumentationen för protokollet för Bing-taligenkänning baserat på WebSockets
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 1d6c0a8ca04949216e6410ff81b15f79c7067522
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217296"
---
# <a name="bing-speech-websocket-protocol"></a>Bing Speech WebSocket-protokoll

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing-taligenkänning är en molnbaserad plattform med funktioner för de mest avancerade algoritmerna som är tillgängliga för konvertera talat ljud till text. Bing-taligenkänning-protokollet definierar den [anslutningsinställningar](#connection-establishment) mellan program och tjänsten och tal igenkänning av meddelanden som utbyts mellan motsvarigheter ([klienten kommer meddelanden](#client-originated-messages) och [service-genererade meddelanden](#service-originated-messages)). Dessutom [telemetrimeddelanden](#telemetry-schema) och [felhantering](#error-handling) beskrivs.

## <a name="connection-establishment"></a>Anslutningen upprättas

Protokollet Speech Service följer specifikationen som standard WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). En WebSocket-anslutning från början som en HTTP-begäran som innehåller HTTP-huvuden som indikerar att klientens önskan att uppgradera anslutningen till en WebSocket istället för att använda HTTP-semantik. Servern indikerar beredvillighet att delta i WebSocket-anslutning genom att returnera ett HTTP `101 Switching Protocols` svar. När du har ett utbyte av den här handskakning både klient- och fortsätta socket och börja använda ett meddelandebaserat protokoll för att skicka och ta emot information.

Om du vill börja WebSocket-handskakning skickar klientprogrammet en HTTPS-GET-begäran till tjänsten. Den innehåller standard WebSocket uppgradera rubriker tillsammans med andra rubriker som är specifika för tal.

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

Alla talade förfrågningar kräver den [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) kryptering. Användning av okrypterade talade förfrågningar stöds inte. Följande TLS-version stöds:

* TLS 1.2

### <a name="connection-identifier"></a>Anslutningsidentifierare

Taltjänsten kräver att alla klienter innehåller ett unikt ID för att identifiera anslutningen. Klienter *måste* inkluderar den *X ConnectionId* rubrik när de startar en WebSocket-handskakning. Den *X ConnectionId* rubriken måste vara en [universell unik identifierare](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID)-värde. Uppgradera WebSocket-begäranden som inte innehåller den *X ConnectionId*, inte anger ett värde för den *X ConnectionId* rubrik, eller inkluderar inte en giltig UUID värdet avvisas av tjänsten med en HTTP `400 Bad Request` svar.

### <a name="authorization"></a>Auktorisering

Utöver standard WebSocket-handskakningen rubriker talade förfrågningar kräver en *auktorisering* rubrik. Anslutningsbegäranden utan den här rubriken avvisas av tjänsten med en HTTP `403 Forbidden` svar.

Den *auktorisering* rubrik måste innehålla en åtkomsttoken för JSON Web Token (JWT).

Information om hur du prenumererar på och hämta API-nycklar som används för att hämta giltiga JWT-åtkomsttoken finns i den [Cognitive Services-prenumeration](https://azure.microsoft.com/try/cognitive-services/) sidan.

API-nyckel skickas till tjänsten token. Exempel:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Informationen i följande huvudet måste anges för tokenåtkomst.

| Name | Format | Beskrivning |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Din prenumerationsnyckel |

Tokentjänsten som returnerar JWT-åtkomsttoken som `text/plain`. Sedan JWT skickas som en `Base64 access_token` till handskakningen som en *auktorisering* rubrik som föregås av strängen `Bearer`. Exempel:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookies

Klienter *måste* stöder HTTP cookies som anges i [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>HTTP-omdirigering

Klienter *måste* stöd för omdirigering av standard-mekanismer som anges av den [HTTP-protokollspecifikation](http://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Slutpunkter för taligenkänning

Klienter *måste* använda en lämplig Speech Service-slutpunkt. Slutpunkten är baserad på erkännande läge och språk. Tabellen visar några exempel.

| Läge | Sökväg | Tjänstens URI |
| -----|-----|-----|
| Interaktiv | /speech/recognition/interactive/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Konversation | /speech/recognition/conversation/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| diktering | /speech/recognition/dictation/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Mer information finns i den [Service URI](../GetStarted/GetStartedREST.md#service-uri) sidan.

### <a name="report-connection-problems"></a>Rapport-anslutningsproblem

Klienter bör omedelbart rapportera alla problem som kan uppstå när du gör en anslutning. Meddelandeprotokollet för reporting misslyckade anslutningar beskrivs i [anslutning fel telemetri](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Anslutningen varaktighet begränsningar

Jämfört med vanliga web service HTTP-anslutningar för senaste WebSocket-anslutningar en *lång* tid. Med Taltjänsten placerar begränsningar på varaktigheten för WebSocket-anslutningar till tjänsten:

 * Maximal varaktighet för någon aktiv WebSocket-anslutning är 10 minuter. En anslutning är aktiv om tjänsten eller klienten skickar WebSocket meddelanden via den anslutningen. Tjänsten avslutas anslutningen utan varning när gränsen har nåtts. Klienter bör ta fram användarscenarier som inte kräver anslutning till förbli aktiv slut eller nästan maximal livstid.

 * Maximal varaktighet för alla inaktiva WebSocket-anslutning är 180 sekunder. En anslutning är inaktiv om varken tjänsten eller klienten överförd ett WebSocket-meddelande. När den maximala inaktiva livstiden har uppnåtts kan avslutas tjänsten inaktiv WebSocket-anslutning.

## <a name="message-types"></a>Typer av meddelanden

När en WebSocket-anslutning har upprättats mellan klienten och tjänsten kan kan både klienten och tjänsten skicka meddelanden. Det här avsnittet beskrivs formatet på meddelandena WebSocket.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) anger att WebSocket meddelanden kan överföra data med hjälp av ett textvärde eller en binär kodning. Två kodningar använder olika på wire-format. Varje format är optimerat för effektiv kodning, överföring och avkodning av meddelandenyttolast.

### <a name="text-websocket-messages"></a>WebSocket textmeddelanden

WebSocket textmeddelanden har en nyttolast för textinformation som består av en del av rubriker och en brödtext med välbekanta double vagnretur ny rad-par som används för HTTP-meddelanden. Och som HTTP-meddelanden, WebSocket textmeddelanden ange huvuden i *namn: värdet* format avgränsade med ett enda vagnretur ny rad-par. Valfri text som ingår i ett textmeddelande för WebSocket *måste* använder [UTF-8](https://tools.ietf.org/html/rfc3629) kodning.

WebSocket textmeddelanden måste ange en sökväg för meddelandet i rubriken *sökvägen*. Värdet för den här rubriken måste vara något av de tal protokoll meddelande-typer som definieras längre fram i det här dokumentet.

### <a name="binary-websocket-messages"></a>Binär WebSocket-meddelanden

Binär WebSocket-meddelanden har en binär nyttolast. I Speech Service-protokollet är ljud skickas till och tas emot från tjänsten med hjälp av binär WebSocket-meddelanden. Alla andra meddelanden är WebSocket textmeddelanden. 

Som WebSocket SMS, binär WebSocket-meddelanden som består av en rubrik och en brödtext-avsnittet. De första 2 byte av binära WebSocket-meddelandet anger, i [big endian](https://en.wikipedia.org/wiki/Endianness) ordning 16-bitars heltal-storlek för den. Den minsta huvudstorlek som avsnittet är 0 byte. Den maximala storleken är 8 192 byte. Texten i rubrikerna för binära WebSocket meddelanden *måste* använder [US-ASCII](https://tools.ietf.org/html/rfc20) kodning.

Rubriker i ett binärt WebSocket-meddelande kodas på samma sätt som WebSocket textmeddelanden. Den *namnvärdet:* format avgränsade med ett enda vagnretur ny rad-par. Binär WebSocket-meddelanden måste ange en sökväg för meddelandet i rubriken *sökvägen*. Värdet för den här rubriken måste vara något av de tal protokoll meddelande-typer som definieras längre fram i det här dokumentet.

Både text och binära WebSocket-meddelanden används i Speech Service-protokollet. 

## <a name="client-originated-messages"></a>Klienten kommer meddelanden

När anslutningen har upprättats kan kan både klienten och tjänsten börja skicka meddelanden. Det här avsnittet beskriver format och nyttolasten för meddelanden som klientprogram skickar till Speech Service. Avsnittet [Service-genererade meddelanden](#service-originated-messages) presenterar de meddelanden som har sitt ursprung i Speech Service och skickas till klientprogram.

De huvudsakliga meddelanden som skickas av klienten till tjänsterna är `speech.config`, `audio`, och `telemetry` meddelanden. Innan vi anser att varje meddelande i detalj, krävs vanligt beskrivs meddelandehuvudena för alla dessa meddelanden.

### <a name="required-message-headers"></a>Meddelandet krävs rubriker

Följande huvuden krävs för alla klient-genererade meddelanden.

| Sidhuvud | Value |
|----|----|
| Sökväg | Meddelande-sökvägen som anges i det här dokumentet |
| X-RequestId | UUID i ”no-dash”-format |
| X-Timestamp | Klienten UTC klockan tidsstämpel i ISO 8601-format |

#### <a name="x-requestid-header"></a>Rubriken X-RequestId

Klient-genererade begäranden identifieras unikt genom den *X-RequestId* meddelandehuvudet. Den här rubriken krävs för alla klient-genererade meddelanden. Den *X-RequestId* huvudets värde måste vara en UUID i ”no-dash” format, till exempel *123e4567e89b12d3a456426655440000*. Den *kan inte* finnas i den kanoniska formen *123e4567-e89b-12d3-a456-426655440000*. Begär utan en *X-RequestId* rubrik eller med ett huvudvärde med fel format för UUID: N kan tjänsten avsluta WebSocket-anslutning.

#### <a name="x-timestamp-header"></a>Rubriken X-tidsstämpel

Varje meddelande som skickats till Speech Service från ett klientprogram *måste* inkluderar en *X-tidsstämpel* rubrik. Värdet för den här rubriken är den tid när klienten skickar meddelandet. Begär utan en *X-tidsstämpel* rubrik eller orsaka att tjänsten ska avsluta WebSocket-anslutning med ett huvudvärde med fel format.

Den *X-tidsstämpel* huvudets värde måste vara i formatet ”åååå'-'MM'-'dd'T' HH': 'mm':'ss '.' fffffffZ ”där” fffffff ”är en del av en sekund. '12,5 ”innebär till exempel” 12 + 5/10 sekunder och ”12.526” innebär ”12 plus 526/1 000 sekunder”. Det här formatet överensstämmer med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och till skillnad från vanlig HTTP *datum* rubrik som är den ger en millisekund upplösning. Klientprogram kan avrunda tidsstämplar till närmaste millisekund. Klientprogram måste du se till att enhetens klocka korrekt spårar tid genom att använda en [Network Time Protocol (NTP) server](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>meddelande `speech.config`

Med Taltjänsten behöver veta egenskaperna för ditt program för att ge bästa möjliga taligenkänning. Obligatoriska egenskaper data innehåller information om enheten och Operativsystemet som driver ditt program. Du anger den här informationen i den `speech.config` meddelande.

Klienter *måste* skicka en `speech.config` meddelandet omedelbart efter att de upprätta anslutningen till Speech Service och innan de skickar någon `audio` meddelanden. Du behöver skicka en `speech.config` visas bara en gång per anslutning.

| Fält | Beskrivning |
|----|----|
| Kodning av WebSocket-meddelande | Text |
| Meddelandetext | Nyttolasten som en JSON-struktur |

#### <a name="required-message-headers"></a>Meddelandet krävs rubriker

| Huvudnamn | Value |
|----|----|
| Sökväg | `speech.config` |
| X-Timestamp | Klienten UTC klockan tidsstämpel i ISO 8601-format |
| Innehållstyp | application/json; charset=utf-8 |

Precis som med alla klient-genererade meddelanden i Speech Service-protokollet, den `speech.config` meddelande *måste* inkluderar en *X-tidsstämpel* -huvud som registrerar klient UTC-clock-tid när meddelandet har skickats till tjänsten. Den `speech.config` meddelande *inte* kräver en *X-RequestId* rubrik eftersom det här meddelandet är inte kopplad till en viss tal-begäran.

#### <a name="message-payload"></a>Meddelandenyttolast
Nyttolasten för den `speech.config` meddelandet är en JSON-struktur som innehåller information om programmet. I följande exempel visas den här informationen. Klient och enhet kontextinformation ingår i den *kontext* element i JSON-strukturen. 

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

System.version-elementet i den `speech.config` meddelandet innehåller versionen av tal SDK-programvara som används av klientprogram eller enhet. Värdet är av typen *major.minor.build.branch*. Du kan utelämna den *gren* komponenten om den inte är tillämplig.

##### <a name="os-element"></a>OS-elementet

| Fält | Beskrivning | Användning |
|-|-|-|
| os.platform | Den OS plattform som är värd för programmet, till exempel Windows, Android, iOS eller Linux |Krävs |
| os.name | OS-produktnamn, till exempel Debian eller Windows 10 | Krävs |
| os.version | Versionen av Operativsystemet i formuläret *major.minor.build.branch* | Krävs |

##### <a name="device-element"></a>Enheten element

| Fält | Beskrivning | Användning |
|-|-|-|
| Device.Manufacturer | Enhetstillverkaren för maskinvara | Krävs |
| device.model | Enhetsmodell | Krävs |
| device.version | Enhetens programvaruversion som tillhandahålls av tillverkaren av enheten. Det här värdet anger en version av den enhet som kan spåras av tillverkaren. | Krävs |

### <a name="message-audio"></a>meddelande `audio`

Talbaserade klientprogram skicka ljud till Speech Service genom att konvertera ljudströmmen i en serie med ljud segment. Varje segment av ljud innebär en del av talat ljud som ska vara transkriberas av tjänsten. Den maximala storleken för ett enda ljud segment är 8 192 byte. Ljudström meddelanden är *binära WebSocket meddelanden*.

Klienter använder den `audio` meddelandet för att skicka ett ljud segment till tjänsten. Klienter läser ljud från mikrofonen i segment och skickar dessa segment till Speech Service för avskrift. Först `audio` meddelande måste innehålla en korrekt strukturerad rubrik som korrekt anger ljudet uppfyller något av kodningsformat som stöds av tjänsten. Ytterligare `audio` meddelanden innehåller endast binära ljudet strömma data läses från mikrofonen.

Klienter kan eventuellt skicka en `audio` meddelandet med en nollängds-text. Det här meddelandet talar om för tjänsten som klienten vet att användaren stoppade talar, uttryck är klar och mikrofonen är avstängd.

Taltjänsten använder först `audio` meddelande som innehåller en om unika begäranidentifierare kan skicka en signal i början av en ny begäran/svar-cykel eller *aktivera*. När tjänsten har tagit emot en `audio` meddelande med en ny begäran identifierare, kastat några köade eller ej skickade meddelanden som är associerade med alla föregående aktivera.

| Fält | Beskrivning |
|-------------|----------------|
| Kodning av WebSocket-meddelande | Binär |
| Meddelandetext | Binära data för ljud segment. Maximal storlek är 8 192 byte. |

#### <a name="required-message-headers"></a>Meddelandet krävs rubriker

Följande huvuden krävs för alla `audio` meddelanden.

| Sidhuvud         |  Value     |
| ------------- | ---------------- |
| Sökväg | `audio` |
| X-RequestId | UUID i ”no-dash”-format |
| X-Timestamp | Klienten UTC klockan tidsstämpel i ISO 8601-format |
| Innehållstyp | Ljud innehållstyp. Typen måste vara antingen *wav-ljud/x* (PCM) eller *ljud/silke* (silke). |

#### <a name="supported-audio-encodings"></a>Ljud kodningar som stöds

Det här avsnittet beskrivs ljudcodec som stöds av Speech Service.

##### <a name="pcm"></a>PCM

Med Taltjänsten accepterar okomprimerade pulse kod modulering (PCM) ljud. Ljudet skickas till tjänsten i [WAV](https://en.wikipedia.org/wiki/WAV) -format, så att dela in första ljudet *måste* ett giltigt [Resource Interchange filformat](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF)-huvud. Om en klient initierar ett varv med ett ljud segment som har *inte* inkludera ett giltigt RIFF-huvud, tjänsten avvisar begäran och avslutar WebSocket-anslutning.

PCM ljud *måste* samlas in vid 16 kHz med 16 bitar per exemplet och en kanal (*riff-16khz-16-bitars-mono-pcm*). Taltjänsten stöder inte stereo ljudströmmar och avvisar ljudströmmar som inte använder den angivna bithastighet, samplingsfrekvens eller flera kanaler.

##### <a name="opus"></a>Opus

Opus är en öppen, royaltyfri, mångsidiga ljudcodec. Taltjänsten stöder Opus med en konstant bithastighet av `32000` eller `16000`. Endast den `OGG` behållare för Opus stöds för närvarande som anges av den `audio/ogg` MIME-typen.

Om du vill använda Opus, ändra den [JavaScript exempel](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) och ändra den `RecognizerSetup` metod för att returnera.

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

Människor inte signalera uttryckligen när de är klar talar. Alla program som accepterar tal som indata har två alternativ för hantering av slutet av tal i en ljudström: slutet av tal-identifiering och klienten slutet av tal-identifiering. Med dessa två alternativ ger tjänsten slutet av tal-identifiering vanligtvis en bättre användarupplevelse.

##### <a name="service-end-of-speech-detection"></a>Tjänsten slutet av tal-identifiering

Program kan tjänsten för att identifiera när användaren har slutförts och tala om du vill skapa den perfekta handsfree-tal-upplevelsen. Klienter skickar ljud från mikrofonen som *ljud* segmenterar förrän tjänsten identifierar tystnad och svarar med en `speech.endDetected` meddelande.

##### <a name="client-end-of-speech-detection"></a>Klienten slutet av tal-identifiering

Klientprogram som används att skicka en signal slutet av tal på något sätt också kan ge tjänsten som signal. Ett klientprogram kan till exempel ha en ”stoppa” eller ”ljud av” knappen som användaren kan trycka på. Om du vill skicka en signal slutet av tal-, klientprogram skicka ett *ljud* segment meddelandet med en nollängds-text. Med Taltjänsten tolkar meddelandet i slutet av den inkommande ljudströmmen.

### <a name="message-telemetry"></a>meddelande `telemetry`

Klientprogram *måste* bekräftar slutet av varje aktivera genom att skicka telemetri om i sin tur till Speech Service. Aktivera slutpunkt bekräftelse tillåter Speech-tjänsten för att säkerställa att alla meddelanden som krävs för att slutföra begäran och svaret korrekt togs emot av klienten. Aktivera slutpunkt bekräftelse kan också Speech-tjänsten för att kontrollera att klientprogrammen fungerar som förväntat. Den här informationen är ovärderlig om du behöver hjälp med att felsöka ditt tal-aktiverade program.

Klienter måste godkänna en aktivera är slut genom att skicka en `telemetry` meddelande strax efter som tar emot en `turn.end` meddelande. Klienter använder för att bekräfta den `turn.end` så snart som möjligt. Om ett klientprogram inte kan bekräfta att Stäng slutet, avsluta tal-tjänsten anslutningen med ett fel. Klienterna måste skicka bara en `telemetry` meddelande för varje begäran och svar som identifieras av den *X-RequestId* värde.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `telemetry` |
| X-Timestamp | Klienten UTC klockan tidsstämpel i ISO 8601-format |
| Innehållstyp | `application/json` |
| Meddelandetext | En JSON-struktur som innehåller klientens information om i sin tur |

Schemat för innehållet i den `telemetry` meddelande definieras i den [telemetri schemat](#telemetry-schema) avsnittet.

#### <a name="telemetry-for-interrupted-connections"></a>Telemetri för avbrutna anslutningar

Om nätverksanslutningen av någon anledning misslyckas under ett drag och klienten gör *inte* får en `turn.end` meddelande från tjänsten, skickar klienten en `telemetry` meddelande. Detta meddelande beskriver misslyckade begäranden nästa gång klienten ansluter till tjänsten. Klienterna behöver inte omedelbart försöka ansluta till att skicka den `telemetry` meddelande. Meddelandet kan buffras på klienten och skickas via en framtida användare begärde anslutning. Den `telemetry` meddelande för misslyckade begäranden *måste* använder den *X-RequestId* värdet från den misslyckade begäranden. Den kan skickas till tjänsten när en anslutning har upprättats, utan att vänta på att skicka eller ta emot för andra meddelanden.

## <a name="service-originated-messages"></a>Tjänsten kommer meddelanden

Det här avsnittet beskrivs de meddelanden som har sitt ursprung i Speech Service och skickas till klienten. Taltjänst upprätthåller ett register för klientfunktioner och genererar meddelanden som krävs för varje klient så inte alla klienter tar emot alla meddelanden som beskrivs här. Av utrymmesskäl, meddelanden refererar till värdet för den *sökväg* rubrik. Exempelvis kan vi refererar till en WebSocket SMS: et med den *sökväg* värdet `speech.hypothesis` som ett speech.hypothesis-meddelande.

### <a name="message-speechstartdetected"></a>meddelande `speech.startDetected`

Den `speech.startDetected` meddelandet anger att Speech Service påträffas tal i ljudströmmen.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `speech.startDetected` |
| Innehållstyp | application/json; charset=utf-8 |
| Meddelandetext | JSON-strukturen som innehåller information om villkor när början av tal har identifierats. Den *Offset* fält i den här strukturen anger förskjutningen (i 100 nanosekunder enheter) när tal har upptäckts i ljudström, i förhållande till början av strömmen. |

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

Taligenkänning genererar Speech Service regelbundet hypoteser om orden tjänsten känns igen. Speech Service skickar dessa hypoteser till klienten ungefär var 300 millisekund. Den `speech.hypothesis` lämpar sig *endast* för bättre användarupplevelse för tal. Du måste inte vidta några beroende på innehållet eller korrektheten i texten i dessa meddelanden.

 Den `speech.hypothesis` meddelandet gäller för de klienter som har vissa text rendering funktionen och vill ge nära realtid feedback om pågående erkännande till den person som talar.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `speech.hypothesis` |
| X-RequestId | UUID i ”no-dash”-format |
| Innehållstyp | application/json |
| Meddelandetext | Tal hypotesen JSON-struktur |

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

Den *Offset* elementet anger förskjutningen (i 100 nanosekunder enheter) när frasen identifierades i förhållande till början av ljudströmmen.

Den *varaktighet* elementet anger tiden (i 100 nanosekunder enheter) för tal frasen.

Klienter måste inte göra några antaganden om frekvens, tidsinställning eller texten i en hypotes tal eller text i alla två tal hypoteser konsekvens. Hypoteser är bara ögonblicksbilder i avskrift processen i tjänsten. De utgör inte en stabil anhopning av avskrift. Till exempel en första hypotesen tal kan innehålla orden ”bra skojs skull” och andra hypotesen kan innehålla orden ”hitta roliga”. Speech Service utföra inte någon efter bearbetning (till exempel versaler, skiljetecken) på texten i tal hypotesen.

### <a name="message-speechphrase"></a>meddelande `speech.phrase`

När Speech Service anger att det finns tillräckligt med information för att producera ett resultat för taligenkänning som inte ändrar tjänsten ger en `speech.phrase` meddelande. Med Taltjänsten ger dessa resultat när det upptäcker att du är klar, en fras eller ett par meningar.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `speech.phrase` |
| Innehållstyp | application/json |
| Meddelandetext | Tal frasen JSON-struktur |

Tal frasen JSON-schemat innehåller följande fält: `RecognitionStatus`, `DisplayText`, `Offset`, och `Duration`. Läs mer om de här fälten, [avskrift svar](../concepts.md#transcription-responses).

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

Den `speech.endDetected` meddelandet anger att klientprogrammet ska avbrytas strömning av ljud till tjänsten.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `speech.endDetected` |
| Meddelandetext | JSON-strukturen som innehåller förskjutningen när slutet av tal har identifierats. Förskjutningen representeras i enheter om 100 nanosekunder förskjutning från början av ljud som används för taligenkänning. |
| Innehållstyp | application/json; charset=utf-8 |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

Den *Offset* elementet anger förskjutningen (i 100 nanosekunder enheter) när frasen identifierades i förhållande till början av ljudströmmen.

### <a name="message-turnstart"></a>meddelande `turn.start`

Den `turn.start` följs av en aktivera perspektiv av tjänsten. Den `turn.start` meddelandet är alltid den första svar du får för varje begäran. Om du inte får en `turn.start` visas, förutsätter att tillståndet för tjänst-anslutningen är ogiltig.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `turn.start` |
| Innehållstyp | application/json; charset=utf-8 |
| Meddelandetext | JSON-struktur |

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

Innehållet i den `turn.start` meddelandet är en JSON-struktur som innehåller sammanhangsberoende i början av länken. Den *kontext* elementet innehåller ett *serviceTag* egenskapen. Den här egenskapen anger ett Taggvärde som tjänsten är associerad med i sin tur. Det här värdet kan användas av Microsoft om du behöver hjälp med att felsöka fel i programmet.

### <a name="message-turnend"></a>meddelande `turn.end`

Den `turn.end` signalerar till slutet av ett varv perspektiv av tjänsten. Den `turn.end` meddelandet är alltid den senaste svar du får för varje begäran. Klienter kan använda mottagaren av meddelandet som en signal för rensningsaktiviteter och gå över till inaktivt läge. Om du inte får en `turn.end` visas, förutsätter att tillståndet för tjänst-anslutningen är ogiltig. I sådana fall att Stäng den befintliga anslutningen till tjänsten och återansluta.

| Fält | Beskrivning |
| ------------- | ---------------- |
| Kodning av WebSocket-meddelande | Text |
| Sökväg | `turn.end` |
| Meddelandetext | Ingen |

#### <a name="sample-message"></a>Exempelmeddelande

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Telemetri schema

Innehållet i den *telemetri* meddelandet är en JSON-struktur som innehåller klientens information om ett varv eller ett anslutningsförsök. Strukturen består av klienten tidsstämplar som registrerar när klienthändelser inträffar. Varje tidsstämpel måste vara i ISO 8601-format som beskrivs i avsnittet ”X-tidsstämpel huvud”. *Telemetri* meddelanden som inte anger alla obligatoriska fält i JSON-strukturen och som inte använder rätt Tidsstämpelformat kan orsaka att tjänsten ska avsluta anslutning till klienten. Klienter *måste* ange giltiga värden för alla obligatoriska fält. Klienter *bör* anger värden för valfria fält när så är lämpligt. De värden som visas i exemplen i det här avsnittet är enbart för illustration.

Telemetri schemat är indelad i följande delar: tog emot meddelande tidsstämplar och mått. Formatera och användning för varje del anges i följande avsnitt.

### <a name="received-message-time-stamps"></a>Mottagna meddelandet tidsstämplar

Klienter måste innehålla tid för mottagande värden för alla meddelanden som de får när du har anslutit till tjänsten. Dessa värden måste registrera tid när klienten *emot* varje meddelande från nätverket. Värdet ska inte registrera en annan tidpunkt. Exempelvis kan klienten inte ska registrera tiden när det *fattat* för meddelandet. De mottagna meddelandet tidsstämplarna anges i en matris med *namnvärdet:* par. Anger namnet på paret den *sökväg* värdet för meddelandet. Värdet för paret anger klienttid när meddelandet togs emot. Eller om flera meddelanden för det angivna namnet har tagits emot, värdet för paret är en matris med tidsstämplar som anger när meddelandena har tagits emot.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Klienter *måste* bekräfta mottagandet av alla meddelanden som skickas av tjänsten genom att inkludera tidsstämplar för meddelandena i JSON-texten. Om en klient inte kan bekräfta mottagandet av ett meddelande, kan tjänsten avslutar anslutningen.

### <a name="metrics"></a>Mått

Klienter måste innehålla information om händelser som inträffat under livslängden för en begäran. Finns stöd för följande mått: `Connection`, `Microphone`, och `ListeningTrigger`.

### <a name="metric-connection"></a>Mått `Connection`

Den `Connection` mått anger information om anslutningsförsök av klienten. Måttet måste innehålla tidsstämplar när WebSocket-anslutning startades och är klar. Den `Connection` mått krävs *endast för den första Stäng av en anslutning*. Efterföljande aktiverar krävs inte för att inkludera denna information. Om en klient gör flera anslutningsförsök innan en anslutning har upprättats, information om *alla* anslutningsförsöken ska inkluderas. Mer information finns i [anslutning fel telemetri](#connection-failure-telemetry).

| Fält | Beskrivning | Användning |
| ----- | ----------- | ----- |
| Name | `Connection` | Krävs |
| ID | Anslutningen ID-värde som användes i den *X ConnectionId* rubriken för den här begäran om anslutning | Krävs |
| Start | Tiden när klienten har skickat anslutningsbegäran | Krävs |
| Slut | Tiden när klienten tog emot meddelande att anslutningen har upprättats har eller, i fall av Schemaläggningsfel, avvisat nekade eller misslyckades | Krävs |
| Fel | En beskrivning av felet som har inträffat, om sådana. Om en anslutning har upprättats, utelämna klienter det här fältet. Den maximala längden på det här fältet är 50 tecken. | Krävs för fall av Schemaläggningsfel, annars utelämnas |

Felbeskrivningen ska vara högst 50 tecken och vi är en av de värden som anges i följande tabell. Om felet inte matchar någon av dessa värden, klienter kan använda en kortfattad beskrivning av felet med hjälp av [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) utan blanksteg. Möjligheten att skicka en *telemetri* meddelande kräver en anslutning till tjänsten, så bara tillfälligt eller tillfälliga fel kan rapporteras i den *telemetri* meddelande. Fel villkor som *permanent* blockera en klient från att upprätta en anslutning till tjänsten hindra klienten från att skicka något meddelande till tjänsten, inklusive *telemetri* meddelanden.

| Fel | Användning |
| ----- | ----- |
| DNSfailure | Klienten kunde inte ansluta till tjänsten på grund av ett DNS-fel i nätverksstacken. |
| NoNetwork | Klienten skulle en anslutning, men nätverksstacken rapporterade att det fanns inga fysiska nätverk. |
| NoAuthorization | Klientanslutningen misslyckades vid försök att erhålla en Autentiseringstoken för anslutningen. |
| NoResources | Klienten tog slut några lokala resurser (till exempel minne) när du försöker upprätta en anslutning. |
| Förbjuden | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade ett HTTP `403 Forbidden` statuskod på WebSocket-uppgraderingsförfrågan. |
| Ej auktoriserad | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade ett HTTP `401 Unauthorized` statuskod på WebSocket-uppgraderingsförfrågan. |
| BadRequest | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade ett HTTP `400 Bad Request` statuskod på WebSocket-uppgraderingsförfrågan. |
| ServerUnavailable | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade ett HTTP `503 Server Unavailable` statuskod på WebSocket-uppgraderingsförfrågan. |
| ServerError | Klienten kunde inte ansluta till tjänsten eftersom tjänsten returnerade ett `HTTP 500` interna felkoden på WebSocket-uppgraderingsförfrågan. |
| Tidsgräns | Klientens begäran om anslutning uppnåddes utan ett svar från tjänsten. Den *slutet* fältet innehåller den tidpunkt då klienten tidsgränsen och stoppats och väntar anslutningen. |
| ClientError | Anslutningen avbröts av klienten på grund av ett internt fel. | 

### <a name="metric-microphone"></a>Mått `Microphone`

Den `Microphone` mått måste anges för alla tal aktiverar. Det här mätvärdet mäter tid under vilken ljudindata som aktivt används för en begäran om tal.

Använd följande exempel som riktlinjer för inspelning *starta* tid värden för den `Microphone` mått i klientprogrammet:

* Ett klientprogram kräver att en användare måste trycka på en fysisk knapp för att starta mikrofonen. Efter tryck på knappen klientprogrammet läser indata från mikrofonen och skickar dem till tal-tjänsten. Den *starta* värde för den `Microphone` mått registrerar tiden efter knappen push när mikrofonen är initierad och är redo att ange indata. Den *slutet* värde för den `Microphone` mått registrerar tiden när klientprogrammet upphört ljud till tjänsten efter den emot den `speech.endDetected` meddelande från tjänsten.

* Ett klientprogram använder en nyckelordet spotter som lyssnar ”alltid”. Endast efter nyckelordet spotter identifierar en fras talat utlösaren klientprogrammet samlar in indata från mikrofonen och skicka den till Speech Service. Den *starta* värde för den `Microphone` mått registrerar tiden när nyckelordet spotter meddelas att klienten kan börja använda indata från mikrofonen. Den *slutet* värde för den `Microphone` mått registrerar tiden när klientprogrammet upphört ljud till tjänsten efter den emot den `speech.endDetected` meddelande från tjänsten.

* Ett klientprogram som har åtkomst till en konstant ljudström och utför åsidosatt inaktivitet/tal identifiering på den ljudströmmen i en *tal-modulen för villkorsidentifiering*. Den *starta* värde för den `Microphone` mått registrerar tiden när den *tal-modulen för villkorsidentifiering* meddelas att klienten kan börja använda indata från ljudströmmen. Den *slutet* värde för den `Microphone` mått registrerar tiden när klientprogrammet upphört ljud till tjänsten efter den emot den `speech.endDetected` meddelande från tjänsten.

* Ett klientprogram bearbetar andra Stäng av en begäran om flera aktivera och underrättat genom ett svarsmeddelande för tjänsten att slå på mikrofonen att samla in indata för andra aktivera. Den *starta* värde för den `Microphone` mått registrerar den tidpunkt då klientprogrammet kan mikrofonen och börjar med indata från den ljudkällan. Den *slutet* värde för den `Microphone` mått registrerar tiden när klientprogrammet upphört ljud till tjänsten efter den emot den `speech.endDetected` meddelande från tjänsten.

Den *slutet* tid värde för den `Microphone` mått registrerar tiden när klientprogrammet upphört ljudindata. I de flesta fall är den här händelsen inträffar strax efter klienten tog emot den `speech.endDetected` meddelande från tjänsten. Klientprogram kan kontrollera att de korrekt som överensstämmer med protokollet genom att säkerställa att den *slutet* tid värde för den `Microphone` mått inträffar senare än kvitto time-värdet för den `speech.endDetected` meddelande. Och eftersom det vanligtvis finns en fördröjning mellan en aktivera är slut och början av en annan aktivera klienter kan kontrollera protokollöverensstämmelse genom att säkerställa att den *starta* för den `Microphone` mått på alla efterföljande tur korrekt registrerar tiden när klienten *igång* med mikrofonen till ljud strömindata till tjänsten.

| Fält | Beskrivning | Användning |
| ----- | ----------- | ----- |
| Name | Mikrofon | Krävs |
| Start | Tiden när klienten komma igång med in ljud från mikrofonen eller andra ljudström eller tog emot en utlösare från nyckelordet spotter | Krävs |
| Slut | Tiden när klienten helt slutat använda dataströmmen mikrofon eller ljud | Krävs |
| Fel | En beskrivning av felet som har inträffat, om sådana. Om mikrofon åtgärderna hade önskat resultat, utelämna klienter det här fältet. Den maximala längden på det här fältet är 50 tecken. | Krävs för fall av Schemaläggningsfel, annars utelämnas |

### <a name="metric-listeningtrigger"></a>Mått `ListeningTrigger`
Den `ListeningTrigger` mätvärdet mäter den tid när användaren utför den åtgärd som initierar talindata. Den `ListeningTrigger` mått är valfritt, men klienter som kan ge det här måttet uppmuntras att göra detta.

Använd följande exempel som riktlinjer för inspelning *starta* och *slutet* tid värden för den `ListeningTrigger` mått i klientprogrammet.

* Ett klientprogram kräver att en användare måste trycka på en fysisk knapp för att starta mikrofonen. Den *starta* värdet för det här måttet registrerar tidpunkten för knappen push-meddelandet. Den *slutet* värdet registrerar tiden när knappen push-överföringen är klar.

* Ett klientprogram använder en nyckelordet spotter som lyssnar ”alltid”. Efter nyckelordet spotter identifierar en fras talat utlösare, klientprogrammet läser indata från mikrofonen och skickar dem till tal-tjänsten. Den *starta* värdet för det här måttet registrerar när nyckelordet spotter emot ljud som upptäcktes sedan som utlösaren frasen. Den *slutet* värdet registrerar tiden när det sista ordet i frasen utlösaren har talat av användaren.

* Ett klientprogram som har åtkomst till en konstant ljudström och utför åsidosatt inaktivitet/tal identifiering på den ljudströmmen i en *tal-modulen för villkorsidentifiering*. Den *starta* värdet för det här måttet registrerar tiden som den *tal-modulen för villkorsidentifiering* emot ljud som upptäcktes sedan som tal. Den *slutet* värdet registrerar tiden när den *tal-modulen för villkorsidentifiering* identifierade tal.

* Ett klientprogram bearbetar andra Stäng av en begäran om flera aktivera och underrättat genom ett svarsmeddelande för tjänsten att slå på mikrofonen att samla in indata för andra aktivera. Klientprogrammet bör *inte* inkluderar en `ListeningTrigger` mått för den här aktivera.

| Fält | Beskrivning | Användning |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | Valfritt |
| Start | Tidpunkten då klienten lyssnande utlösaren startades | Krävs |
| Slut | Tidpunkten då klienten lyssnande utlösaren avslutades | Krävs |
| Fel | En beskrivning av felet som har inträffat, om sådana. Om utlösaråtgärden lyckades utelämna klienter det här fältet. Den maximala längden på det här fältet är 50 tecken. | Krävs för fall av Schemaläggningsfel, annars utelämnas |

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

Det här avsnittet beskrivs vilka typer av felmeddelanden och villkor som programmet behöver för att hantera.

### <a name="http-status-codes"></a>HTTP-statuskoder

Under uppgraderingen begäran WebSocket Speech Service kan returnera någon av de vanliga HTTP-statuskoder som `400 Bad Request`osv. Programmet måste korrekt hantera dessa felvillkor.

#### <a name="authorization-errors"></a>Auktoriseringsfel

Om felaktig auktorisering har angetts under uppgraderingen WebSocket Speech Service returnerar ett HTTP `403 Forbidden` statuskod. Bland de villkor som kan utlösa den här felkoden finns:

* Saknas *auktorisering* rubrik

* Ogiltig autentiseringstoken

* Autentiseringstoken har upphört att gälla

Den `403 Forbidden` felmeddelande anger inte ett problem med Speech Service. Det här felmeddelandet indikerar ett problem med klientprogrammet.

### <a name="protocol-violation-errors"></a>Fel vid protokollöverträdelser

Om Speech Service identifierar överträdelser protokollet från en klient, tjänsten avslutas WebSocket-anslutning efter att ha returnerat en *statuskod* och *orsak* för avslutas. Klientprogram kan använda den här informationen för att felsöka och lösa överträdelser.

#### <a name="incorrect-message-format"></a>Felaktig meddelandeformat

Om en klient skickar en text eller binära meddelandet till den tjänst som inte har kodats i rätt format som anges i den här specifikationen, tjänsten stängs anslutningen med en *1007 Ogiltig nyttolast Data* statuskod. 

Tjänsten returnerar den här statuskoden för en mängd orsaker, som visas i följande exempel:

* ”Felaktig meddelandeformat. Binära meddelandet har ogiltigt huvud Storleksprefix ”. Klienten skickade en binär meddelanden som har ett ogiltigt huvud-prefix för storlek.

* ”Felaktig meddelandeformat. Binära meddelandet har ogiltig huvudstorlek ”. Klienten har skickat en binära meddelandet som angav en ogiltig huvudstorlek.

* ”Felaktig meddelandeformat. Binär meddelandehuvudena avkodning i UTF-8 misslyckades ”. Klienten har skickat en binära meddelandet som innehåller rubriker som inte korrekt kodad i UTF-8.

* ”Felaktig meddelandeformat. SMS: et innehåller inga data ”. Klienten har skickat ett textmeddelande som innehåller inga data för brödtext.

* ”Felaktig meddelandeformat. SMS: et avkodning i UTF-8 misslyckades ”. Klienten har skickat ett SMS som inte har kodats korrekt i UTF-8.

* ”Felaktig meddelandeformat. Textmeddelandet innehåller ingen rubrik avgränsare ”. Klienten har skickat ett SMS som inte innehöll huvudet avgränsare eller fel sidhuvud avgränsare som används.

#### <a name="missing-or-empty-headers"></a>Saknas eller är tomt rubriker

Om en klient skickar ett meddelande som inte har de nödvändiga rubrikerna *X-RequestId* eller *sökväg*, tjänsten stängs anslutningen med en *1002 protokollfel* statuskod. Meddelandet är ”huvud saknas/tomt. {Huvudet name}.

#### <a name="requestid-values"></a>RequestId värden

Om en klient skickar ett meddelande som anger en *X-RequestId* huvud med ett felaktigt format, tjänsten stängs anslutningen och returnerar en *1002 protokollfel* status. Meddelandet är ”ogiltig begäran. X-RequestId huvudets värde angavs inte i nr dash UUID-format ”.

#### <a name="audio-encoding-errors"></a>Ljud kodningsfel

Om en klient skickar en ljud segment som initierar ett varv och ljudformatet eller kodning stämmer inte överens med specifikationen krävs, tjänsten stängs anslutningen och returnerar en *1007 Ogiltig nyttolast Data* statuskod. Meddelandet anger formatet kodning felkälla.

#### <a name="requestid-reuse"></a>RequestId återanvändning

När ett varv är klar, om en klient skickar ett meddelande med Återanvänd begäranidentifieraren från att Stäng, tjänsten stängs anslutningen och returnerar en *1002 protokollfel* statuskod. Meddelandet är ”ogiltig begäran. Återanvändning av begäran-ID: n tillåts inte ”.

## <a name="connection-failure-telemetry"></a>Anslutningen misslyckades telemetri

För att säkerställa den bästa möjliga användarupplevelsen, klienter måste meddela Speech Service av tidsstämplarna för viktiga kontrollpunkter i en anslutning med hjälp av den *telemetri* meddelande. Det är lika viktigt att klienter informera tjänsten med de anslutningar som har gjorts men misslyckades.

För varje anslutningsförsök som inte skapar en *telemetri* meddelandet med ett unikt *X-RequestId* huvudets värde. Eftersom klienten kunde inte upprätta en anslutning i *ReceivedMessages* fält i JSON-texten kan utelämnas. Endast den `Connection` post i den *mått* fält ingår. Den här posten innehåller början och slutet tidsstämplar samt felet som uppstod.

### <a name="connection-retries-in-telemetry"></a>Anslutningsförsök i telemetri

Klienter ska skilja *återförsök* från *flera anslutningsförsök* av händelse som utlöser anslutningsförsöket. Anslutningsförsök som utförs via programmering utan några användarindata är återförsök. Flera anslutningsförsök som utförs som svar på indata från användaren är flera anslutningsförsök. Klienter ger varje användare som utlöste anslutningsförsök ett unikt *X-RequestId* och *telemetri* meddelande. Klienter återanvända den *X-RequestId* programmässiga återförsök. Om flera försök har gjorts för ett enda anslutningsförsök, varje nytt försök ingår som en `Connection` post i den *telemetri* meddelande.

Anta exempelvis att en användare talar nyckelordet-utlösare för att starta en anslutning och det första anslutningsförsöket misslyckas på grund av DNS-fel. Dock lyckas en andra försöket som görs via programmering av klienten. Eftersom klienten göras anslutningen utan ytterligare indata från användaren, klienten använder en enda *telemetri* meddelande med flera `Connection` poster att beskriva anslutningen.

Ett annat exempel är att anta att en användare talar nyckelordet-utlösare för att starta en anslutning och den här anslutningsförsöket misslyckas efter tre försök. Klienten ger dig stoppas försöker ansluta till tjänsten och informerar användaren om att något gick fel. Användaren talar nyckelordet utlösaren sedan igen. Den här tiden kan anta att klienten ansluter till tjänsten. När du har anslutit, skickar klienten omedelbart ett *telemetri* meddelande till den tjänst som innehåller tre `Connection` poster som beskriver anslutningsfel. När du tar emot den `turn.end` meddelandet, skickar klienten en annan *telemetri* meddelande som beskriver lyckad anslutning.

## <a name="error-message-reference"></a>Referens för felmeddelanden

### <a name="http-status-codes"></a>HTTP-statuskoder

| HTTP-statuskod | Beskrivning | Felsökning |
| - | - | - |
| 400 Felaktig förfrågan | Klienten har skickat en begäran om anslutning WebSocket som var felaktig. | Kontrollera att du har angett alla obligatoriska parametrar och HTTP-huvuden och att värden är korrekta. |
| 401 Ej behörig | Klienten innehöll inte de nödvändiga auktoriseringsinformationen. | Kontrollera att du skickar den *auktorisering* rubrik i WebSocket-anslutning. |
| 403 Åtkomst nekas | Klienten har skickat auktoriseringsinformation, men det var ogiltig. | Kontrollera att du inte skickar ett värde som har upphört att gälla eller är ogiltig den *auktorisering* rubrik. |
| 404 Kunde ej hittas | Klienten försökte komma åt en URL-sökväg som inte stöds. | Kontrollera att du använder rätt Webbadress för WebSocket-anslutning. |
| 500 Server Error | Tjänsten påträffade ett internt fel och det gick inte att utföra den begärda åtgärden. | I de flesta fall är det här felet är tillfälligt. Gör om begäran. |
| 503 Tjänsten är inte tillgänglig | Tjänsten kunde inte hantera begäran. | I de flesta fall är det här felet är tillfälligt. Gör om begäran. |

### <a name="websocket-error-codes"></a>WebSocket-felkoder

| WebSocketsStatus kod | Beskrivning | Felsökning |
| - | - | - |
| 1000 normala kommer att avslutas | Tjänsten stängs WebSocket-anslutning utan fel. | Om WebSocket-avslutas oväntat, Läs i dokumentationen för att säkerställa att du förstår hur och när tjänsten kan avsluta WebSocket-anslutning. |
| 1002 protokollfel | Det gick inte att följa kraven för protokollet för klienten. | Se till att du förstår dokumentationen för protokollet och är tydliga om kraven. Läs föregående dokumentationen om fel skäl att se om du brott mot protokollkrav. |
| Ogiltig nyttolast för 1007 Data | Klienten skickade en ogiltig nyttolast i ett protokollmeddelande. | Kontrollera det sista meddelandet som skickades till tjänsten efter fel. Läs föregående dokumentationen om nyttolastfel. |
| 1011 serverfel | Tjänsten påträffade ett internt fel och det gick inte att utföra den begärda åtgärden. | I de flesta fall är det här felet är tillfälligt. Gör om begäran. |

## <a name="related-topics"></a>Relaterade ämnen

Se en [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) som är en implementering av protokollet WebSocket-baserad tal-tjänst.
