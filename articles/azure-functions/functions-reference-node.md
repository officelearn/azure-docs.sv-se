---
title: JavaScript-utvecklare för Azure Functions | Microsoft Docs
description: Förstå hur du utvecklar funktioner med hjälp av JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: eb9387cec98621e27aff7dcb40b8897e326c6706
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353500"
---
# <a name="azure-functions-javascript-developer-guide"></a>Utvecklarguide för Azure Functions JavaScript
Den här guiden innehåller information om krångla skriva Azure Functions med JavaScript.

En JavaScript-funktion är en exporterad `function` som körs när den utlöses ([utlösare har konfigurerats i function.json](functions-triggers-bindings.md)). Varje funktion skickas en `context` objektet som används för mottagning och skicka bindningsdata, loggning och kommunicera med körningen.

Den här artikeln förutsätter att du redan har läst den [Azure Functions för utvecklare](functions-reference.md). Vi rekommenderar också att du har följt en självstudiekurs i ”Snabbstarter” till [skapa din första funktion](functions-create-first-function-vs-code.md).

## <a name="folder-structure"></a>mappstruktur

Det ser ut som följande nödvändiga mappstrukturen för en JavaScript-projektet. Observera att du kan ändra denna standardinställning: finns i den [skriptfil](functions-reference-node.md#using-scriptfile) avsnittet nedan för mer information.

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
 | - bin
```

I roten av projektet, det finns en delad [host.json](functions-host-json.md) -fil som kan användas för att konfigurera funktionsappen. Varje funktion har en mapp med en egen fil med kod (.js) och bindningen konfigurationsfil (function.json).

Bindningen-tillägg som krävs i [version 2.x](functions-versions.md) funktioner runtime definieras i den `extensions.csproj` -fil med faktiska library-filer i den `bin` mapp. När du utvecklar lokalt, måste du [registrera tillägg av bindning](functions-triggers-bindings.md#local-development-azure-functions-core-tools). När du utvecklar funktioner i Azure-portalen görs denna registrering för dig.

## <a name="exporting-a-function"></a>Exportera en funktion

JavaScript-funktioner måste exporteras [ `module.exports` ](https://nodejs.org/api/modules.html#modules_module_exports) (eller [ `exports` ](https://nodejs.org/api/modules.html#modules_exports)). I standard-fall din exporterade funktion ska vara den enda exporten från dess fil, export med namnet `run`, eller export med namnet `index`. Standardplatsen för din funktion `index.js`, där `index.js` delar samma överordnad katalog som motsvarande `function.json`. Observera att namnet på `function.json`'s överordnad katalog är alltid namnet på din funktion. 

Om du vill konfigurera filens plats och exportera namnet på din funktion, Läs om [konfigurerar din funktionsadressen](functions-reference-node.md#configure-function-entry-point) nedan.

Din startpunkt för exporterade funktionen alltid vidta en `context` objektet som första parameter.

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```
```javascript
// You can also use 'arguments' to dynamically handle inputs
module.exports = async function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
};
```

Utlösare och bindningar för indata (bindningarna för `direction === "in"`) kan skickas till funktionen som parametrar. De skickas till funktionen i samma ordning som de har definierats i *function.json*. Du kan också dynamiskt hantera indata med hjälp av JavaScript [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) objekt. Om du har till exempel `function(context, a, b)` och ändra den till `function(context, a)`, du kan fortfarande få värdet för `b` i Funktionskoden genom att referera till `arguments[2]`.

Alla bindningar, oavsett riktning, skickas även vidare den `context` objekt med hjälp av den `context.bindings` egenskapen.

### <a name="exporting-an-async-function"></a>Exportera en async-funktion
När du använder JavaScript [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarationen eller vanlig JavaScript [löften](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) (inte tillgängligt med Functions v1.x), du uttryckligen behöver inte anropa den [ `context.done` ](#contextdone-method) återanrop för att signalera att funktionen har slutförts. Slutför din funktion när exporterade async-funktion/löftet har slutförts.

Till exempel är det här en enkel funktion som loggar den utlöstes och omedelbart är slutfört.
``` javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

När du exporterar en async-funktion kan du också konfigurera utdatabindningar att ta den `return` värde. Det här är en annan metod för tilldelning av utdata med hjälp av den [ `context.bindings` ](#contextbindings-property) egenskapen.

Tilldela en utdata med hjälp av `return`, ändra den `name` egenskap `$return` i `function.json`.
```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```
Funktionskoden JavaScript kan se ut så här:
```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="context-object"></a>Context-objektet
Körningen använder en `context` objekt att skicka data till och från din funktion och så att du kan kommunicera med körningen.

Den `context` objektet är alltid den första parametern för en funktion och måste tas eftersom den har metoder som `context.done` och `context.log`, vilket krävs för att använda körningen på rätt sätt. Du kan kalla objektet vad du vill ha (till exempel `ctx` eller `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Context.Bindings egenskap

```
context.bindings
```
Returnerar ett namngivna objekt som innehåller alla inkommande och utgående data. Till exempel följande bindningsdefinitionerna i din *function.json* kan du få åtkomst till innehållet i en kö från `context.bindings.myInput` och tilldela utdata till en kö med hjälp av `context.bindings.myOutput`.

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

Observera att du kan välja att definiera utdata bindning data med den `context.done` metoden i stället för den `context.binding` objekt (se nedan).

### <a name="contextbindingdata-property"></a>context.bindingData egenskap

```
context.bindingData
```
Returnerar en namngiven objekt som innehåller data för utlösaren metadata och funktionen anrop (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Ett exempel på utlösaren metadata finns i den här [event hubs exempel](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Context.Done metod
```
context.done([err],[propertyBag])
```

Informerar den runtime som koden har slutförts. Om din funktion använder JavaScript [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarationen (tillgängligt med hjälp av Node 8 + i Functions version 2.x), du behöver inte använda `context.done()`. Den `context.done` anropas implicit återanrop.

Om funktionen inte är en async-funktion, **måste du anropa** `context.done` att informera körningen att funktionen har slutförts. Körningen når tidsgränsen om den saknas.

Den `context.done` metoden kan du ange både en användardefinierad fel att körningen och ett JSON-objekt som innehåller utdata-bindning. Egenskaper som skickas till `context.done` skrivs allt på den `context.bindings` objekt.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context.log metod  

```
context.log(message)
```
Gör att du kan skriva till direktuppspelningsloggarna funktion på standardnivå för spårningen. På `context.log`, ytterligare loggning metoder är tillgängliga som gör att du sparar funktionsloggar på andra spårningsnivåer:


| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **fel (_meddelande_)**   | Skriver till Felnivån loggningen eller lägre.   |
| **Varna (_meddelande_)**    | Skriver till varningsnivå loggningen eller lägre. |
| **information (_meddelande_)**    | Skriver till info-nivån loggningen eller lägre.    |
| **utförlig (_meddelande_)** | Skriver till utförlig loggning för nivån.           |

I följande exempel skriver en logg vid spårningsnivån varning:

```javascript
context.log.warn("Something has happened."); 
```
Du kan [konfigurera spårningsnivå tröskelvärdet för loggning](#configure-the-trace-level-for-console-logging) i host.json-filen. Mer information om hur du skriver loggar finns i [skriva trace utdata](#writing-trace-output-to-the-console) nedan.

Läs [övervaka Azure Functions](functions-monitoring.md) vill veta mer om att visa och fråga funktionsloggar.

## <a name="binding-data-type"></a>Bindningstyp för data

För att definiera datatypen för en indatabindning använder den `dataType` -egenskapen i Bindningsdefinitionen för. Till exempel använda typen för att läsa innehållet i en HTTP-förfrågan i binärformat `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Alternativ för `dataType` är: `binary`, `stream`, och `string`.

## <a name="writing-trace-output-to-the-console"></a>Skrivning spårningsutdata till konsolen 

I funktioner, använder du den `context.log` metoder för att skriva spårningsutdata till konsolen. Spåra ouputs via i Functions v2.x `console.log` samlas på Funktionsapp-nivå. Det innebär att utdata från `console.log` inte är knutna till en specifik funktionsanrop och kan därför inte visas i loggarna för en specifik funktion. De kommer dock att spridas till Application Insights. Du kan inte använda i Functions v1.x `console.log` att skriva till konsolen. 

När du anropar `context.log()`, meddelandet skrivs till konsolen vid spårningsnivån standard, vilket är den _info_ spårningsnivå. Följande kod skriver till konsolen vid spårningsnivån info:

```javascript
context.log({hello: 'world'});  
```

Den här koden motsvarar koden ovan:

```javascript
context.log.info({hello: 'world'});  
```

Den här koden skriver till konsolen vid Felnivån:

```javascript
context.log.error("An error has occurred.");  
```

Eftersom _fel_ är högsta spårningen loggningsnivån genom den här skrivs spåret till utdata i alla spårningsnivåer som har aktiverats.

Alla `context.log` metoderna stöder samma parameter-format som stöds av Node.js [util.format metoden](https://nodejs.org/api/util.html#util_util_format_format). Titta på följande kod som skriver funktionsloggar med hjälp av standard spårningsnivån:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Du kan också skriva samma kod i följande format:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Konfigurera Spårningsnivån för konsolen loggning

Functions kan du definiera spårningsnivån tröskelvärdet för att skriva till konsolen, vilket gör det enkelt att styra sätt spårningar har skrivits till konsolen från dina funktioner. Ange tröskelvärdet för alla spårningar som skrivs till konsolen och den `tracing.consoleLevel` egenskap i host.json-fil. Den här inställningen gäller för alla funktioner i din funktionsapp. I följande exempel anger tröskelvärdet spårning för att aktivera utförlig loggning:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Värden för **consoleLevel** motsvarar namnen på de `context.log` metoder. För att inaktivera alla spårningsloggning till konsolen, ange **consoleLevel** till _av_. Mer information finns i [referens för host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP-utlösare och bindningar

HTTP- och webhook-utlösare och HTTP-utdata bindningar använda begäranden och svar-objekt som representerar den HTTP-meddelanden.  

### <a name="request-object"></a>Objekt

Den `context.req` (begäran) objekt har följande egenskaper:

| Egenskap       | Beskrivning                                                    |
| ------------- | -------------------------------------------------------------- |
| _Brödtext_        | Ett objekt som innehåller brödtext för begäran.               |
| _Rubriker_     | Ett objekt som innehåller de begärda rubrikerna.                   |
| _Metoden_      | HTTP-metod för begäran.                                |
| _originalUrl_ | URL för begäran.                                        |
| _parametrar_      | Ett objekt som innehåller parametrarna routning av begäran. |
| _Fråga_       | Ett objekt som innehåller frågeparametrarna.                  |
| _rawBody_     | Brödtexten i meddelandet som en sträng.                           |


### <a name="response-object"></a>Svarsobjekt

Den `context.res` ()-svarsobjekt har följande egenskaper:

| Egenskap   | Beskrivning                                               |
| --------- | --------------------------------------------------------- |
| _Brödtext_    | Ett objekt som innehåller brödtexten i svaret.         |
| _Rubriker_ | Ett objekt som innehåller svarshuvuden.             |
| _isRaw_   | Anger att formatering hoppas för svaret.    |
| _Status_  | HTTP-statuskod i svaret.                     |

### <a name="accessing-the-request-and-response"></a>Åtkomst till begäranden och svar 

När du arbetar med HTTP-utlösare kan komma du åt HTTP-begäranden och svar-objekt på flera olika sätt:

+ Från `req` och `res` egenskaper på den `context` objekt. På så sätt kan du använda det vanliga mönstret att komma åt HTTP data från context-objektet i stället för att använda fullständiga `context.bindings.name` mönster. I följande exempel visas hur du kommer åt den `req` och `res` objekt på den `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Från namngivna indata och utdatabindningar. På så sätt kan fungerar HTTP-utlösare och bindningar på samma som för andra bindningen. I följande exempel anges svarsobjekt med hjälp av en namngiven `response` bindning: 

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
+ _[Endast svar]_  Genom att anropa `context.res.send(body?: any)`. Ett HTTP-svar skapas med indata `body` som svarstexten. `context.done()` anropas implicit.

+ _[Endast svar]_  Genom att anropa `context.done()`. En särskild typ av HTTP-bindning returnerar ett svar som skickas till den `context.done()` metoden. Följande HTTP-utdatabindning definierar en `$return` utdataparameter:

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

## <a name="node-version"></a>Nodversionen

I följande tabell visas Node.js-version som används av varje huvudversion av Functions-körning:

| Functions-version | Node.js-version | 
|---|---|
| 1.x | 6.11.2 (låst av körningen) |
| 2.x  | _Aktiva LTS_ och _aktuella_ Node.js-versioner (8.11.1 och 10.6.0 rekommenderas). Ange version med hjälp av WEBSITE_NODE_DEFAULT_VERSION [appinställningen](functions-how-to-use-azure-function-app-settings.md#settings).|

Du kan se den aktuella versionen som körningen använder genom att kontrollera inställningarna ovan appen eller genom att skriva ut `process.version` från valfri funktion.

## <a name="dependency-management"></a>Beroendehantering
För att kunna använda community-biblioteken i JavaScript-koden som visas i det exemplet nedan måste du kontrollera att alla beroenden är installerade på din Funktionsapp i Azure.

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
> Du bör definiera en `package.json` i roten på din Funktionsapp. Definiera filen kan alla funktioner i appen som delar samma cachelagrade paket, vilket ger bästa prestanda. Om det uppstår en versionskonflikt, du kan lösa problemet genom att lägga till en `package.json` filen i mappen för en specifik funktion.  

När du distribuerar Funktionsappar från källkontroll, alla `package.json` filen finns i ditt lager, utlöser en `npm install` i en mapp under distributionen. Men när du distribuerar via portalen eller CLI så du måste manuellt installera paket.

Det finns två sätt att installera paket på din Funktionsapp: 

### <a name="deploying-with-dependencies"></a>Distribuera med beroenden
1. Installera alla nödvändiga paket lokalt genom att köra `npm install`.

2. Distribuera din kod och se till att den `node_modules` mappen ingår i distributionen. 


### <a name="using-kudu"></a>Med Kudu
1. Gå till `https://<function_app_name>.scm.azurewebsites.net`.

2. Klicka på **felsöka konsolen** > **CMD**.

3. Gå till `D:\home\site\wwwroot`, och dra sedan package.json-fil till den **wwwroot** mapp på den övre delen av sidan.  
    Du kan också överföra filer till din funktionsapp på andra sätt. Mer information finns i [så här uppdaterar du funktionen appfiler](functions-reference.md#fileupdate). 

4. När package.json-fil har överförts, kör den `npm install` i den **Kudu-konsolen för fjärrkörning**.  
    Den här åtgärden hämtar de paket som anges i package.json-fil och startar om appen.

## <a name="environment-variables"></a>Miljövariabler
Hämta en miljövariabel eller en app som inställningsvärde `process.env`, vilket visas här i den `GetEnvironmentVariable` funktionen:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="configure-function-entry-point"></a>Konfigurera funktionens startadress

Den `function.json` egenskaper `scriptFile` och `entryPoint` kan användas för att konfigurera platsen och namnet på din exporterade funktion. Det kan vara viktigt om din JavaScript är transpiled.

### <a name="using-scriptfile"></a>Med hjälp av `scriptFile`

Som standard körs en JavaScript-funktion från `index.js`, en fil som delar samma överordnad katalog som dess motsvarande `function.json`.

`scriptFile` kan användas för att få en mappstruktur som ser ut så här:
```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Den `function.json` för `myNodeFunction` bör innehålla en `scriptFile` egenskap som pekar på filen med exporterade funktionen ska köras.
```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Med hjälp av `entryPoint`

I `scriptFile` (eller `index.js`), en funktion måste exporteras med `module.exports` för att hitta och köra. Som standard är den funktion som körs när den utlöses endast export från filen, export med namnet `run`, eller export med namnet `index`.

Detta kan konfigureras med hjälp av `entryPoint` i `function.json`:
```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

I funktioner v2.x som har stöd för den `this` parameter i användarfunktioner Funktionskoden kan sedan vara på följande sätt:
```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };
    
    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

I det här exemplet är det viktigt att Observera att även om ett objekt ska exporteras, det finns inga guarantess runt bevara tillstånd mellan körningar.

## <a name="considerations-for-javascript-functions"></a>Överväganden för JavaScript-funktioner

När du arbetar med JavaScript-funktioner måste du vara medveten om överväganden i följande avsnitt.

### <a name="choose-single-vcpu-app-service-plans"></a>Välj en vCPU App Service-planer

När du skapar en funktionsapp som använder App Service-planen, rekommenderar vi att du väljer en enda vCPU-plan i stället för en plan med flera virtuella processorer. Idag funktioner körs mer effektivt JavaScript-funktioner på enskild vCPU virtuella datorer och med större virtuella datorer genererar inte de förväntade prestandaförbättringarna. Om det behövs kan du manuellt skala ut genom att lägga till flera enskild vCPU VM-instanser eller du kan aktivera automatisk skalning. Mer information finns i [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Stöd för TypeScript och CoffeeScript
Eftersom direktstöd ännu inte finns för automatisk kompilering av TypeScript eller CoffeeScript via körningen, behöver stödet hanteras utanför körning, vid tidpunkten för distribution.  

### <a name="cold-start"></a>Kallstart
När börjar utveckla Azure Functions i utan server som värd modellen kalla är verklighet. ”Kallstart” avser faktumet att när appen startas för första gången efter en tids inaktivitet, det tar längre tid att starta. För JavaScript-funktioner med stora beroendeträd särskilt kan detta medföra större minskningen. För att skynda processen, om möjligt [kör dina funktioner som en paketfil](run-functions-from-deployment-package.md). Många distributionsmetoder välja i den här modellen som standard, men om du arbetar med stora kallstarter och inte kör en paketfil från kan detta kan vara en enorm förbättring.

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

