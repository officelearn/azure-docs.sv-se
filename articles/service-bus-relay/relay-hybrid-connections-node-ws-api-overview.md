---
title: 'Översikt över Azure Relay nod-API: er | Microsoft Docs'
description: Vidarebefordra nod API-översikt
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 696e3f77a283cc31d3c8f6007a839480ae8eb984
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019472"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Översikt över Hybrid anslutningar nod API-relä

## <a name="overview"></a>Översikt

Den [ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) nod paketet för Azure Relay Hybridanslutningar bygger på och utökar den ['ws'](https://www.npmjs.com/package/ws) NPM-paketet. Det här paketet igen exporterar alla export av det grundläggande paketet och lägger till nya export som möjliggör integrering med funktionen Azure vidarebefordrande tjänsten Hybridanslutningar. 

Befintliga program som `require('ws')` kan använda det här paketet med `require('hyco-ws')` i stället som kan också hybridscenarier där ett program kan lyssna efter WebSocket-anslutningar från ”innanför brandväggen” lokalt och via Hybridanslutningar, på samma gång.
  
## <a name="documentation"></a>Dokumentation

API: erna är [dokumenterade i paketets huvudsakliga ws](https://github.com/websockets/ws/blob/master/doc/ws.md). Den här artikeln beskriver hur det här paketet skiljer sig från den baslinjen. 

De viktigaste skillnaderna mellan grundläggande paketet och den här 'hyco-ws' är det lägger till en ny serverklass som exporteras via `require('hyco-ws').RelayedServer`, och några hjälpmetoder.

### <a name="package-helper-methods"></a>Paketet hjälpmetoder

Det finns flera metoder för export paketet som du kan referera till enligt följande:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Helper-metoder för användning med det här paketet, men kan också användas av en nod-server för att aktivera webb- eller klienter att skapa lyssnare eller avsändare. Servern använder dessa metoder genom att skicka dem till URI: er som bädda in tillfällig token. Dessa URI: er kan också användas med vanliga WebSocket-stackar som inte stöder inställningen HTTP-huvuden för WebSocket-handskakning. Bädda in auktorisering token i URI: N stöds i första hand för dessa bibliotek externa Användningsscenarier. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Skapar en giltig Azure Relay Hybridanslutning-lyssnare URI för det angivna namnområdet och sökvägen. Den här URI kan sedan användas med klassen WebSocketServer relay-versionen.

- `namespaceName`(obligatoriskt) - domän kvalificerade namnet på Azure Relay-namnområde som ska användas.
- `path`(obligatoriskt) - namnet på en befintlig Azure Relay Hybrid-anslutning i detta namnområde.
- `token`(valfritt) – en Relay-åtkomst som tidigare utfärdade token som är inbäddad i lyssnaren URI (se följande exempel).
- `id`(valfritt) – ett spårnings-ID som möjliggör slutpunkt till slutpunkt diagnostik uppföljning av begäranden.

Den `token` värdet är valfritt och bör endast användas när det inte går att skicka HTTP-huvuden tillsammans med WebSocket-handskakning som är fallet med W3C WebSocket-stacken.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Skapar en giltig Azure Relay Hybridanslutning-skicka URI för det angivna namnområdet och sökvägen. Den här URI kan användas med WebSocket-klienten.

- `namespaceName`(obligatoriskt) - domän kvalificerade namnet på Azure Relay-namnområde som ska användas.
- `path`(obligatoriskt) - namnet på en befintlig Azure Relay Hybrid-anslutning i detta namnområde.
- `token`(valfritt) – en Relay-åtkomst som tidigare utfärdade token som är inbäddad i Skicka URI (se följande exempel).
- `id`(valfritt) – ett spårnings-ID som möjliggör slutpunkt till slutpunkt diagnostik uppföljning av begäranden.

Den `token` värdet är valfritt och bör endast användas när det inte går att skicka HTTP-huvuden tillsammans med WebSocket-handskakning som är fallet med W3C WebSocket-stacken.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Skapar ett Azure Relay delade signatur åtkomst (SAS)-token för angivna mål-URI, SAS-regeln och SAS regeln nyckel som är giltig för det angivna antalet sekunder eller en timme från aktuell om detta utelämnas används argumentet upphör att gälla.

- `uri`(obligatoriskt) - URI: N som är token utfärdas. URI: N är normaliserat om du vill använda HTTP-schemat och läsa sträng information tas bort.
- `ruleName`(krävs) - Regelnamn SAS för antingen det entitet som representeras av den angivna URI: N eller för det namnområde som representeras av värddelen URI.
- `key`(krävs) - giltig nyckel för SAS-regeln. 
- `expirationSeconds`(valfritt) – antal sekunder innan den genererade token ska upphöra att gälla. Om den inte anges är standardvärdet 1 timme (3600).

Utfärdad token ger de rättigheter som associeras med den angivna regeln för SAS för den angivna varaktigheten.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Den här metoden fungerar som den `createRelayToken` returnerar metoden som beskrivs tidigare, men den token som ska läggas till för indata-URI.

### <a name="class-wsrelayedserver"></a>Klassen ws. RelayedServer

Den `hycows.RelayedServer` klass är ett alternativ till att den `ws.Server` klass som inte lyssnar på nätverket, men delegater som lyssnar på den Azure vidarebefordrande tjänsten.

Två klasser är främst kontraktet som är kompatibla, vilket innebär att en befintlig programmet med den `ws.Server` klassen kan enkelt ändras för att använda den vidarebefordrande versionen. De viktigaste skillnaderna är i konstruktorn och de tillgängliga alternativen.

#### <a name="constructor"></a>Konstruktorn  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

Den `RelayedServer` konstruktorn stöder en annan uppsättning argument än den `Server`, eftersom den inte är en fristående lyssnare eller kan vara inbäddat i ett befintligt http-lyssnaren ramverk. Det finns också färre alternativ eftersom WebSocket-management är i stort sett delegerat till den vidarebefordrande tjänsten.

Konstruktorargument:

- `server`(krävs) - konstruerade fullständigt kvalificerade URI för en Hybridanslutning namn som ska övervakas, vanligtvis med hjälpmetoden WebSocket.createRelayListenUri().
- `token`(krävs) - innehåller det här argumentet en tidigare utfärdade token sträng eller en motringning funktion som kan användas för att hämta en token sträng. Alternativet är att föredra, eftersom det token förnyelse.

#### <a name="events"></a>Händelser

`RelayedServer`instanser genererar tre händelser som hjälper dig att hantera inkommande begäranden, upprätta anslutningar och identifiera felvillkor. Måste du prenumerera på `connect` händelsen för att hantera meddelanden. 

##### <a name="headers"></a>rubriker

```JavaScript 
function(headers)
```

Den `headers` händelse utlöses innan en inkommande anslutning är godkända att aktivera ändringen av huvuden som skickas till klienten. 

##### <a name="connection"></a>anslutning

```JavaScript
function(socket)
```

Orsakat när en ny WebSocket-anslutning accepteras. Objektet är av typen `ws.WebSocket`, samma som med grundläggande paketet.


##### <a name="error"></a>fel

```JavaScript
function(error)
```

Om den underliggande servern genererar ett fel, skickas den här.  

#### <a name="helpers"></a>Hjälpprogram

För att förenkla vidarebefordrande server startas och omedelbart prenumerera på inkommande anslutningar, visar paketet en enkel hjälpfunktion som också används i exemplen, enligt följande:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Den här metoden anropar konstruktorn för att skapa en ny instans av RelayedServer och prenumererar på angivna återanropet på händelsen 'anslutningen'.
 
##### <a name="relayedconnect"></a>relayedConnect

Spegling bara den `createRelayedServer` helper i funktionen `relayedConnect` skapar en klientanslutning och prenumererar på händelsen 'öppen' på den resulterande socketen.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Relay finns i följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Tillgängliga Relay-API: er](relay-api-overview.md)
