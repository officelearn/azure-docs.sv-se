---
title: JavaScript-utvecklarreferens för Azure-funktioner
description: Förstå hur du utvecklar funktioner med hjälp av JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: 345df8e1ea88caa6f8dbe941245c1f989c3e81c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276834"
---
# <a name="azure-functions-javascript-developer-guide"></a>Utvecklarhandboken för Azure Functions JavaScript

Den här guiden innehåller information om krångligheter att skriva Azure Functions med JavaScript.

En JavaScript-funktion är `function` en exporterad som körs när den utlöses[(utlösare konfigureras i function.json](functions-triggers-bindings.md)). Det första argumentet som skickas `context` till varje funktion är ett objekt som används för att ta emot och skicka bindningsdata, logga och kommunicera med körningen.

Den här artikeln förutsätter att du redan har läst [Utvecklarreferensen för Azure Functions](functions-reference.md). Slutför snabbstarten Funktioner för att skapa din första funktion, med [Visual Studio-kod](functions-create-first-function-vs-code.md) eller [i portalen](functions-create-first-azure-function.md).

Den här artikeln stöder också [utveckling av TypeScript-appar](#typescript).

## <a name="folder-structure"></a>Mappstrukturen

Den mappstruktur som krävs för ett JavaScript-projekt ser ut som följande. Den här standardinställningen kan ändras. Mer information finns i [avsnittet scriptFile](#using-scriptfile) nedan.

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

I projektets rot finns en delad [host.json-fil](functions-host-json.md) som kan användas för att konfigurera funktionsappen. Varje funktion har en mapp med en egen kodfil (.js) och bindningskonfigurationsfil (function.json). Namnet på `function.json`'s överordnade katalog är alltid namnet på din funktion.

De bindningstillägg som krävs i [version 2.x](functions-versions.md) `extensions.csproj` av funktionskörningen definieras `bin` i filen, med de faktiska biblioteksfilerna i mappen. När du utvecklar lokalt måste du [registrera bindningstillägg.](./functions-bindings-register.md#extension-bundles) När du utvecklar funktioner i Azure-portalen görs den här registreringen åt dig.

## <a name="exporting-a-function"></a>Exportera en funktion

JavaScript-funktioner måste exporteras [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) [`exports`](https://nodejs.org/api/modules.html#modules_exports)via (eller ). Den exporterade funktionen ska vara en JavaScript-funktion som körs när den utlöses.

Som standard söker körtiden Funktioner efter `index.js`din `index.js` funktion i , där `function.json`samma överordnade katalog som motsvarande . I standardfallet bör den exporterade funktionen vara den enda exporten från filen eller exporten som namnges `run` eller `index`. Om du vill konfigurera filens plats och exportnamn för din funktion läser du om [att konfigurera funktionens startpunkt](functions-reference-node.md#configure-function-entry-point) nedan.

Den exporterade funktionen skickas ett antal argument vid körning. Det första argumentet som `context` krävs är alltid ett objekt. Om din funktion är synkron (returnerar inte ett `context` löfte) måste `context.done` du skicka objektet, eftersom det krävs att du ringer för korrekt användning.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportera en asynkron funktion
När du [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) använder deklarationen eller vanliga JavaScript-löften i version 2.x av [`context.done`](#contextdone-method) funktionskörningen behöver du inte uttryckligen anropa motringningen för att signalera att funktionen har slutförts. [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Din funktion slutförs när den exporterade async-funktionen/Promise slutförs. För funktioner som är inriktade på körningen version [`context.done`](#contextdone-method) 1.x måste du fortfarande anropa när koden körs.

Följande exempel är en enkel funktion som loggar att den utlöstes och omedelbart slutför körningen.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

När du exporterar en asynkron funktion kan `return` du också konfigurera en utdatabindning för att ta värdet. Detta rekommenderas om du bara har en utdatabindning.

Om du vill `return`tilldela `name` en `$return` utdata med ändrar du egenskapen till i `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

I det här fallet bör din funktion se ut som följande exempel:

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
I JavaScript konfigureras och definieras [bindningar](functions-triggers-bindings.md) i en funktions funktion.json. Funktioner interagerar med bindningar på flera olika sätt.

### <a name="inputs"></a>Indata
Indata är indelade i två kategorier i Azure Functions: en är utlösarindata och den andra är ytterligare indata. Utlösare och andra indatabindningar (bindningar av `direction === "in"`) kan läsas av en funktion på tre sätt:
 - **_[Rekommenderas]_ Som parametrar skickas till din funktion.** De skickas till funktionen i samma ordning som de definieras i *function.json*. Egenskapen `name` som definieras i *function.json* behöver inte matcha namnet på parametern, även om den borde.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Som medlemmar [`context.bindings`](#contextbindings-property) i objektet.** Varje medlem namnges `name` av egenskapen som definieras i *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Som ingångar med [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) JavaScript-objektet.** Detta är i stort sett samma sak som att skicka indata som parametrar, men gör att du dynamiskt kan hantera indata.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Utdata
Utdata (bindningar `direction === "out"`av ) kan skrivas till av en funktion på flera olika sätt. I samtliga fall `name` motsvarar egenskapen för bindningen enligt definitionen i *function.json* namnet på den objektmedlem som skrivits till i din funktion. 

Du kan tilldela data till utdatabindningar på något av följande sätt (kombinera inte dessa metoder):

- **_[Rekommenderas för flera utgångar]_ Returnerar ett objekt.** Om du använder en async/Promise-returfunktion kan du returnera ett objekt med tilldelade utdata. I exemplet nedan heter utdatabindningarna "httpResponse" och "queueOutput" i *function.json*.

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

  Om du använder en synkron funktion kan du [`context.done`](#contextdone-method) returnera objektet med (se exempel).
- **_[Rekommenderas för enstaka utdata]_ Returnerar ett värde direkt och använder $return bindningsnamn.** Detta fungerar bara för async/ Promise återkommande funktioner. Se exempel på [hur du exporterar en async-funktion](#exporting-an-async-function). 
- **Tilldela värden `context.bindings` till** Du kan tilldela värden direkt till context.bindings.

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

### <a name="bindings-data-type"></a>Datatyp för bindningar

Om du vill definiera datatypen `dataType` för en indatabindning använder du egenskapen i bindningsdefinitionen. Om du till exempel vill läsa innehållet i en `binary`HTTP-begäran i binärt format använder du typen:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Alternativ `dataType` för `binary`är: `stream` `string`, och .

## <a name="context-object"></a>sammanhangsobjekt
Körningen använder `context` ett objekt för att skicka data till och från din funktion och låta dig kommunicera med körningen. Kontextobjektet kan användas för att läsa och ställa in data `context.done` från bindningar, skriva loggar och använda motringning när den exporterade funktionen är synkron.

Objektet `context` är alltid den första parametern till en funktion. Det bör ingå eftersom det har `context.done` `context.log`viktiga metoder såsom och . Du kan namnge objektet vad du vill `ctx` `c`(till exempel ).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings egenskap

```js
context.bindings
```

Returnerar ett namngivet objekt som används för att läsa eller tilldela bindningsdata. Indata- och utlösarbindningsdata `context.bindings`kan nås genom läsegenskaper på . Utdatabindningsdata kan tilldelas genom att lägga till data i`context.bindings`

Med följande bindningsdefinitioner i din function.json kan du `context.bindings.myInput` till exempel komma åt `context.bindings.myOutput`innehållet i en kö från och tilldela utdata till en kö med .

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

Du kan välja att definiera `context.done` utdatabindningsdata med metoden i stället för `context.binding` objektet (se nedan).

### <a name="contextbindingdata-property"></a>context.bindingData egenskap

```js
context.bindingData
```

Returnerar ett namngivet objekt som innehåller utlösardata `sys.methodName` `sys.utcNow`och `sys.randGuid`funktionsinropsdata (`invocationId`, , , ). Ett exempel på utlösande metadata finns i exemplet med [händelsehubbar](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>metoden context.done

```js
context.done([err],[propertyBag])
```

Gör att körningen vet att koden har slutförts. När funktionen använder [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarationen behöver du `context.done()`inte använda . Motringningen `context.done` anropas implicit. Async-funktioner är tillgängliga i Nod 8 eller en senare version, vilket kräver version 2.x av funktionskörningen.

Om din funktion inte är en async-funktion **måste du ringa** `context.done` för att informera om körningen att din funktion är klar. Körningen time out om det saknas.

Med `context.done` metoden kan du skicka tillbaka både ett användardefinierat fel till körningen och ett JSON-objekt som innehåller utdatabindningsdata. Egenskaper som `context.done` skickas för att `context.bindings` skriva över allt som angetts på objektet.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>metoden context.log  

```js
context.log(message)
```

Gör att du kan skriva till strömmande funktionsloggar på standardspårningsnivå. På `context.log`finns ytterligare loggningsmetoder som gör att du kan skriva funktionsloggar på andra spårningsnivåer:


| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **error(_meddelande_)**   | Skriver till felnivåloggning eller lägre.   |
| **varna(_meddelande_)**    | Skriver till loggning på varningsnivå eller lägre. |
| **info(_meddelande_)**    | Skriver till loggning på informationsnivå eller lägre.    |
| **verbose(_meddelande_)** | Skriver till utförlig nivåloggning.           |

I följande exempel skrivs en logg på varningsspårningsnivå:

```javascript
context.log.warn("Something has happened."); 
```

Du kan [konfigurera tröskelvärdet för spårningsnivå för loggning](#configure-the-trace-level-for-console-logging) i filen host.json. Mer information om hur du skriver loggar finns i [skriva spårningsutdata](#writing-trace-output-to-the-console) nedan.

Läs [övervakning av Azure Functions](functions-monitoring.md) om du vill veta mer om att visa och fråga funktionsloggar.

## <a name="writing-trace-output-to-the-console"></a>Skriva spårningsutdata till konsolen 

I Funktioner använder `context.log` du metoderna för att skriva spårningsutdata till konsolen. I Functions v2.x fångas `console.log` spårningsutdata med på funktionsappnivå. Det innebär att `console.log` utdata från inte är kopplade till en viss funktionsyrop och inte visas i en viss funktions loggar. De sprider sig dock till Application Insights. I Functions v1.x kan `console.log` du inte använda för att skriva till konsolen.

När du `context.log()`ringer skrivs meddelandet till konsolen på standardspårningsnivå, vilket är informationsspårningsnivån. _info_ Följande kod skrivs till konsolen på informationsspårningsnivå:

```javascript
context.log({hello: 'world'});  
```

Denna kod motsvarar koden ovan:

```javascript
context.log.info({hello: 'world'});  
```

Den här koden skriver till konsolen på felnivå:

```javascript
context.log.error("An error has occurred.");  
```

Eftersom _felet_ är den högsta spårningsnivån skrivs den här spårningen till utdata på alla spårningsnivåer så länge loggning är aktiverat.

Alla `context.log` metoder stöder samma parameterformat som stöds av metoden Node.js [util.format](https://nodejs.org/api/util.html#util_util_format_format). Tänk på följande kod, som skriver funktionsloggar med hjälp av standardspårningsnivån:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Du kan också skriva samma kod i följande format:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Konfigurera spårningsnivån för konsolloggning

Med funktionerna 1.x kan du definiera tröskelspårningsnivån för att skriva till konsolen, vilket gör det enkelt att styra hur spårningar skrivs till konsolen från din funktion. Om du vill ange tröskelvärdet för alla `tracing.consoleLevel` spårningar som skrivits till konsolen använder du egenskapen i filen host.json. Den här inställningen gäller för alla funktioner i funktionsappen. I följande exempel anges spårningströskeln för att aktivera utförlig loggning:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Värden för **consoleLevel** motsvarar namnen `context.log` på metoderna. Om du vill inaktivera all spårningsloggning till konsolen ställer du **in consoleLevel** to _off_. Mer information finns i [host.json reference](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP-utlösare och bindningar

HTTP- och webhook-utlösare och HTTP-utdatabindningar använder begäran och svarsobjekt för att representera HTTP-meddelanden.  

### <a name="request-object"></a>Begär objekt

Objektet `context.req` (begäran) har följande egenskaper:

| Egenskap      | Beskrivning                                                    |
| ------------- | -------------------------------------------------------------- |
| _Kroppen_        | Ett objekt som innehåller brödtexten för begäran.               |
| _Headers_     | Ett objekt som innehåller begäranderubrikerna.                   |
| _Metod_      | HTTP-metoden för begäran.                                |
| _originalUrl_ | Url:en för begäran.                                        |
| _Params_      | Ett objekt som innehåller routningsparametrarna för begäran. |
| _Fråga_       | Ett objekt som innehåller frågeparametrarna.                  |
| _rawBody (råkropp)_     | Meddelandets brödtext som en sträng.                           |


### <a name="response-object"></a>Svarsobjekt

Objektet `context.res` (svar) har följande egenskaper:

| Egenskap  | Beskrivning                                               |
| --------- | --------------------------------------------------------- |
| _Kroppen_    | Ett objekt som innehåller svarets brödtext.         |
| _Headers_ | Ett objekt som innehåller svarsrubrikerna.             |
| _ärRaw_   | Anger att formateringen hoppas över för svaret.    |
| _status_  | HTTP-statuskoden för svaret.                     |
| _Cookies_ | En matris med HTTP-cookieobjekt som anges i svaret. Ett `name`HTTP-cookie-objekt `value`har en , , `maxAge` `sameSite`och andra cookie-egenskaper, till exempel eller . |

### <a name="accessing-the-request-and-response"></a>Komma åt begäran och svar 

När du arbetar med HTTP-utlösare kan du komma åt HTTP-begäran och svarsobjekt på flera olika sätt:

+ **Från `req` `res` och egenskaper `context` på objektet.** På så sätt kan du använda det konventionella mönstret för att komma `context.bindings.name` åt HTTP-data från kontextobjektet, i stället för att behöva använda hela mönstret. I följande exempel visas `req` hur `res` du `context`kommer åt objekten och på :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Från de namngivna in- och utdatabindningarna.** På så sätt fungerar HTTP-utlösaren och bindningarna på samma sätt som alla andra bindningar. I följande exempel anges svarsobjektet med hjälp av en namngiven `response` bindning: 

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
+ **_[Endast svar]_ Genom `context.res.send(body?: any)`att ringa .** Ett HTTP-svar skapas med indata `body` som svarstext. `context.done()`kallas implicit.

+ **_[Endast svar]_ Genom `context.done()`att ringa .** En särskild typ av HTTP-bindning returnerar svaret som skickas till `context.done()` metoden. Följande HTTP-utdatabindning definierar en `$return` utdataparameter:

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

## <a name="scaling-and-concurrency"></a>Skalning och samtidighet

Som standard övervakar Azure Functions automatiskt belastningen på ditt program och skapar ytterligare värdinstanser för Node.js efter behov. Funktioner använder inbyggda (inte användarkonfigurerbara) tröskelvärden för olika utlösartyper för att bestämma när instanser ska läggas till, till exempel meddelandeålder och köstorlek för QueueTrigger. Mer information finns i [Så här fungerar förbruknings- och Premium-abonnemangen](functions-scale.md#how-the-consumption-and-premium-plans-work).

Det här skalningsbeteendet är tillräckligt för många Node.js-program. För CPU-bundna program kan du förbättra prestanda ytterligare med hjälp av flera språkarbetsprocesser.

Som standard har varje värdinstans för Funktioner en enda språkarbetsprocess. Du kan öka antalet arbetsprocesser per värd (upp till 10) med hjälp av [programinställningen för FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) Azure Functions försöker sedan distribuera samtidiga funktionsarop mellan dessa arbetare. 

Den FUNCTIONS_WORKER_PROCESS_COUNT gäller för varje värd som Functions skapar när du skalar ut ditt program för att möta efterfrågan. 

## <a name="node-version"></a>Nodversion

I följande tabell visas aktuella nod.js-versioner som stöds för varje huvudversion av funktionskörningen, efter operativsystem:

| Version av funktioner | Nodversion (Windows) | Nod version (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (låst av körningen) | Saknas |
| 2.x  | ~8<br/>~10 (rekommenderas)<br/>~ 12<sup>*</sup> | ~8 (rekommenderas)<br/>~ 10  |
| 3.x | ~ 10<br/>~12 (rekommenderas)  | ~ 10<br/>~12 (rekommenderas) |

<sup>*</sup>Nod ~12 är för närvarande tillåtet på version 2.x av functions runtime. För bästa prestanda rekommenderar vi dock att du använder Funktioner runtime version 3.x med Nod ~12. 

Du kan se den aktuella versionen som körningen använder genom `process.version` att kontrollera ovanstående appinställning eller genom att skriva ut från valfri funktion. Rikta in versionen i Azure genom att ange [WEBSITE_NODE_DEFAULT_VERSION appinställningen](functions-how-to-use-azure-function-app-settings.md#settings) till `~10`en LTS-version som stöds, till exempel .

## <a name="dependency-management"></a>Beroendehantering
För att kunna använda communitybibliotek i javascript-koden, vilket visas i exemplet nedan, måste du se till att alla beroenden är installerade på din funktionsapp i Azure.

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
> Du bör `package.json` definiera en fil i roten till funktionsappen. Genom att definiera filen kan alla funktioner i appen dela samma cachelagrade paket, vilket ger bästa prestanda. Om en versionskonflikt uppstår kan du lösa `package.json` den genom att lägga till en fil i mappen för en viss funktion.  

När du distribuerar funktionsappar från källkontrollen utlöser `package.json` `npm install` alla filer som finns i repo-enheten en i mappen under distributionen. Men när du distribuerar via portalen eller CLI måste du installera paketen manuellt.

Det finns två sätt att installera paket på din funktionsapp: 

### <a name="deploying-with-dependencies"></a>Distribuera med beroenden
1. Installera alla nödvändiga paket lokalt genom `npm install`att köra .

2. Distribuera koden och se `node_modules` till att mappen ingår i distributionen. 


### <a name="using-kudu"></a>Använda Kudu
1. Gå till `https://<function_app_name>.scm.azurewebsites.net`.

2. Klicka på **Felsökkonsol** > **CMD**.

3. Gå `D:\home\site\wwwroot`till och dra sedan filen package.json till **wwwroot-mappen** längst upp på sidan.  
    Du kan ladda upp filer till din funktionsapp på andra sätt också. Mer information finns i [Så här uppdaterar du funktionsappfiler](functions-reference.md#fileupdate). 

4. När package.json-filen har laddats upp kör du `npm install` kommandot i **Kudu-fjärrkörningskonsolen**.  
    Den här åtgärden hämtar de paket som anges i filen package.json och startar om funktionsappen.

## <a name="environment-variables"></a>Miljövariabler

I Funktioner visas [appinställningar](functions-app-settings.md), till exempel tjänstanslutningssträngar, som miljövariabler under körningen. Du kan komma `process.env`åt dessa inställningar med , som `context.log()` visas här `AzureWebJobsStorage` `WEBSITE_SITE_NAME` i den andra och tredje samtal till där vi loggar och miljö variabler:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

När du kör lokalt läses appinställningarna från projektfilen [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="configure-function-entry-point"></a>Konfigurera funktionspostpunkt

Egenskaperna `function.json` `scriptFile` och `entryPoint` kan användas för att konfigurera platsen och namnet på den exporterade funktionen. Dessa egenskaper kan vara viktiga när ditt JavaScript är transpiled.

### <a name="using-scriptfile"></a>Använda `scriptFile`

Som standard körs en JavaScript-funktion från `index.js`, en fil som `function.json`delar samma överordnade katalog som motsvarande .

`scriptFile`kan användas för att hämta en mappstruktur som ser ut som följande exempel:

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

Den `function.json` `myNodeFunction` för bör `scriptFile` innehålla en egenskap som pekar på filen med den exporterade funktionen att köra.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Använda `entryPoint`

I `scriptFile` (eller `index.js`) måste en `module.exports` funktion exporteras med för att hittas och köras. Som standard är funktionen som körs när den utlöses den enda `run`exporten från `index`filen, exporten med namnet eller exporten med namnet .

Detta kan konfigureras `entryPoint` `function.json`med hjälp av i , som i följande exempel:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

I Functions v2.x, `this` som stöder parametern i användarfunktioner, kan funktionskoden sedan vara som i följande exempel:

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

I det här exemplet är det viktigt att notera att även om ett objekt exporteras finns det inga garantier för att bevara tillståndet mellan körningar.

## <a name="local-debugging"></a>Lokal felsökning

När du `--inspect` börjar med parametern lyssnar en Node.js-process efter en felsökningsklient på den angivna porten. I Azure Functions 2.x kan du ange argument som ska överföras till nod.js-processen som kör koden genom att lägga till miljövariabeln eller Appinställningen `languageWorkers:node:arguments = <args>`. 

Om du vill felsöka lokalt lägger du till `"languageWorkers:node:arguments": "--inspect=5858"` under `Values` i filen [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) och bifogar en felsökare till port 5858.

När du felsöker vs-kod läggs `--inspect` parametern `port` automatiskt till med hjälp av värdet i projektets launch.json-fil.

I version 1.x `languageWorkers:node:arguments` fungerar inställningen inte. Felsökningsporten kan väljas [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) med parametern på Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

När du inriktar dig på version 2.x av funktionskörningen kan både [Azure Functions for Visual Studio Code](functions-create-first-function-vs-code.md) och Azure Functions Core [Tools](functions-run-local.md) du skapa funktionsappar med hjälp av en mall som stöder TypeScript-funktionsappprojekt. Mallen genererar `package.json` `tsconfig.json` och projektfiler som gör det enklare att transpile, köra och publicera JavaScript-funktioner från TypeScript-kod med dessa verktyg.

En `.funcignore` genererad fil används för att ange vilka filer som är undantagna när ett projekt publiceras på Azure.  

TypeScript-filer (.ts) överförs till JavaScript-filer (.js) i `dist` utdatakatalogen. TypeScript-mallar använder `function.json` [ `scriptFile` parametern](#using-scriptfile) i för att ange platsen `dist` för motsvarande .js-fil i mappen. Utdataplatsen anges av mallen `outDir` med `tsconfig.json` hjälp av parametern i filen. Om du ändrar den här inställningen eller namnet på mappen kan körningen inte hitta den kod som ska köras.

> [!NOTE]
> Experimentellt stöd för TypeScript finns version 1.x av funktionskörningen. Den experimentella versionen transpiles TypeScript-filer till JavaScript-filer när funktionen anropas. I version 2.x har det här experimentella stödet ersatts av den verktygsdrivna metoden som gör transpilation innan värden initieras och under distributionsprocessen.

Hur du utvecklar och distribuerar lokalt från ett TypeScript-projekt beror på ditt utvecklingsverktyg.

### <a name="visual-studio-code"></a>Visual Studio-koden

Med [Azure Functions for Visual Studio-kodtillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) kan du utveckla dina funktioner med TypeScript. Core Tools är ett krav i Azure Functions-tillägget.

Om du vill skapa en TypeScript-funktionsapp i Visual Studio-kod väljer du `TypeScript` som språk när du skapar en funktionsapp.

När du trycker på **F5** för att köra appen lokalt sker transpilation innan värden (func.exe) initieras. 

När du distribuerar din funktionsapp till Azure med hjälp av knappen **Distribuera för att fungera app...** genererar Azure Functions-tillägget först en produktionsklar version av JavaScript-filer från TypeScript-källfilerna.

### <a name="azure-functions-core-tools"></a>Grundläggande verktyg för Azure-funktioner

Det finns flera sätt på vilka ett TypeScript-projekt skiljer sig från ett JavaScript-projekt när du använder kärnverktygen.

#### <a name="create-project"></a>Skapa projekt

Om du vill skapa ett TypeScript-funktionsappprojekt med Hjälp av Core Tools måste du ange språkalternativet TypeScript när du skapar funktionsappen. Du kan göra detta på något av följande sätt:

- Kör `func init` kommandot, `node` välj som språkstack `typescript`och välj sedan .

- Kör `func init --worker-runtime typescript`-kommandot.

#### <a name="run-local"></a>Kör lokalt

Om du vill köra funktionsappkoden lokalt med Core `func host start`Tools använder du följande kommandon i stället för: 

```command
npm install
npm start
```

Kommandot `npm start` motsvarar följande kommandon:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publicera till Azure

Innan du [`func azure functionapp publish`] använder kommandot för att distribuera till Azure skapar du en produktionsklar version av JavaScript-filer från TypeScript-källfilerna. 

Följande kommandon förbereder och publicerar TypeScript-projektet med Hjälp av Core Tools: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Ersätt med `<APP_NAME>` namnet på funktionsappen i det här kommandot.

## <a name="considerations-for-javascript-functions"></a>Överväganden för JavaScript-funktioner

När du arbetar med JavaScript-funktioner bör du vara medveten om vad som anges i följande avsnitt.

### <a name="choose-single-vcpu-app-service-plans"></a>Välj abonnemang för apptjänst med en vCPU

När du skapar en funktionsapp som använder App Service-planen rekommenderar vi att du väljer en enda vCPU-plan i stället för en plan med flera virtuella processorer. Idag kör Functions JavaScript-funktioner mer effektivt på virtuella processorer med en vCPU, och med större virtuella datorer ger inte de förväntade prestandaförbättringarna. Vid behov kan du skala ut manuellt genom att lägga till fler single-vCPU VM-instanser, eller så kan du aktivera automatisk skalning. Mer information finns i [Skala instansantal manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Kallstart

När du utvecklar Azure Functions i den serverlösa värdmodellen är kalla starter verklighet. *Kallstart* refererar till det faktum att när din funktionsapp startar för första gången efter en period av inaktivitet tar det längre tid att starta. För JavaScript-funktioner med stora beroendeträd i synnerhet kan kallstart vara betydande. För att påskynda kallstartsprocessen, [kör dina funktioner som en paketfil](run-functions-from-deployment-package.md) när det är möjligt. Många distributionsmetoder använder körningen från paketmodellen som standard, men om du upplever stora kalla starter och inte körs på det här sättet kan den här ändringen erbjuda en betydande förbättring.

### <a name="connection-limits"></a>Anslutningsgränser

När du använder en tjänstspecifik klient i ett Azure Functions-program ska du inte skapa en ny klient med varje funktionsanrop. Skapa i stället en enda statisk klient i det globala scopet. Mer information finns [i hantera anslutningar i Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Användning `async` och`await`

När du skriver Azure Functions i JavaScript `async` `await` bör du skriva kod med nyckelorden och nyckelorden. Skriva kod `async` `await` med hjälp av `.then` och `.catch` istället för motringningar eller och med Löften hjälper till att undvika två vanliga problem:
 - Om du kastar undantag som [inte utlöses som kraschar nod.js-processen](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), vilket kan påverka körningen av andra funktioner.
 - Oväntat beteende, till exempel saknade loggar från context.log, orsakade av asynkrona anrop som inte är rätt efterlängtade.

I exemplet nedan anropas den asynkrona metoden `fs.readFile` med en fel-första motringningsfunktion som den andra parametern. Den här koden orsakar båda de problem som nämns ovan. Ett undantag som inte uttryckligen fångas i rätt omfattning kraschade hela processen (problem #1). Anropa `context.done()` utanför motringningsfunktionens omfattning innebär att funktionsanropet kan avslutas innan filen läss (problem #2). I det här `context.done()` exemplet resulterar det att `Data from file:`det saknas loggposter som börjar med .

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

Med `async` hjälp `await` av och nyckelord hjälper till att undvika båda dessa fel. Du bör använda verktyget Node.js [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) för att omvandla fel-första motringningsliknande funktioner till väntande funktioner.

I exemplet nedan misslyckas alla ohanterade undantag som genereras under funktionskörningen bara den enskilda åkallan som tog upp ett undantag. Nyckelordet `await` innebär att `readFileAsync` stegen `readFile` efter endast körs när det är klart. Med `async` `await`och behöver du inte heller `context.done()` ringa motringningen.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
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

["func azure functionapp publish"]: functions-run-local.md#project-file-deployment
