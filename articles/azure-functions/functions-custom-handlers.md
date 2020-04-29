---
title: Azure Functions anpassade hanterare (förhands granskning)
description: Lär dig hur du använder Azure Functions med valfri språk-eller körnings version.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479259"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions anpassade hanterare (förhands granskning)

Varje Functions-App körs av en språkspecifik hanterare. Även om Azure Functions stöder många [språk hanterare](./supported-languages.md) som standard, finns det fall där du kanske vill ha ytterligare kontroll över appens körnings miljö. Anpassade hanterare ger dig den här ytterligare kontrollen.

Anpassade hanterare är lätta webb servrar som tar emot händelser från Functions-värden. Alla språk som stöder HTTP-primitiver kan implementera en anpassad hanterare.

Anpassade hanterare passar bäst för situationer där du vill:

- Implementera en Functions-app på ett språk som ligger utanför de språk som stöds
- Implementera en Functions-app i en språk version eller runtime stöds inte som standard
- Få detaljerad kontroll över appens körnings miljö

Med anpassade hanterare stöds alla [utlösare och indata-och utgående bindningar](./functions-triggers-bindings.md) via [tilläggs paket](./functions-bindings-register.md).

## <a name="overview"></a>Översikt

I följande diagram visas relationen mellan Functions-värden och en webb server som implementerats som en anpassad hanterare.

![Översikt över Azure Functions anpassad hanterare](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Händelser utlöser en begäran som skickas till Functions-värden. Händelsen har antingen en RAW HTTP-nyttolast (för HTTP-utlösta funktioner utan bindningar) eller en nytto last som innehåller data för inkommande bindning för funktionen.
- Funktions värden skickar sedan begäran till webb servern genom att utfärda en [nytto last för begäran](#request-payload).
- Webb servern kör den enskilda funktionen och returnerar en [svars nytto Last](#response-payload) till Functions-värden.
- Functions-värden skickar svaret som en utgående data bindning till målet.

En Azure Functions-app som implementeras som en anpassad hanterare måste konfigurera *Host. JSON* -och *Function. JSON* -filer enligt några konventioner.

## <a name="application-structure"></a>Program struktur

Om du vill implementera en anpassad hanterare behöver du följande aspekter av ditt program:

- En *Host. JSON* -fil i appens rot
- En *Function. JSON* -fil för varje funktion (inuti en mapp som matchar funktions namnet)
- Ett kommando, ett skript eller en körbar fil som kör en webb server

Följande diagram visar hur dessa filer ser ut i fil systemet för en funktion med namnet "order".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Konfiguration

Programmet konfigureras via *Host. JSON* -filen. Den här filen visar de funktioner som är värdar för att skicka begär Anden genom att peka på en webb server som kan bearbeta HTTP-händelser.

En anpassad hanterare definieras genom att konfigurera *Host. JSON* -filen med information om hur du kör webb servern via `httpWorker` avsnittet.

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

`httpWorker` Avsnittet pekar på ett mål som definieras av `defaultExecutablePath`. Körnings målet kan antingen vara ett kommando, en körbar fil eller en fil där webb servern är implementerad.

För skriptbaserade appar `defaultExecutablePath` pekar du på skript språkets körning och `defaultWorkerPath` pekar på skript filens plats. I följande exempel visas hur en JavaScript-app i Node. js konfigureras som en anpassad hanterare.

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

Du kan också skicka argument med hjälp `arguments` av matrisen:

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

Argument krävs för många fel söknings installationer. Mer information finns i avsnittet [fel sökning](#debugging) .

> [!NOTE]
> *Host. JSON* -filen måste finnas på samma nivå i katalog strukturen som den webb server som körs. Vissa språk och verktygs kedjor kanske inte placerar filen i program roten som standard.

#### <a name="bindings-support"></a>Stöd för bindningar

Standard utlösare tillsammans med indata-och utgående bindningar är tillgängliga genom att referera till [tilläggs paket](./functions-bindings-register.md) i *Host. JSON* -filen.

### <a name="function-metadata"></a>Function-metadata

När den används med en anpassad hanterare skiljer sig innehållet i *Function. JSON* inte från hur du definierar en funktion under en annan kontext. Det enda kravet är att *Function. JSON* -filer måste finnas i en mapp med namnet som matchar funktions namnet.

### <a name="request-payload"></a>Begär nytto Last

Nytto lasten för begäran för rena HTTP-funktioner är nytto lasten för RAW HTTP-begäran. Rena HTTP-funktioner definieras som Functions utan några indata-eller utdata-bindningar som returnerar ett HTTP-svar.

En annan typ av funktion som innehåller antingen indata, utgående bindningar eller utlöses via en annan händelse källa än HTTP har en anpassad nytto last för begäran.

Följande kod representerar en nytto last för begäran. Nytto lasten innehåller en JSON-struktur med två `Data` medlemmar `Metadata`: och.

`Data` Medlemmen innehåller nycklar som matchar indata och utlösnings namn som definieras i matrisen för bindningar i *Function. JSON* -filen.

`Metadata` Medlemmen inkluderar [metadata som genereras från händelse källan](./functions-bindings-expressions-patterns.md#trigger-metadata).

De bindningar som definierats i följande *funktion. JSON* -fil:

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

En nytto last för begäran som liknar det här exemplet returneras:

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

### <a name="response-payload"></a>Svars nytto Last

Efter konvention formateras funktions svaren som nyckel/värde-par. Nycklar som stöds är:

| <nobr>Nytto Last nyckel</nobr>   | Datatyp | Anmärkningar                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Innehåller svars värden som definieras av `bindings` matrisen *Function. JSON* -filen.<br /><br />Om en funktion till exempel har kon figurer ATS med en utgående bindning för Blob Storage med namnet BLOB, `Outputs` innehåller en nyckel med `blob`namnet, som är inställd på blobens värde. |
| `Logs`        | matris     | Meddelanden visas i loggarna Functions-anrop.<br /><br />När du kör i Azure visas meddelanden i Application Insights. |
| `ReturnValue` | sträng    | Används för att tillhandahålla ett svar när utdata har kon figurer `$return` ATS som i *Function. JSON* -filen. |

Se [exemplet för en exempel nytto Last](#bindings-implementation).

## <a name="examples"></a>Exempel

Anpassade hanterare kan implementeras på alla språk som stöder HTTP-händelser. Även om Azure Functions [har fullt stöd för Java Script och Node. js](./functions-reference-node.md)visar följande exempel hur du implementerar en anpassad hanterare med hjälp av Java Script i Node. js i instruktions syfte.

> [!TIP]
> När du är en guide för att lära dig hur du implementerar en anpassad hanterare på andra språk kan även de Node. js-baserade exempel som visas här vara användbara om du vill köra en Functions-app i en version av Node. js som inte stöds.

## <a name="http-only-function"></a>Funktionen endast HTTP

Följande exempel visar hur du konfigurerar en HTTP-utlöst funktion utan ytterligare bindningar eller utdata. Scenariot som implementeras i det här exemplet innehåller `http` en funktion som `GET` heter `POST` som accepterar en eller.

Följande kodfragment visar hur en begäran till funktionen består.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementering

I en mapp med namnet *http*konfigurerar *Function. JSON* -filen http-utlöst funktion.

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

Funktionen har kon figurer ATS för att `GET` acceptera `POST` både-och-begär Anden och resultatvärdet anges via ett `res`argument med namnet.

I roten av appen är *Host. JSON* -filen konfigurerad att köra Node. js och peka `server.js` filen.

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

Filen *Server. js* implementerar en webb server och http-funktion.

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

I det här exemplet används Express för att skapa en webb server som hanterar HTTP-händelser och är inställd på att lyssna efter begär `FUNCTIONS_HTTPWORKER_PORT`Anden via.

Funktionen definieras i sökvägen till `/hello`. `GET`begär Anden hanteras genom att returnera ett enkelt JSON-objekt och `POST` begär Anden har åtkomst till begär ande texten `req.body`via.

Vägen för funktionen order finns `/hello` här och inte `/api/hello` eftersom Functions-värden proxyerar begäran till den anpassade hanteraren.

>[!NOTE]
>`FUNCTIONS_HTTPWORKER_PORT` Är inte den offentliga porten som används för att anropa funktionen. Den här porten används av Functions-värden för att anropa den anpassade hanteraren.

## <a name="function-with-bindings"></a>Funktion med bindningar

Scenariot som implementeras i det här exemplet innehåller `order` en funktion med `POST` namnet som godkänner en med en nytto last som representerar en produkt order. När en order skickas till funktionen skapas ett Queue Storage meddelande och ett HTTP-svar returneras.

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

I en mapp med namnet *order*konfigurerar *Function. JSON* -filen http-utlöst funktion.

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

Den här funktionen definieras som en [http-utlöst funktion](./functions-bindings-http-webhook-trigger.md) som returnerar ett [http-svar](./functions-bindings-http-webhook-output.md) och matar ut ett [kö lagrings](./functions-bindings-storage-queue-output.md) meddelande.

I roten av appen är *Host. JSON* -filen konfigurerad att köra Node. js och peka `server.js` filen.

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

Filen *Server. js* implementerar en webb server och http-funktion.

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

I det här exemplet används Express för att skapa en webb server som hanterar HTTP-händelser och är inställd på att lyssna efter begär `FUNCTIONS_HTTPWORKER_PORT`Anden via.

Funktionen definieras i sökvägen till `/order` .  Vägen för funktionen order finns `/order` här och inte `/api/order` eftersom Functions-värden proxyerar begäran till den anpassade hanteraren.

När `POST` förfrågningar skickas till den här funktionen exponeras data genom några punkter:

- Begär ande texten är tillgänglig via`req.body`
- De data som publiceras till funktionen är tillgängliga via`req.body.Data.req.Body`

Funktionens svar är formaterat i ett nyckel/värde-par där `Outputs` medlemmen innehåller ett JSON-värde där nycklarna matchar de utdata som definieras i *Function. JSON* -filen.

Genom att `message` ange ett värde som motsvarar det meddelande som kom från begäran, `res` och till det förväntade http-svaret, skickar den här funktionen ett meddelande till Queue Storage och returnerar ett HTTP-svar.

## <a name="debugging"></a>Felsökning

Om du vill felsöka appen anpassad hanterare för funktioner måste du lägga till lämpliga argument för språket och körnings miljön för att aktivera fel sökning.

Om du till exempel vill felsöka ett Node. js-program `--inspect` skickas flaggan som ett argument i *Host. JSON* -filen.

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
> Konfigurationen för fel sökning är en del av din *Host. JSON* -fil, vilket innebär att du kan behöva ta bort vissa argument innan du distribuerar till produktion.

Med den här konfigurationen kan du starta funktionens värd process med hjälp av följande kommando:

```bash
func host start
```

När processen har startats kan du koppla en fel sökare och trycka på Bryt punkter.

### <a name="visual-studio-code"></a>Visual Studio-koden

Följande exempel är en exempel konfiguration som visar hur du kan konfigurera din *Launch. JSON* -fil för att ansluta din app till Visual Studio Code debugger.

Det här exemplet är för Node. js, så du kan behöva ändra det här exemplet för andra språk eller körningar.

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

## <a name="deploying"></a>Deploy

En anpassad hanterare kan distribueras till nästan alla Azure Functions värd alternativ (se [begränsningar](#restrictions)). Om din hanterare kräver anpassade beroenden (till exempel språk körning) kan du behöva använda en [anpassad behållare](./functions-create-function-linux-custom-image.md).

## <a name="restrictions"></a>Begränsningar

- Anpassade hanterare stöds inte i Linux-förbruknings planer.
- Webb servern måste starta inom 60 sekunder.

## <a name="samples"></a>Exempel

Exempel på hur du implementerar funktioner på flera olika språk hittar du i [exemplen för anpassade hanterare GitHub lagrings platsen](https://github.com/Azure-Samples/functions-custom-handlers) .
