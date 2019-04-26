---
title: 'Översikt över API: er för Azure Relay noden | Microsoft Docs'
description: Vidarebefordra noden API-översikt
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 794e797e504d6064c13ffe0a4ed131e668d86e97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421615"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Relä Hybrid anslutningar noden API-översikt

## <a name="overview"></a>Översikt

Den [ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) Node package för Azure Relay-Hybridanslutningar som bygger på och utökar den ['ws'](https://www.npmjs.com/package/ws) NPM-paket. Det här paketet igen exporterar all export av det grundläggande paketet och lägger till nya export som möjliggör integrering med funktionen Hybrid Connections Azure Relay-tjänsten. 

Befintliga program som `require('ws')` kan använda det här paketet med `require('hyco-ws')` i stället som kan också hybridscenarier som ett program kan lyssna efter WebSocket anslutningar lokalt från ”i brandväggen” och via Hybridanslutningar, allt på samtidigt.
  
## <a name="documentation"></a>Dokumentation

API: er [dokumenteras i paketets huvudsakliga ws](https://github.com/websockets/ws/blob/master/doc/ws.md). Den här artikeln beskriver hur det här paketet skiljer sig från den baslinjen. 

De viktigaste skillnaderna mellan grundläggande paketet och det här 'hyco-ws' är det lägger till en ny serverklass, exporteras via `require('hyco-ws').RelayedServer`, och några hjälpmetoder.

### <a name="package-helper-methods"></a>Paketet hjälpmetoder

Det finns flera metoder för paketet export som du kan referera till enligt följande:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Hjälpmetoder är för användning med det här paketet, men kan också användas av en nod-server för att aktivera webb- eller enhet klienter att skapa lyssnare eller avsändare. Servern använder dessa metoder genom att skicka dem till URI: er som tillfällig inbäddningstoken. Dessa URI: er kan också användas med vanliga WebSocket-stackar som inte stöder inställningen för HTTP-huvuden för WebSocket-handskakning. Bädda in auktoriseringstoken i URI: N stöds i första hand för dessa bibliotek externa Användningsscenarier. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Skapar en giltig Hybridanslutning för Azure Relay-lyssnare URI för den angivna namnområdet och sökvägen. URI: N kan sedan användas med klassen WebSocketServer relay-versionen.

- `namespaceName` (obligatoriskt) - domän kvalificerade namnet på Azure Relay-namnområde som ska användas.
- `path` (krävs) – namnet på en befintlig Azure Relay Hybrid-anslutning i det namnområdet.
- `token` (valfritt) – en tidigare utfärdat Relay åtkomst-token som är inbäddad i lyssnaren URI: N (se exemplet nedan).
- `id` (valfritt) – en spårnings-ID som aktiverar diagnostik för slutpunkt till slutpunkt-spårning av förfrågningar.

Den `token` värdet är valfritt och bör endast användas när det inte går att skicka HTTP-huvuden tillsammans med WebSocket-handskakning så är fallet med W3C WebSocket-stack.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Skapar Hybridanslutning för Azure Relay giltig skicka URI för den angivna namnområdet och sökvägen. URI: N kan användas med alla WebSocket-klienter.

- `namespaceName` (obligatoriskt) - domän kvalificerade namnet på Azure Relay-namnområde som ska användas.
- `path` (krävs) – namnet på en befintlig Azure Relay Hybrid-anslutning i det namnområdet.
- `token` (valfritt) – en tidigare utfärdat Relay åtkomst-token som är inbäddad i Skicka URI: N (se exemplet nedan).
- `id` (valfritt) – en spårnings-ID som aktiverar diagnostik för slutpunkt till slutpunkt-spårning av förfrågningar.

Den `token` värdet är valfritt och bör endast användas när det inte går att skicka HTTP-huvuden tillsammans med WebSocket-handskakning så är fallet med W3C WebSocket-stack.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Skapar en Azure Relay signatur för delad åtkomst (SAS) token för angivna mål-URI-, SAS-regel- och SAS-nyckel för regeln som är giltig för det angivna antalet sekunder eller i en timme från aktuell om detta utelämnas används argumentet slutar att gälla.

- `uri` (obligatoriskt) - URI: N som är token utfärdas. URI: N är normaliserade om du vill använda HTTP-schemat och läsa sträng information tas bort.
- `ruleName` (krävs) - Regelnamn SAS för antingen den entitet som representeras av den angivna URI: N eller för det namnområde som representeras av värddelen URI.
- `key` (krävs) – giltig nyckel för SAS-regeln. 
- `expirationSeconds` (valfritt) – hur många sekunder tills den genererade token ska upphöra att gälla. Om inte anges är standardvärdet 1 timme (3600).

Utfärdad token ger de rättigheter som är associerade med den angivna SAS-regeln för den angivna perioden.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Den här metoden fungerar som den `createRelayToken` returnerar metoden som beskrevs tidigare, men den token som ska läggas till för indata-URI.

### <a name="class-wsrelayedserver"></a>Klass ws. RelayedServer

Den `hycows.RelayedServer` klass är ett alternativ till den `ws.Server` klass som inte lyssnar på lokalt nätverk, men delegerar lyssna på Azure Relay-tjänsten.

Två klasser är främst kontraktet som är kompatibel, vilket innebär att ett befintligt program med hjälp av den `ws.Server` klassen kan enkelt ändras om du vill använda den vidarebefordrande versionen. De viktigaste skillnaderna är i konstruktorn och i de tillgängliga alternativen.

#### <a name="constructor"></a>Konstruktor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

Den `RelayedServer` konstruktor stöder en annan uppsättning argument än den `Server`, eftersom det inte är en fristående lyssnare, eller kan bäddas in i ett ramverk för befintliga HTTP-lyssnare. Det finns också färre alternativ eftersom WebSocket-hanteringsprincip huvudsakligen delegeras till den vidarebefordrande tjänsten.

Konstruktorn argument:

- `server` (krävs) – konstruerats den fullständigt kvalificerade URI för en Hybridanslutning som ska övervakas, vanligtvis med hjälpmetoden WebSocket.createRelayListenUri().
- `token` Det här argumentet innehåller (krävs) – en tidigare utfärdade token sträng eller en återanropningsfunktion som kan användas för att hämta en token sträng. Alternativet är att föredra, eftersom det ger token förnyelse.

#### <a name="events"></a>Händelser

`RelayedServer` instanser generera tre händelser som hjälper dig att hantera inkommande begäranden, upprätta anslutningar och identifiera felvillkor. Du måste prenumerera på den `connect` händelse kan hantera meddelanden. 

##### <a name="headers"></a>Rubriker

```JavaScript 
function(headers)
```

Den `headers` inträffar händelsen precis innan en inkommande anslutning har accepterats, aktiverar ändring av huvuden som skickas till klienten. 

##### <a name="connection"></a>anslutning

```JavaScript
function(socket)
```

Genereras när en ny WebSocket-anslutning accepteras. Objektet är av typen `ws.WebSocket`, samma som med grundläggande paketet.


##### <a name="error"></a>fel

```JavaScript
function(error)
```

Om den underliggande servern genererar ett fel, skickas det här.  

#### <a name="helpers"></a>Hjälpprogram

För att förenkla börjar en vidarebefordrande server och direkt prenumerera på inkommande anslutningar, visar paketet en enkel hjälpfunktionen, som också används i exemplen, enligt följande:

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

Den här metoden anropas konstruktorn för att skapa en ny instans av RelayedServer och sedan prenumererar motringningen till ”anslutning”-händelse.
 
##### <a name="relayedconnect"></a>relayedConnect

Spegling bara den `createRelayedServer` helper i funktionen `relayedConnect` skapar en klientanslutning och prenumererar på händelsen ”öppna” för den resulterande socketen.

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
* [Tillgängliga Relay API: er](relay-api-overview.md)
