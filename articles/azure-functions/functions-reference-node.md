---
title: Referens för JavaScript-utvecklare för Azure Functions | Microsoft Docs
description: Lär dig hur du utvecklar funktioner med hjälp av Java Script.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 62115dd519336c728b679e4e698182a50660a464
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "68949888"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript-guide för utvecklare

Den här guiden innehåller information om erna för att skriva Azure Functions med Java Script.

En JavaScript-funktion är en `function` exporterad som körs när den utlöses (utlösare[konfigureras i function. JSON](functions-triggers-bindings.md)). Det första argumentet som skickas till varje funktion är `context` ett-objekt som används för att ta emot och skicka bindnings data, loggning och kommunikation med körnings miljön.

Den här artikeln förutsätter att du redan har läst [Azure Functions Developer](functions-reference.md)-referensen. Slutför snabb starten av funktioner för att skapa din första funktion med [Visual Studio Code](functions-create-first-function-vs-code.md) eller [i portalen](functions-create-first-azure-function.md).

Den här artikeln stöder också [utveckling av typescript-appar](#typescript).

## <a name="folder-structure"></a>Mappstruktur

Den obligatoriska mappstrukturen för ett JavaScript-projekt ser ut ungefär så här. Standardvärdet kan ändras. Mer information finns i avsnittet [scriptFile](#using-scriptfile) nedan.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

I roten av projektet finns det en delad [Host. JSON](functions-host-json.md) -fil som kan användas för att konfigurera Function-appen. Varje funktion har en mapp med en egen kod fil (. js) och en bindnings konfigurations fil (Function. JSON). Namnet `function.json`på den överordnade katalogen är alltid namnet på din funktion.

De bindnings tillägg som krävs i [version 2. x](functions-versions.md) av Functions-körningen `extensions.csproj` definieras i filen med de faktiska `bin` biblioteksfilerna i mappen. När du utvecklar lokalt måste du [Registrera bindnings tillägg](./functions-bindings-register.md#extension-bundles). När du utvecklar funktioner i Azure Portal görs registreringen åt dig.

## <a name="exporting-a-function"></a>Exportera en funktion

JavaScript-funktioner måste exporteras via [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (eller [`exports`](https://nodejs.org/api/modules.html#modules_exports)). Den exporterade funktionen bör vara en JavaScript-funktion som körs när den utlöses.

Som standard söker Functions-körningen efter din funktion `index.js`i, `index.js` där delar samma överordnade katalog som dess motsvarande `function.json`. I standard fallet bör den exporterade funktionen vara den enda exporten från filen eller exporten med namnet `run` eller `index`. Om du vill konfigurera fil platsen och export namnet för din funktion läser du om hur [du konfigurerar din funktions start punkt](functions-reference-node.md#configure-function-entry-point) nedan.

Den exporterade funktionen skickas ett antal argument vid körning. Det första argumentet som det tar är alltid `context` ett objekt. Om funktionen är synkron (returnerar inte ett löfte) måste du skicka `context` objektet, `context.done` vilket krävs för korrekt användning.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportera en async-funktion
När du använder [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarationen eller det [](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) vanliga JavaScript-löfteet i version 2. x av Functions-körningen behöver du inte [`context.done`](#contextdone-method) uttryckligen anropa återanropet för att signalera att funktionen har slutförts. Funktionen slutförs när den exporterade asynkrona funktionen/Promise-funktionen slutförs. För funktioner som är riktade till version 1. x-körningen, måste [`context.done`](#contextdone-method) du fortfarande anropa när koden har körts.

Följande exempel är en enkel funktion som loggar att den utlöstes och slutfört körningen direkt.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

När du exporterar en async-funktion kan du också konfigurera en utgående bindning för att `return` ta värdet. Detta rekommenderas om du bara har en utgående bindning.

Om du vill tilldela en `return`utmatning med `name` hjälp av `$return` ändrar `function.json`du egenskapen till i.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

I det här fallet bör din funktion se ut som i följande exempel:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Bindningar 
I Java Script [](functions-triggers-bindings.md) konfigureras och definieras bindningarna i en funktions funktion. JSON. Funktioner interagerar med bindningar på flera sätt.

### <a name="inputs"></a>Indata
Indatamängden är indelade i två kategorier i Azure Functions: en är utlösaren och den andra är den extra ingången. Utlösare och andra indatamasker (bindningar av `direction === "in"`) kan läsas av en funktion på tre sätt:
 - **_[Rekommenderas]_ Som parametrar som skickas till din funktion.** De skickas till funktionen i samma ordning som de definieras i *Function. JSON*. Egenskapen som definieras i *Function. JSON* behöver inte matcha namnet på din parameter, även om den borde. `name`
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Som medlemmar i [`context.bindings`](#contextbindings-property) objektet.** Varje medlem namnges av den `name` egenskap som definierats i *Function. JSON*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Som indata med JavaScript [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) -objektet.** Detta är i stort sett detsamma som att skicka indata som parametrar, men gör att du kan hantera indata dynamiskt.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>outputs
Utdata (bindningar av `direction === "out"`) kan skrivas till av en funktion på flera sätt. I samtliga fall `name` motsvarar egenskapen för bindningen som definieras i *Function. JSON* namnet på den objekt medlem som skrivits till i din funktion. 

Du kan tilldela data till utgående bindningar på något av följande sätt (kombinera inte dessa metoder):

- **_[Rekommenderas för flera utdata]_ Returnera ett objekt.** Om du använder en asynkron/löftes funktion för att returnera, kan du returnera ett objekt med tilldelad utmatnings information. I exemplet nedan heter utgående bindningarna "httpResponse" och "queueOutput" i *Function. JSON*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Om du använder en synkron funktion kan du returnera objektet med hjälp av [`context.done`](#contextdone-method) (se exemplet).
- **_[Rekommenderas för enstaka utdata]_ Returnerar ett värde direkt och använder $return bindnings namn.** Detta fungerar endast för asynkrona/löftes retur funktioner. Se exempel i [Exportera en async-funktion](#exporting-an-async-function). 
- Om du **tilldelar `context.bindings` värden till** kan du tilldela värden direkt till Context. bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Data typen bindings

Använd `dataType` egenskapen i bindnings definitionen för att definiera data typen för en indata-bindning. Om du till exempel vill läsa innehållet i en HTTP-begäran i binärt format använder du `binary`typen:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Alternativen för `dataType` är: `binary`, `stream`och .`string`

## <a name="context-object"></a>kontext objekt
Körningen använder ett `context` objekt för att skicka data till och från din funktion och för att kommunicera med körningen. Context-objektet kan användas för att läsa och ställa in data från bindningar, skriva loggar och använda `context.done` motringning när den exporterade funktionen är synkron.

`context` Objektet är alltid den första parametern för en funktion. Den bör inkluderas eftersom den har viktiga metoder som `context.done` och. `context.log` Du kan namnge objektet oavsett om du vill (till exempel `ctx` eller `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Egenskapen context. bindings

```js
context.bindings
```

Returnerar ett namngivet objekt som används för att läsa eller tilldela data bindnings data. Du kan få åtkomst till indata och utlösnings bindnings `context.bindings`data genom att läsa egenskaper på. Data för utgående bindning kan tilldelas genom att lägga till data i`context.bindings`

Följande bindnings definitioner i din function. JSON kan till exempel användas för att komma åt innehållet i en kö `context.bindings.myInput` från och tilldela utdata till en kö med `context.bindings.myOutput`hjälp av.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Du kan välja att definiera utgående bindnings data med `context.done` hjälp av-metoden `context.binding` i stället för objektet (se nedan).

### <a name="contextbindingdata-property"></a>Context. bindingData-egenskap

```js
context.bindingData
```

Returnerar ett namngivet objekt som innehåller utlösarens metadata och anrops data (`invocationId` `sys.utcNow`, `sys.methodName`,, `sys.randGuid`). Ett exempel på att utlösa metadata finns i exemplet på den här [händelse hubben](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>metoden context. Done

```js
context.done([err],[propertyBag])
```

Låter körnings miljön veta att koden har slutförts. När din funktion använder [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarationen behöver du inte använda. `context.done()` `context.done` Återanropet anropas implicit. Async Functions är tillgängliga i Node 8 eller en senare version, vilket kräver version 2. x av Functions-körningen.

Om funktionen inte är en async-funktion **måste du anropa** `context.done` för att informera körnings miljön om att funktionen har slutförts. Körnings tiden går ut om det saknas.

`context.done` Metoden gör att du kan skicka tillbaka både ett användardefinierat fel till körnings miljön och ett JSON-objekt som innehåller data för utgående bindning. Egenskaper som har `context.done` överförts för att skriva över `context.bindings` någon uppsättning i objektet.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>kontext. log-metod  

```js
context.log(message)
```

Gör att du kan skriva till strömnings funktions loggarna på standard spårnings nivån. På `context.log`är ytterligare loggnings metoder tillgängliga som låter dig skriva funktions loggar på andra spårnings nivåer:


| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **fel (_meddelande_)**   | Skriver till loggning på fel nivå eller lägre.   |
| **Varna (_meddelande_)**    | Skriver till loggning på varnings nivå eller lägre. |
| **info (_meddelande_)**    | Skriver till loggning på informations nivå eller lägre.    |
| **utförlig (_meddelande_)** | Skriver till utförlig nivå loggning.           |

I följande exempel skrivs en logg på varnings spårnings nivån:

```javascript
context.log.warn("Something has happened."); 
```

Du kan [Konfigurera tröskeln på spårnings nivå för loggning](#configure-the-trace-level-for-console-logging) i Host. JSON-filen. Mer information om hur du skriver loggar finns i [Skriv spårnings utdata](#writing-trace-output-to-the-console) nedan.

Läs [övervaknings Azure Functions](functions-monitoring.md) om du vill veta mer om att visa och fråga funktions loggar.

## <a name="writing-trace-output-to-the-console"></a>Skriva spårningsutdata till konsolen 

I functions använder `context.log` du metoder för att skriva spårningsutdata till-konsolen. I functions v2. x, fångas spåra `console.log` utdata som använder på Funktionsapp nivå. Det innebär att utdata från `console.log` inte är kopplade till ett särskilt funktions anrop och inte visas i en speciell funktions loggar. De kan dock spridas till Application Insights. I functions v1. x kan du inte `console.log` använda för att skriva till-konsolen.

När du anropar `context.log()`, skrivs ditt meddelande till-konsolen på standard spårnings nivån, vilket är spårnings nivån för _information_ . Följande kod skriver till-konsolen på informations spårnings nivån:

```javascript
context.log({hello: 'world'});  
```

Den här koden motsvarar koden ovan:

```javascript
context.log.info({hello: 'world'});  
```

Den här koden skriver till konsolen på fel nivå:

```javascript
context.log.error("An error has occurred.");  
```

Eftersom _fel_ är den högsta spårnings nivån skrivs denna spårning till utdata vid alla spårnings nivåer så länge loggning har Aktiver ATS.

Alla `context.log` metoder har stöd för samma parameter format som stöds av metoden Node. js- [util. format](https://nodejs.org/api/util.html#util_util_format_format). Tänk på följande kod, som skriver funktions loggar med hjälp av standard spårnings nivån:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Du kan också skriva samma kod i följande format:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Konfigurera spårnings nivå för konsol loggning

Med funktionerna 1. x kan du definiera tröskel spårnings nivå för att skriva till-konsolen, vilket gör det enkelt att styra hur spårningen skrivs till-konsolen från din funktion. Om du vill ange tröskelvärdet för alla spår som skrivs till-konsolen `tracing.consoleLevel` använder du egenskapen i Host. JSON-filen. Den här inställningen gäller för alla funktioner i din Function-app. I följande exempel anges spårnings tröskeln för att aktivera utförlig loggning:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Värdena för **consoleLevel** motsvarar namnen på `context.log` metoderna. Om du vill inaktivera all spårnings loggning till-konsolen anger du **consoleLevel** till _av_. Mer information finns i [Host. JSON-referens](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP-utlösare och bindningar

HTTP-och webhook-utlösare och HTTP-utgående bindningar använder begäran-och svars objekt för att representera HTTP-meddelanden.  

### <a name="request-object"></a>Begär ande objekt

`context.req` (Request)-objektet har följande egenskaper:

| Egenskap      | Beskrivning                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Ett objekt som innehåller bröd texten i begäran.               |
| _headers_     | Ett objekt som innehåller begärandehuvuden.                   |
| _method_      | HTTP-metoden för begäran.                                |
| _originalUrl_ | URL för begäran.                                        |
| _params_      | Ett objekt som innehåller Dirigerings parametrarna för begäran. |
| _query_       | Ett objekt som innehåller frågeparametrarna.                  |
| _rawBody_     | Bröd texten i meddelandet som en sträng.                           |


### <a name="response-object"></a>Svarsobjekt

Objektet `context.res` (Response) har följande egenskaper:

| Egenskap  | Beskrivning                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Ett objekt som innehåller bröd texten i svaret.         |
| _headers_ | Ett objekt som innehåller svarshuvuden.             |
| _isRaw_   | Anger att formateringen hoppas över för svaret.    |
| _status_  | HTTP-statuskod för svaret.                     |

### <a name="accessing-the-request-and-response"></a>Åtkomst till begäran och svar 

När du arbetar med HTTP-utlösare kan du komma åt HTTP-begäran och svars objekt på flera olika sätt:

+ **Från `req` och `res` egenskaper för`context` objektet.** På så sätt kan du använda det konventionella mönstret för att komma åt http-data från objektet Context, i stället för att behöva använda `context.bindings.name` det fullständiga mönstret. I följande exempel visas hur du kommer åt `req` - `res` och-objekten `context`på:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Från namngivna indata och utgående bindningar.** På så sätt fungerar HTTP-utlösaren och bindningarna på samma sätt som andra bindningar. I följande exempel anges Response-objektet med hjälp av `response` en namngiven bindning: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Endast svar]_ Genom att `context.res.send(body?: any)`anropa.** Ett HTTP-svar skapas med inkommande `body` som svars text. `context.done()`har implicit namn.

+ **_[Endast svar]_ Genom att `context.done()`anropa.** En särskild typ av http-bindning returnerar svaret som skickas till `context.done()` metoden. Följande http-utgående bindning definierar en `$return` utdataparameter:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Node-version

I följande tabell visas den Node. js-version som används av varje huvud version av Functions-körningen:

| Funktions version | Node. js-version | 
|---|---|
| 1.x | 6.11.2 (låst av körningen) |
| 2x  | _Aktiva LTS_ och _Underhåll LTS_ Node. js-versioner (8.11.1 och 10.14.1 rekommenderas). Ställ in versionen med WEBSITE_NODE_DEFAULT_VERSION- [appens inställning](functions-how-to-use-azure-function-app-settings.md#settings).|

Du kan se den aktuella versionen som körningen använder genom att kontrol lera appen ovan eller genom att skriva `process.version` ut från vilken funktion som helst.

## <a name="dependency-management"></a>Beroendehantering
För att kunna använda grupp bibliotek i JavaScript-koden, som visas i exemplet nedan, måste du se till att alla beroenden är installerade på Funktionsapp i Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Du bör definiera en `package.json` fil i roten för din Funktionsapp. Genom att definiera filen kan alla funktioner i appen dela samma cachelagrade paket, vilket ger bästa möjliga prestanda. Om en versions konflikt uppstår kan du lösa det genom att lägga `package.json` till en fil i mappen för en speciell funktion.  

När du distribuerar Function- `package.json` appar från käll kontroll utlöser en `npm install` fil som finns i din lagrings platsen i mappen under distributionen. Men när du distribuerar via portalen eller CLI måste du manuellt installera paketen.

Det finns två sätt att installera paket på Funktionsapp: 

### <a name="deploying-with-dependencies"></a>Distribuera med beroenden
1. Installera alla nödvändiga paket lokalt genom att `npm install`köra.

2. Distribuera koden och se till att `node_modules` mappen ingår i distributionen. 


### <a name="using-kudu"></a>Använda kudu
1. Gå till `https://<function_app_name>.scm.azurewebsites.net`.

2. Klicka på **Felsök konsol** > **cmd**.

3. Gå till `D:\home\site\wwwroot`och dra sedan din Package. JSON-fil till mappen **wwwroot** på sidans övre halva.  
    Du kan också ladda upp filer till din Function-app på andra sätt. Mer information finns i [så här uppdaterar du Function Apps-filer](functions-reference.md#fileupdate). 

4. När Package. JSON-filen har överförts kör du `npm install` kommandot i **kudu-konsolen**för fjärrkörning.  
    Den här åtgärden hämtar de paket som anges i Package. JSON-filen och startar om Function-appen.

## <a name="environment-variables"></a>Miljövariabler

I funktioner visas [appinställningar](functions-app-settings.md), till exempel tjänst anslutnings strängar, som miljövariabler under körningen. Du kan komma åt de här `process.env`inställningarna med hjälp av, som visas här i den andra `context.log()` och tredje anropet `AzureWebJobsStorage` , `WEBSITE_SITE_NAME` där vi loggar variablerna och.

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

När du kör lokalt läses AppData från projekt filen [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="configure-function-entry-point"></a>Konfigurera funktionens start punkt

`function.json` Egenskaperna ochkan`entryPoint` användas för att konfigurera platsen och namnet på den exporterade funktionen. `scriptFile` Dessa egenskaper kan vara viktiga när ditt java script är indelat.

### <a name="using-scriptfile"></a>Där`scriptFile`

Som standard körs en JavaScript-funktion från `index.js`en fil som delar samma överordnade katalog som dess motsvarande. `function.json`

`scriptFile`kan användas för att hämta en mappstruktur som ser ut som i följande exempel:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

För ska inkludera en`scriptFile` egenskap som pekar på filen med den exporterade funktionen som ska köras. `myNodeFunction` `function.json`

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Där`entryPoint`

I `scriptFile` (eller `index.js`) måste en funktion exporteras med `module.exports` för att kunna hittas och köras. Som standard är den funktion som körs när den utlöses den enda exporten från filen, exporten med namnet `run`eller exporten med namnet `index`.

Detta kan konfigureras med `entryPoint` i `function.json`, som i följande exempel:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

I functions v2. x, som stöder `this` parametern i användar funktioner, kan funktions koden sedan vara som i följande exempel:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

I det här exemplet är det viktigt att Observera att även om ett objekt exporteras, finns det inga garantier för att bevara tillstånd mellan körningar.

## <a name="local-debugging"></a>Lokal fel sökning

När den startas med `--inspect` parametern lyssnar en Node. js-process efter en fel söknings klient på den angivna porten. I Azure Functions 2. x kan du ange argument som ska skickas till Node. js-processen som kör koden genom att lägga till miljövariabeln eller app `languageWorkers:node:arguments = <args>`-inställningen. 

Du kan felsöka lokalt genom `"languageWorkers:node:arguments": "--inspect=5858"` att `Values` lägga till under i din [lokala. Settings. JSON](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) -fil och koppla en fel sökare till port 5858.

När du `--inspect` felsöker med vs Code läggs parametern automatiskt till `port` med värdet i projektets starta. JSON-fil.

Inställningen `languageWorkers:node:arguments` fungerar inte i version 1. x. Du kan välja fel söknings port med [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parametern på Azure Functions Core tools.

## <a name="typescript"></a>TypeScript

När du riktar in dig på version 2. x av Functions-körningen kan du använda både [Azure Functions för Visual Studio Code](functions-create-first-function-vs-code.md) och [Azure Functions Core tools](functions-run-local.md) för att skapa Function-appar med hjälp av en mall som stöder typescript Function app projects. Mallen genererar `package.json` och `tsconfig.json` projektfiler som gör det enklare att stapla, köra och publicera JavaScript-funktioner från typescript-kod med dessa verktyg.

En genererad `.funcignore` fil används för att ange vilka filer som ska undantas när ett projekt publiceras i Azure.  

TypeScript-filer (. TS) är destaplade i JavaScript-filer (. js) `dist` i utdatakatalogen. TypeScript-mallar använder [ `scriptFile` ](#using-scriptfile) -parametern `function.json` i för att ange platsen för motsvarande. js-fil i `dist` mappen. Platsen för utdata anges av mallen med hjälp av `outDir` parametern `tsconfig.json` i filen. Om du ändrar den här inställningen eller mappens namn kan inte körningen hitta koden som ska köras.

> [!NOTE]
> Experimentellt stöd för TypeScript finns version 1. x i functions-körningen. Experimentella versioner instaplar TypeScript-filer i JavaScript-filer när funktionen anropas. I version 2. x har det här experimentella stödet ersatts av den verktygs drivna metoden som transpilation innan värden initieras och under distributions processen.

Hur du utvecklar och distribuerar lokalt från ett TypeScript-projekt beror på ditt utvecklingsverktyg.

### <a name="visual-studio-code"></a>Visual Studio-koden

Med [Azure Functions för kod tillägget för Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) kan du utveckla dina funktioner med typescript. Kärn verktygen är ett krav i Azure Functions-tillägget.

Om du vill skapa en typescript Function-app i Visual Studio `TypeScript` Code väljer du som språk när du skapar en Function-app.

När du trycker på **F5** för att köra appen lokalt görs transpilation innan värden (Func. exe) initieras. 

När du distribuerar din Function-app till Azure med hjälp av knappen **distribuera till Function-appen...** , skapar Azure Functions-tillägget först en produktions klar version av JavaScript-filer från källfilerna för typescript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Det finns flera sätt på vilka ett TypeScript-projekt skiljer sig från ett JavaScript-projekt när du använder kärn verktygen.

#### <a name="create-project"></a>Skapa projekt

Om du vill skapa ett TypeScript Function-projekt med hjälp av kärn verktyg måste du ange språk alternativet TypeScript när du skapar din Function-app. Det kan du göra på något av följande sätt:

- Kör kommandot, Välj `node` som språk stack och välj `typescript`sedan. `func init`

- Kör `func init --worker-runtime typescript`-kommandot.

#### <a name="run-local"></a>Kör lokal

Om du vill köra en Function-app-kod lokalt med hjälp av kärn verktyg, `func host start`använder du följande kommandon i stället för: 

```command
npm install
npm start
```

`npm start` Kommandot motsvarar följande kommandon:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publicera till Azure

Innan du använder [`func azure functionapp publish`] kommandot för att distribuera till Azure skapar du en produktions klar version av JavaScript-filer från källfilerna för typescript. 

Följande kommandon förbereder och publicerar ditt TypeScript-projekt med hjälp av kärn verktyg: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

I det här kommandot ersätter `<APP_NAME>` du med namnet på din Function-app.

## <a name="considerations-for-javascript-functions"></a>Överväganden för JavaScript-funktioner

När du arbetar med JavaScript-funktioner bör du vara medveten om överväganden i följande avsnitt.

### <a name="choose-single-vcpu-app-service-plans"></a>Välj en vCPU App Service planer

När du skapar en Function-app som använder App Service plan rekommenderar vi att du väljer en plan med en vCPU i stället för en plan med flera virtuella processorer. Idag kör funktionerna JavaScript-funktioner på ett mer effektivt sätt på virtuella datorer med en vCPU och användningen av större virtuella datorer ger inte de förväntade prestanda förbättringarna. Vid behov kan du manuellt skala ut genom att lägga till fler VM-instanser med en vCPU, eller så kan du aktivera autoskalning. Mer information finns i [skala antalet instanser manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Kall start

När du utvecklar Azure Functions i den serverbaserade värd modellen är kall start en verklighet. *Kall start* avser det faktum att när din Function-app startar för första gången efter en tids inaktivitet tar det längre tid att starta. För JavaScript-funktioner med stora beroende träd i synnerhet kan kall start vara betydande. [Kör dina funktioner som en paket fil](run-functions-from-deployment-package.md) när det är möjligt för att påskynda den kall start processen. Många distributions metoder använder modellen kör från paket som standard, men om du har stora kall starter och inte körs på det här sättet kan den här ändringen ge en betydande förbättring.

### <a name="connection-limits"></a>Anslutnings gränser

När du använder en tjänstespecifik klient i ett Azure Functions program ska du inte skapa en ny klient med varje funktions anrop. Skapa i stället en enda statisk klient i det globala omfånget. Mer information finns i [hantera anslutningar i Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Använda `async` och`await`

När du skriver Azure Functions i Java Script ska du skriva kod med `async` hjälp `await` av nyckelorden och. Att skriva kod `async` med `await` hjälp av och i stället `.then` för `.catch` återanrop eller och med löfte bidrar till att undvika två vanliga problem:
 - Utlöser ej fångade undantag som [kraschar Node. js-processen](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), vilket kan påverka körningen av andra funktioner.
 - Oväntat beteende, t. ex. saknade loggar från context. log, som orsakas av asynkrona anrop som inte är korrekt förväntat.

I exemplet nedan anropas den asynkrona `fs.readFile` metoden med en fel-första återanrops funktion som den andra parametern. Den här koden orsakar båda de problem som anges ovan. Ett undantag som inte uttryckligen fångas i rätt område kraschade hela processen (utfärdande #1). Anrop `context.done()` utanför omfånget för motringningsfunktionen innebär att funktions anropet kan avslutas innan filen läses (utfärdande #2). I det här exemplet anropar `context.done()` för tidigt resultatet att logg poster som saknas börjar med. `Data from file:`

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Genom att `async` använda `await` -och-nyckelorden kan du undvika båda dessa fel. Du bör använda funktionen [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) Node. js för att aktivera fel-första funktions sätt för callback-format till inväntade funktioner.

I exemplet nedan går det inte att utföra alla ohanterade undantag som genererades under funktions körningen, men det enskilda anropet som utlöste ett undantag kunde inte utföras. Nyckelordet innebär att stegen nedan `readFileAsync` bara körs när `readFile` har slutförts. `await` Med `async` och `await`behöver du`context.done()` inte heller anropa återanropet.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    try {
        const data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

+ [Metodtips för Azure Functions](functions-best-practices.md)
+ [Azure Functions, info för utvecklare](functions-reference.md)
+ [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)

[' FUNC Azure functionapp Publish ']: functions-run-local.md#project-file-deployment
