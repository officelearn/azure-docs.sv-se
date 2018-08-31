---
title: JavaScript-utvecklare för Azure Functions | Microsoft Docs
description: Förstå hur du utvecklar funktioner med hjälp av JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: 6099a818651cf75a75159f43748720b3eb01e4de
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287829"
---
# <a name="azure-functions-javascript-developer-guide"></a>Utvecklarguide för Azure Functions JavaScript

JavaScript-upplevelsen för Azure Functions gör det enkelt att exportera en funktion som skickas som en `context` objekt för att kommunicera med körningen och för att ta emot och skicka data via bindningar.

Den här artikeln förutsätter att du redan har läst den [Azure Functions för utvecklare](functions-reference.md).

## <a name="exporting-a-function"></a>Exportera en funktion
Varje JavaScript-funktion måste exportera en enda `function` via `module.exports` för körning för att hitta funktionen och kör den. Den här funktionen måste alltid ta en `context` objektet som första parameter.

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
// You can also use 'arguments' to dynamically handle inputs
module.exports = function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
    context.done();
};
```

Bindningar för indata och utlösare (bindningarna för `direction === "in"`) kan skickas till funktionen som parametrar. De skickas till funktionen i samma ordning som de har definierats i *function.json*. Du kan dynamiskt hantera indata med hjälp av JavaScript [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) objekt. Om du har till exempel `function(context, a, b)` och ändra den till `function(context, a)`, du kan fortfarande få värdet för `b` i Funktionskoden genom att referera till `arguments[2]`.

Alla bindningar, oavsett riktning, skickas även vidare den `context` objekt med hjälp av den `context.bindings` egenskapen.

## <a name="context-object"></a>Context-objektet
Körningen använder en `context` objekt att skicka data till och från din funktion och så att du kan kommunicera med körningen.

Den `context` objektet är alltid den första parametern för en funktion och måste tas eftersom den har metoder som `context.done` och `context.log`, vilket krävs för att använda körningen på rätt sätt. Du kan kalla objektet vad du vill ha (till exempel `ctx` eller `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
    context.done();
};
```

### <a name="contextbindings-property"></a>Context.Bindings egenskap

```
context.bindings
```
Returnerar ett namngivna objekt som innehåller alla inkommande och utgående data. Till exempel följande Bindningsdefinitionen i din *function.json* kan du få åtkomst till innehållet i kö från den `context.bindings.myInput` objekt. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
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

### <a name="contextdone-method"></a>Context.Done metod
```
context.done([err],[propertyBag])
```

Informerar den runtime som koden har slutförts. Om din funktion använder den `async function` deklarationen (tillgängligt med hjälp av Node 8 + i Functions version 2.x), du behöver inte använda `context.done()`. Den `context.done` anropas implicit återanrop.

Om funktionen inte är en async-funktion, **måste du anropa `context.done`**  att informera körningen att funktionen har slutförts. Körningen når tidsgränsen om den saknas.

Den `context.done` metoden kan du ange både en användardefinierad fel att körningen och en egenskapsuppsättning av egenskaperna som skriver över egenskaperna på den `context.bindings` objekt.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Context.log metod  

```
context.log(message)
```
Gör att du kan skriva till direktuppspelningsloggarna konsolen på standardnivå för spårningen. På `context.log`, ytterligare loggning metoder är tillgängliga som gör att du bara behöver skriva till konsolloggen på andra spårningsnivåer:


| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **fel (_meddelande_)**   | Skriver till Felnivån loggningen eller lägre.   |
| **Varna (_meddelande_)**    | Skriver till varningsnivå loggningen eller lägre. |
| **information (_meddelande_)**    | Skriver till info-nivån loggningen eller lägre.    |
| **utförlig (_meddelande_)** | Skriver till utförlig loggning för nivån.           |

I följande exempel skriver till konsolen vid varningsspårningsnivå:

```javascript
context.log.warn("Something has happened."); 
```
Du kan ange spårningsnivå tröskelvärdet för att logga in filen host.json eller stänga av den.  Mer information om hur du kan skriva till loggar finns i nästa avsnitt.

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

Andra alternativ för `dataType` är `stream` och `string`.

## <a name="writing-trace-output-to-the-console"></a>Skrivning spårningsutdata till konsolen 

I funktioner, använder du den `context.log` metoder för att skriva spårningsutdata till konsolen. Nu kan du inte använda `console.log` att skriva till konsolen.

När du anropar `context.log()`, meddelandet skrivs till konsolen vid spårningsnivån standard, vilket är den _info_ spårningsnivå. Följande kod skriver till konsolen vid spårningsnivån info:

```javascript
context.log({hello: 'world'});  
```

Föregående kod motsvarar följande kod:

```javascript
context.log.info({hello: 'world'});  
```

Följande kod skriver till konsolen vid Felnivån:

```javascript
context.log.error("An error has occurred.");  
```

Eftersom _fel_ är högsta spårningen loggningsnivån genom den här skrivs spåret till utdata i alla spårningsnivåer som har aktiverats.  


Alla `context.log` metoderna stöder samma parameter-format som stöds av Node.js [util.format metoden](https://nodejs.org/api/util.html#util_util_format_format). Titta på följande kod som skriver till konsolen med hjälp av standard spårningsnivån:

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

Den `request` objekt har följande egenskaper:

| Egenskap       | Beskrivning                                                    |
| ------------- | -------------------------------------------------------------- |
| _Brödtext_        | Ett objekt som innehåller brödtext för begäran.               |
| _Rubriker_     | Ett objekt som innehåller de begärda rubrikerna.                   |
| _Metoden_      | HTTP-metod för begäran.                                |
| _originalUrl_ | URL för begäran.                                        |
| _parametrar_      | Ett objekt som innehåller parametrarna routning av begäran. |
| _Fråga_       | Ett objekt som innehåller frågeparametrarna.                  |
| _rawBody_     | Brödtexten i meddelandet som en sträng.                           |


### <a name="response-object"></a>-Svarsobjekt

Den `response` objekt har följande egenskaper:

| Egenskap   | Beskrivning                                               |
| --------- | --------------------------------------------------------- |
| _Brödtext_    | Ett objekt som innehåller brödtexten i svaret.         |
| _Rubriker_ | Ett objekt som innehåller svarshuvuden.             |
| _isRaw_   | Anger att formatering hoppas för svaret.    |
| _Status_  | HTTP-statuskod i svaret.                     |

### <a name="accessing-the-request-and-response"></a>Åtkomst till begäranden och svar 

När du arbetar med HTTP-utlösare kan komma du åt HTTP-begäranden och svar-objekt på något av tre sätt:

+ Från namngivna indata och utdatabindningar. På så sätt kan fungerar HTTP-utlösare och bindningar på samma som för andra bindningen. I följande exempel anges svarsobjekt med hjälp av en namngiven `response` bindning: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ Från `req` och `res` egenskaper på den `context` objekt. På så sätt kan du använda det vanliga mönstret att komma åt HTTP data från context-objektet i stället för att använda fullständiga `context.bindings.name` mönster. I följande exempel visas hur du kommer åt den `req` och `res` objekt på den `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Genom att anropa `context.done()`. En särskild typ av HTTP-bindning returnerar ett svar som skickas till den `context.done()` metoden. Följande HTTP-utdatabindning definierar en `$return` utdataparameter:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    Den här utdatabindning förväntar sig att du kan ange svaret när du anropar `done()`, enligt följande:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Hantering av version och paket

I följande tabell visas Node.js-version som används av varje huvudversion av Functions-körning:

| Functions-version | Node.js-version | 
|---|---|
| 1.x | 6.11.2 (låst av körningen) |
| 2.x  | _Aktiva LTS_ och _aktuella_ Node.js-versioner (8.11.1 och 10.6.0 rekommenderas). Ange version med hjälp av WEBSITE_NODE_DEFAULT_VERSION [appinställningen](functions-how-to-use-azure-function-app-settings.md#settings).|

Du kan se den aktuella versionen med hjälp av körningen genom att skriva ut `process.version` från valfri funktion.

Följande steg kan du inkludera paket i din funktionsapp: 

1. Gå till `https://<function_app_name>.scm.azurewebsites.net`.

2. Klicka på **felsöka konsolen** > **CMD**.

3. Gå till `D:\home\site\wwwroot`, och dra sedan package.json-fil till den **wwwroot** mapp på den övre delen av sidan.  
    Du kan också överföra filer till din funktionsapp på andra sätt. Mer information finns i [så här uppdaterar du funktionen appfiler](functions-reference.md#fileupdate). 

4. När package.json-fil har överförts, kör den `npm install` i den **Kudu-konsolen för fjärrkörning**.  
    Den här åtgärden hämtar de paket som anges i package.json-fil och startar om appen.

När du har installerat de paket som du behöver du ska importera dem till din funktion genom att anropa `require('packagename')`, som i följande exempel:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Du bör definiera en `package.json` i roten på din funktionsapp. Definiera filen kan alla funktioner i appen som delar samma cachelagrade paket, vilket ger bästa prestanda. Om det uppstår en versionskonflikt, du kan lösa problemet genom att lägga till en `package.json` filen i mappen för en specifik funktion.  

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
## <a name="considerations-for-javascript-functions"></a>Överväganden för JavaScript-funktioner

När du arbetar med JavaScript-funktioner måste du vara medveten om överväganden i följande två avsnitt.

### <a name="choose-single-vcpu-app-service-plans"></a>Välj en vCPU App Service-planer

När du skapar en funktionsapp som använder App Service-planen, rekommenderar vi att du väljer en enda vCPU-plan i stället för en plan med flera virtuella processorer. Idag funktioner körs mer effektivt JavaScript-funktioner på enskild vCPU virtuella datorer och med större virtuella datorer genererar inte de förväntade prestandaförbättringarna. Om det behövs kan du manuellt skala ut genom att lägga till flera enskild vCPU VM-instanser eller du kan aktivera automatisk skalning. Mer information finns i [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Stöd för TypeScript och CoffeeScript
Eftersom direktstöd ännu inte finns för automatisk kompilering av TypeScript eller CoffeeScript via körningen, behöver stödet hanteras utanför körning, vid tidpunkten för distribution.  

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

