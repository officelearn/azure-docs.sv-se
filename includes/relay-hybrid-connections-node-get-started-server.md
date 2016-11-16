### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
* Skapa en ny JavaScript-fil som kallas `listener.js`.

### <a name="add-the-relay-npm-package"></a>Lägg till Relay NPM-paketet
* Kör `npm install hyco-ws` från Kommandotolken för en nod i projektmappen.

### <a name="write-some-code-to-receive-messages"></a>Skriva kod för att ta emot meddelanden
1. Lägg till följande `constant` överst i `listener.js`-filen.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Lägg till följande Relay `constants` till `listener.js` för Hybridanslutningens anslutningsinformation. Ersätt platshållare inom hakparentes med rätt värden som erhölls när du skapade Hybridanslutningen.
   
   1. `const ns` – Relay-namnområde
   2. `const path` – Namnet på Hybridanslutningen
   3. `const keyrule` – Namnet på SAS-nyckeln
   4. `const key` – SAS-nyckelvärdet
3. Lägg till följande kod i `listener.js`-filen:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Så här bör din listener.js bör se ut:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```



<!--HONumber=Nov16_HO2-->


