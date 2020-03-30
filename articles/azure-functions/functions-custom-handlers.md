---
title: Anpassade hanterare av Azure Functions (förhandsversion)
description: Lär dig att använda Azure-funktioner med valfritt språk eller körningsversion.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479259"
---
# <a name="azure-functions-custom-handlers-preview"></a>Anpassade hanterare av Azure Functions (förhandsversion)

Varje funktionsapp körs av en språkspecifik hanterare. Azure Functions stöder många [språkhanterare](./supported-languages.md) som standard, men det finns fall där du kanske vill ha ytterligare kontroll över appkörningsmiljön. Anpassade hanterare ger dig den här extra kontrollen.

Anpassade hanterare är lätta webbservrar som tar emot händelser från functions-värden. Alla språk som stöder HTTP-primitiver kan implementera en anpassad hanterare.

Anpassade hanterare är bäst lämpade för situationer där du vill:

- Implementera en funktionsapp på ett språk utöver de språk som stöds officiellt
- Implementera en functions-app i en språkversion eller körning som inte stöds som standard
- Ha detaljerad kontroll över appkörningsmiljön

Med anpassade hanterare stöds alla [utlösare och in- och utdatabindningar](./functions-triggers-bindings.md) via [tilläggspaket](./functions-bindings-register.md).

## <a name="overview"></a>Översikt

I följande diagram visas förhållandet mellan programledaren Funktioner och en webbserver som implementerats som en anpassad hanterare.

![Översikt över anpassad hanterare i Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Händelser utlöser en begäran som skickas till functions-värden. Händelsen medför antingen en rå HTTP-nyttolast (för HTTP-utlösta funktioner utan bindningar) eller en nyttolast som innehåller indatabindningsdata för funktionen.
- Functions-värden proxyserar sedan begäran till webbservern genom att utfärda en [begäran nyttolast](#request-payload).
- Webbservern kör den enskilda funktionen och returnerar en [svarsnyttolast](#response-payload) till functions-värden.
- Functions-värden proxyservrar svaret som en utdatabindningstillalast till målet.

En Azure Functions-app som implementeras som en anpassad hanterare måste konfigurera *host.json-* och *function.json-filerna* enligt några konventioner.

## <a name="application-structure"></a>Applikationsstruktur

Om du vill implementera en anpassad hanterare behöver du följande aspekter i ditt program:

- En *host.json-fil* i appens rot
- En *function.json-fil* för varje funktion (inuti en mapp som matchar funktionsnamnet)
- Ett kommando, skript eller körbart skript som kör en webbserver

Följande diagram visar hur dessa filer ser ut på filsystemet för en funktion som heter "ordning".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Konfiguration

Programmet konfigureras via *filen host.json.* Den här filen talar om för programledaren för funktioner var begäranden ska skickas genom att peka på en webbserver som kan bearbeta HTTP-händelser.

En anpassad hanterare definieras genom att konfigurera *filen host.json* med information om `httpWorker` hur du kör webbservern via avsnittet .

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

Avsnittet `httpWorker` pekar på ett mål `defaultExecutablePath`som definieras av . Körningsmålet kan antingen vara ett kommando, en körbar fil eller en fil där webbservern implementeras.

För skriptappar `defaultExecutablePath` pekar du på skriptspråkets `defaultWorkerPath` körning och pekar på skriptfilens plats. I följande exempel visas hur en JavaScript-app i Node.js är konfigurerad som en anpassad hanterare.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Du kan också skicka `arguments` argument med hjälp av matrisen:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Argument är nödvändiga för många felsökningsinställningar. Mer information finns i avsnittet [Felsökning.](#debugging)

> [!NOTE]
> *Host.json-filen* måste vara på samma nivå i katalogstrukturen som den webbserver som körs. Vissa språk och verktygskedjor kanske inte placerar filen i programroten som standard.

#### <a name="bindings-support"></a>Stöd för bindningar

Standardutlösare tillsammans med indata- och utdatabindningar är tillgängliga genom att referera till [tilläggspaket](./functions-bindings-register.md) i filen *host.json.*

### <a name="function-metadata"></a>Funktionsmetadata

När det används med en anpassad hanterare skiljer sig *funktion.json-innehållet* inte från hur du definierar en funktion under någon annan kontext. Det enda kravet är att *function.json-filer* måste finnas i en mapp med namnet för att matcha funktionsnamnet.

### <a name="request-payload"></a>Begär nyttolast

Begäran nyttolast för rena HTTP-funktioner är rå HTTP begäran nyttolast. Rena HTTP-funktioner definieras som funktioner utan indata- eller utdatabindningar, som returnerar ett HTTP-svar.

Alla andra typer av funktioner som inkluderar antingen indata, utdatabindningar eller utlöses via en annan händelsekälla än HTTP har en anpassad begäran nyttolast.

Följande kod representerar en nyttolast för exempelbegäran. Nyttolasten innehåller en JSON-struktur `Data` `Metadata`med två medlemmar: och .

Medlemmen `Data` innehåller nycklar som matchar indata och utlösarnamn enligt definitionen i bindningsmatrisen i *filen function.json.*

Medlemmen `Metadata` innehåller [metadata som genereras från händelsekällan](./functions-bindings-expressions-patterns.md#trigger-metadata).

Med tanke på bindningarna som definieras i följande *function.json-fil:*

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

En nyttolast för begäran som liknar det här exemplet returneras:

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>Nyttolast för svar

Efter konvention formateras funktionssvaren som nyckel-/värdepar. Nycklar som stöds inkluderar:

| <nobr>Nyttolastnyckel</nobr>   | Datatyp | Anmärkningar                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Innehåller svarsvärden som `bindings` definieras av matrisen *filen function.json.*<br /><br />Till exempel om en funktion är konfigurerad med en blob lagringsbelödningsbindning med namnet "blob", innehåller du `Outputs` en nyckel med namnet `blob`, som är inställd på blobens värde. |
| `Logs`        | matris     | Meddelanden visas i funktionskallningsloggarna.<br /><br />När du kör i Azure visas meddelanden i Application Insights. |
| `ReturnValue` | sträng    | Används för att ge ett svar `$return` när en utdata har konfigurerats som i *filen function.json.* |

Se [exemplet för en provnyttolast](#bindings-implementation).

## <a name="examples"></a>Exempel

Anpassade hanterare kan implementeras på alla språk som stöder HTTP-händelser. Medan Azure Functions [har fullt stöd för JavaScript och Node.js](./functions-reference-node.md)visas följande exempel hur du implementerar en anpassad hanterare med JavaScript i Node.js i instruktionssyfte.

> [!TIP]
> När du är en guide för att lära dig hur du implementerar en anpassad hanterare på andra språk kan de Node.js-baserade exemplen som visas här också vara användbara om du vill köra en functions-app i en version av Node.js som inte stöds.

## <a name="http-only-function"></a>Funktionen ENDAST HTTP

I följande exempel visas hur du konfigurerar en HTTP-utlöst funktion utan ytterligare bindningar eller utdata. Scenariot som implementeras i det `http` här exemplet `GET` `POST` har en funktion med namnet som accepterar en eller .

Följande kodavsnitt representerar hur en begäran till funktionen består.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementering

I en mapp med namnet *http*konfigurerar *filen function.json* funktionen HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Funktionen är konfigurerad för `GET` `POST` att acceptera båda och begäranden `res`och resultatvärdet tillhandahålls via ett argument med namnet .

Vid appens rot är *filen host.json* konfigurerad för att köra Node.js och peka på `server.js` filen.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Filen *server.js* implementerar en webbserver och HTTP-funktion.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

I det här exemplet används Express för att skapa en webbserver för `FUNCTIONS_HTTPWORKER_PORT`att hantera HTTP-händelser och är inställd på att lyssna efter begäranden via .

Funktionen definieras på sökvägen `/hello`till . `GET`begäranden hanteras genom att returnera ett `POST` enkelt JSON-objekt `req.body`och begäranden har tillgång till begäran via .

Vägen för orderfunktionen här `/hello` är `/api/hello` och inte för att functions-värden proxyerar begäran till den anpassade hanteraren.

>[!NOTE]
>Den `FUNCTIONS_HTTPWORKER_PORT` är inte den offentliga inför porten som används för att anropa funktionen. Den här porten används av functions-värden för att anropa den anpassade hanteraren.

## <a name="function-with-bindings"></a>Funktion med bindningar

Scenariot som implementeras i det `order` här exemplet `POST` har en funktion som heter som accepterar en med en nyttolast som representerar en produktorder. När en order bokförs till funktionen skapas ett kölagringsmeddelande och ett HTTP-svar returneras.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Implementering

I en mapp med namnet ordning konfigurerar *filen function.json* funktionen HTTP.In a folder named *order.*

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

Den här funktionen definieras som en [HTTP-utlöst funktion](./functions-bindings-http-webhook-trigger.md) som returnerar ett [HTTP-svar](./functions-bindings-http-webhook-output.md) och matar ut ett [kölagringsmeddelande.](./functions-bindings-storage-queue-output.md)

Vid appens rot är *filen host.json* konfigurerad för att köra Node.js och peka på `server.js` filen.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Filen *server.js* implementerar en webbserver och HTTP-funktion.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

I det här exemplet används Express för att skapa en webbserver för `FUNCTIONS_HTTPWORKER_PORT`att hantera HTTP-händelser och är inställd på att lyssna efter begäranden via .

Funktionen definieras på sökvägen `/order` till .  Vägen för orderfunktionen här `/order` är `/api/order` och inte för att functions-värden proxyerar begäran till den anpassade hanteraren.

När `POST` begäranden skickas till den här funktionen exponeras data via några punkter:

- Organet för begäran är tillgängligt via`req.body`
- De data som publiceras på funktionen är tillgängliga via`req.body.Data.req.Body`

Funktionens svar formateras i ett nyckel-/värdepar `Outputs` där medlemmen har ett JSON-värde där nycklarna matchar utdata enligt definitionen i *filen function.json.*

Genom `message` att ställa in samma som meddelandet `res` som kom in från begäran och det förväntade HTTP-svaret matar den här funktionen ut ett meddelande till Kölagring och returnerar ett HTTP-svar.

## <a name="debugging"></a>Felsökning

Om du vill felsöka den anpassade hanterarens funktionsapp måste du lägga till argument som är lämpliga för språket och körningen för att aktivera felsökning.

Om du till exempel vill felsöka ett `--inspect` Node.js-program skickas flaggan som ett argument i *filen host.json.*

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> Felsökningskonfigurationen är en del av filen *host.json,* vilket innebär att du kan behöva ta bort vissa argument innan du distribuerar till produktion.

Med den här konfigurationen kan du starta funktionens värdprocess med följande kommando:

```bash
func host start
```

När processen har startats kan du bifoga en felsökning och träffa brytpunkter.

### <a name="visual-studio-code"></a>Visual Studio-koden

Följande exempel är en exempelkonfiguration som visar hur du kan konfigurera *filen launch.json* för att ansluta appen till felsökningsfelsökaren för Visual Studio-kod.

Det här exemplet är för Node.js, så du kan behöva ändra det här exemplet för andra språk eller runtimes.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Distribuera

En anpassad hanterare kan distribueras till nästan alla Azure Functions-värdalternativ (se [begränsningar](#restrictions)). Om hanteraren kräver anpassade beroenden (till exempel en språkkörning) kan du behöva använda en [anpassad behållare](./functions-create-function-linux-custom-image.md).

## <a name="restrictions"></a>Begränsningar

- Anpassade hanterare stöds inte i Linux-förbrukningsplaner.
- Webbservern måste starta inom 60 sekunder.

## <a name="samples"></a>Exempel

Se de [anpassade hanterarexempel GitHub-repoerna](https://github.com/Azure-Samples/functions-custom-handlers) för exempel på hur du implementerar funktioner på en mängd olika språk.
