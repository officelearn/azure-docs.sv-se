### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
* Skapa en ny JavaScript-fil som kallas `sender.js`.

### <a name="add-the-relay-npm-package"></a>Lägg till Relay NPM-paketet
* Kör `npm install hyco-ws` från Kommandotolken för en nod i projektmappen.

### <a name="write-some-code-to-send-messages"></a>Skriva kod för att skicka meddelanden
1. Lägg till följande `constants` överst i `sender.js`-filen.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Lägg till följande Relay `constants` till `sender.js` för Hybridanslutningens anslutningsinformation. Ersätt platshållare inom hakparentes med rätt värden som erhölls när du skapade Hybridanslutningen.
   
   1. `const ns` – Relay-namnområde
   2. `const path` – Namnet på Hybridanslutningen
   3. `const keyrule` – Namnet på SAS-nyckeln
   4. `const key` – SAS-nyckelvärdet
3. Lägg till följande kod i `sender.js`-filen:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    Så här bör din listener.js bör se ut:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```



<!--HONumber=Nov16_HO2-->


