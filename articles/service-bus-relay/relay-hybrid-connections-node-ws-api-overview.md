---
title: Översikt över AZURE Relay Node API:er | Microsoft-dokument
description: Den här artikeln innehåller en översikt över Api:et node.js för Azure Relay-tjänsten. Den visar också hur du använder hyco-ws Node-paketet.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 2877284c419da4999e23490fc986e5da44e5d92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514518"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Api-överblick över Relay Hybrid Connections Nod

## <a name="overview"></a>Översikt

[`hyco-ws`](https://www.npmjs.com/package/hyco-ws) Node-paketet för Azure Relay Hybrid-anslutningar bygger på och utökar [NPM-paketet ws.](https://www.npmjs.com/package/ws) Det här paketet återexporterar all export av baspaketet och lägger till ny export som möjliggör integrering med Azure Relay-tjänsten Hybrid-anslutningar. 

Befintliga program `require('ws')` som kan `require('hyco-ws')` använda det här paketet med i stället, vilket också möjliggör hybridscenarier där ett program kan lyssna efter WebSocket-anslutningar lokalt från "inne i brandväggen" och via Hybridanslutningar, alla på samma gång.
  
## <a name="documentation"></a>Dokumentation

API:erna [dokumenteras i huvudpaketet "ws".](https://github.com/websockets/ws/blob/master/doc/ws.md) I den här artikeln beskrivs hur det här paketet skiljer sig från den baslinjen. 

De viktigaste skillnaderna mellan baspaketet och denna "hyco-ws" är att den `require('hyco-ws').RelayedServer`lägger till en ny serverklass, exporteras via , och några hjälpmetoder.

### <a name="package-helper-methods"></a>Metoder för pakethjälp

Det finns flera verktygsmetoder som finns tillgängliga på paketexporten som du kan referera till på följande sätt:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Hjälpmetoderna är till för användning med det här paketet, men kan också användas av en nodserver för att aktivera webb- eller enhetsklienter för att skapa lyssnare eller avsändare. Servern använder dessa metoder genom att skicka dem URI:er som bäddar in kortlivade token. Dessa URI:er kan också användas med vanliga WebSocket-stackar som inte stöder inställning av HTTP-huvuden för WebSocket-handskakningen. Inbäddning av auktoriseringstoken i URI:n stöds främst för de biblioteksutkrena användningsscenarier. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Skapar en giltig Azure Relay Hybrid Connection lyssnare URI för det angivna namnområdet och sökvägen. Den här URI:n kan sedan användas med reläversionen av klassen WebSocketServer.

- `namespaceName`(obligatoriskt) - det domänkvalificerade namnet på Azure Relay-namnområdet som ska användas.
- `path`(obligatoriskt) - namnet på en befintlig Azure Relay Hybrid-anslutning i det namnområdet.
- `token`(valfritt) - en tidigare utfärdad Relay access-token som är inbäddad i lyssnaren URI (se följande exempel).
- `id`(valfritt) - en spårningsidentifierare som möjliggör heltäckande diagnostikspårning av begäranden.

Värdet `token` är valfritt och bör endast användas när det inte är möjligt att skicka HTTP-huvuden tillsammans med WebSocket-handskakningen, vilket är fallet med W3C WebSocket-stacken.                  


#### <a name="createrelaysenduri"></a>skapaRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Skapar en giltig Azure Relay Hybrid Connection skicka URI för det angivna namnområdet och sökvägen. Denna URI kan användas med alla WebSocket-klienter.

- `namespaceName`(obligatoriskt) - det domänkvalificerade namnet på Azure Relay-namnområdet som ska användas.
- `path`(obligatoriskt) - namnet på en befintlig Azure Relay Hybrid-anslutning i det namnområdet.
- `token`(valfritt) - en tidigare utfärdad Relay access-token som är inbäddad i skicka URI (se följande exempel).
- `id`(valfritt) - en spårningsidentifierare som möjliggör heltäckande diagnostikspårning av begäranden.

Värdet `token` är valfritt och bör endast användas när det inte är möjligt att skicka HTTP-huvuden tillsammans med WebSocket-handskakningen, vilket är fallet med W3C WebSocket-stacken.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Skapar en AZURE Relay Shared Access Signature (SAS) token för den angivna mål-URI-, SAS-regeln och SAS-regelnyckeln som är giltig för det angivna antalet sekunder eller i en timme från det aktuella ögonblicket om argumentet för utgångsdatum utelämnas.

- `uri`(obligatoriskt) - den URI för vilken token ska utfärdas. URI:et normaliseras för att använda HTTP-schemat och frågestränginformation tas bort.
- `ruleName`(obligatoriskt) - SAS-regelnamn för antingen den enhet som representeras av den angivna URI:n eller för namnområdet som representeras av URI-värddelen.
- `key`(obligatoriskt) - giltig nyckel för SAS-regeln. 
- `expirationSeconds`(valfritt) - antalet sekunder tills den genererade token ska upphöra att gälla. Om inget anges är standardvärdet 1 timme (3600).

Den utfärdade token ger de rättigheter som är associerade med den angivna SAS-regeln för den angivna varaktigheten.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Den här metoden är `createRelayToken` funktionellt likvärdig med den metod som dokumenterats tidigare, men returnerar token korrekt bifogad till indata-URI.

### <a name="class-wsrelayedserver"></a>Klass ws. Relässerver

Klassen `hycows.RelayedServer` är ett alternativ `ws.Server` till den klass som inte lyssnar på det lokala nätverket, men ombud som lyssnar på Azure Relay-tjänsten.

De två klasserna är oftast kontraktskompatibla, vilket innebär att ett befintligt program som använder `ws.Server` klassen enkelt kan ändras för att använda den vidarebefordrade versionen. De största skillnaderna finns i konstruktorn och i de tillgängliga alternativen.

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

Konstruktorn `RelayedServer` stöder en annan uppsättning `Server`argument än , eftersom den inte är en fristående lyssnare eller kan bäddas in i ett befintligt HTTP-lyssnareramverk. Det finns också färre alternativ tillgängliga eftersom WebSocket-hanteringen till stor del delegeras till relay-tjänsten.

Konstruktorargument:

- `server`(obligatoriskt) - den fullt kvalificerade URI för ett hybridanslutningsnamn som du kan lyssna på, vanligtvis konstruerat med hjälpmetoden WebSocket.createRelayListenUri().
- `token`(obligatoriskt) - det här argumentet innehåller antingen en tidigare utfärdad tokensträng eller en motringningsfunktion som kan anropas för att hämta en sådan tokensträng. Motringningsalternativet är att föredra eftersom det aktiverar tokenförnyelse.

#### <a name="events"></a>Händelser

`RelayedServer`instanser avger tre händelser som gör att du kan hantera inkommande begäranden, upprätta anslutningar och identifiera feltillstånd. Du måste prenumerera `connect` på händelsen för att hantera meddelanden. 

##### <a name="headers"></a>Headers

```JavaScript 
function(headers)
```

Händelsen `headers` utlöses strax innan en inkommande anslutning accepteras, vilket gör att ändringar av rubrikerna kan skickas till klienten. 

##### <a name="connection"></a>anslutning

```JavaScript
function(socket)
```

Avges när en ny WebSocket-anslutning accepteras. Objektet är av `ws.WebSocket`typ , samma som med baspaketet.


##### <a name="error"></a>fel

```JavaScript
function(error)
```

Om den underliggande servern avger ett fel vidarebefordras det här.  

#### <a name="helpers"></a>Hjälpare

För att förenkla starten av en vidarebefordrad server och omedelbart prenumerera på inkommande anslutningar, exponerar paketet en enkel hjälpfunktion, som också används i exemplen, enligt följande:

##### <a name="createrelayedlistener"></a>skapaRelayedListener

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

##### <a name="createrelayedserver"></a>skapaRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Den här metoden anropar konstruktorn för att skapa en ny instans av RelayedServer och prenumererar sedan på den angivna motringningen till händelsen "anslutning".
 
##### <a name="relayedconnect"></a>vidarebefordrasAnslutning

Helt enkelt `createRelayedServer` speglar hjälparen `relayedConnect` i funktion, skapar en klientanslutning och prenumererar på "öppna" händelsen på den resulterande uttaget.

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
* [Tillgängliga relay-API:er](relay-api-overview.md)
