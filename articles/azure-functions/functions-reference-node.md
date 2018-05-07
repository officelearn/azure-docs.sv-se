---
title: JavaScript-utvecklare för Azure Functions | Microsoft Docs
description: Förstå hur du utvecklar funktioner med hjälp av JavaScript.
services: functions
documentationcenter: na
author: tdykstra
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
ms.author: tdykstra
ms.openlocfilehash: e593f58f6b92e562067401381cfce06d5fb18e29
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="azure-functions-javascript-developer-guide"></a>Utvecklarhandbok för Azure Functions JavaScript

JavaScript-upplevelsen för Azure Functions gör det enkelt att exportera en funktion som har skickats som en `context` objekt för att kommunicera med körningen och för att ta emot och skicka data via bindningar.

Den här artikeln förutsätter att du redan har läst den [Azure Functions för utvecklare](functions-reference.md).

## <a name="exporting-a-function"></a>Exportera en funktion
Alla JavaScript-funktioner måste exportera en enda `function` via `module.exports` för runtime hitta funktionen och kör den. Den här funktionen måste alltid innehålla ett `context` objekt.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Bindningar för `direction === "in"` skickas vidare som funktionsargument, vilket innebär att du kan använda [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) dynamiskt hantera nya indata (till exempel med hjälp av `arguments.length` och iterera över alla indata). Den här funktionen är praktisk när du har bara en utlösare och inga ytterligare indata, eftersom du kan komma åt data utlösaren förutsägbart utan refererar till din `context` objekt.

Argumenten är alltid överföras till funktionen i den ordning som de sker i *function.json*, även om du inte anger dem i export-instruktionen. Om du har till exempel `function(context, a, b)` och ändra den till `function(context, a)`, du kan fortfarande få värdet för `b` i Funktionskoden genom att referera till `arguments[2]`.

Alla bindningar oavsett riktning, skickas även vidare den `context` objekt (se följande skript). 

## <a name="context-object"></a>Context-objektet
Körtidsbiblioteket använder en `context` objekt att skicka data till och från din funktion och så att du kan kommunicera med körningen.

Den `context` objektet är alltid den första parametern till en funktion och måste tas med eftersom den innehåller metoder som `context.done` och `context.log`, vilket krävs för att använda körningsmiljön på rätt sätt. Du kan kalla objektet vad du vill (till exempel `ctx` eller `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>Egenskapen Context.Bindings

```
context.bindings
```
Returnerar ett namngivet objekt som innehåller alla inkommande och utgående data. Till exempel följande bindning definition i din *function.json* kan du få åtkomst till innehållet i kö från den `context.bindings.myInput` objekt. 

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

### <a name="contextdone-method"></a>Context.Done-metoden
```
context.done([err],[propertyBag])
```

Informerar körningsmiljön koden har slutförts. Du måste anropa `context.done`, eller annan körningsmiljön aldrig vet att din funktion är klar och körningen gör timeout. 

Den `context.done` metod kan du skicka både en användardefinierad fel körningsmiljön och en egenskapsuppsättning egenskaper som åsidosätter egenskaper på den `context.bindings` objekt.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Context.log-metoden  

```
context.log(message)
```
Gör att du kan skriva till direktuppspelningsloggar konsolen på standardnivå för spårning. På `context.log`, ytterligare loggning metoder är tillgängliga som gör att du kan skriva till konsolen loggen på andra spårningsnivåer:


| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **fel (_meddelandet_)**   | Skriver till Felnivån loggningen eller lägre.   |
| **Varna (_meddelandet_)**    | Skriver till varningsnivå loggningen eller lägre. |
| **Info (_meddelandet_)**    | Skriver till info-nivån loggningen eller lägre.    |
| **utförlig (_meddelandet_)** | Skriver till nivån utförlig loggning.           |

I följande exempel skriver till konsolen vid spårningsnivån varning:

```javascript
context.log.warn("Something has happened."); 
```
Du kan ange spårningsnivå tröskelvärdet för loggning i filen host.json eller stänga av den.  Mer information om hur du kan skriva till loggar finns i nästa avsnitt.

## <a name="binding-data-type"></a>Datatypen för bindning

Om du vill definiera datatypen för en indatabindning den `dataType` egenskapen i Bindningsdefinitionen. Till exempel använda typen om du vill läsa innehållet i en HTTP-begäran i binärformat `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Andra alternativ för `dataType` är `stream` och `string`.

## <a name="writing-trace-output-to-the-console"></a>Skrivning av spårningsutdata till konsolen 

I funktion, använder du den `context.log` metoder för att skriva av spårningsutdata till konsolen. Nu kan du inte använda `console.log` att skriva till konsolen.

När du anropar `context.log()`, meddelandet skrivs till konsolen på trace nivå, som är den _info_ spårningsnivå. Följande kod skriver till konsolen vid spårningsnivån info:

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

Eftersom _fel_ är den högsta spårningen genom den här skrivs spåret till utdata med alla spårningsnivåer som loggning är aktiverat.  


Alla `context.log` -metoder stöder samma parameter format som stöds av Node.js [util.format metoden](https://nodejs.org/api/util.html#util_util_format_format). Överväg följande kod, som skriver till konsolen genom att använda trace Standardnivå:

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

Funktioner kan du definiera spårningsnivån tröskelvärdet för att skriva till konsolen, vilket gör det enkelt att styra sätt spårningar skrivs till konsolen från dina funktioner. Om du vill ange ett tröskelvärde för alla spår som skrivs till konsolen använda den `tracing.consoleLevel` egenskapen i filen host.json. Den här inställningen gäller för alla funktioner i appen funktion. I följande exempel anger tröskelvärdet spårning för att aktivera utförlig loggning:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Värden för **consoleLevel** motsvarar namnen på de `context.log` metoder. Om du vill inaktivera alla spårningsloggning i konsolen, ange **consoleLevel** till _av_. Mer information finns i [host.json referens](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP-utlösare och bindningar

HTTP- och webhook-utlösare och HTTP-utdataformat bindningar använder förfrågan och svar objekt för att representera HTTP-meddelanden.  

### <a name="request-object"></a>Request-objektet

Den `request` objektet har följande egenskaper:

| Egenskap      | Beskrivning                                                    |
| ------------- | -------------------------------------------------------------- |
| _Brödtext_        | Ett objekt som innehåller brödtexten i begäran.               |
| _Rubriker_     | Ett objekt som innehåller huvuden för begäran.                   |
| _Metoden_      | HTTP-metod för begäran.                                |
| _originalUrl_ | URL för begäran.                                        |
| _Parametrar_      | Ett objekt som innehåller parametrar routning av begäran. |
| _Frågan_       | Ett objekt som innehåller Frågeparametrar.                  |
| _rawBody_     | Innehållet i meddelandet som en sträng.                           |


### <a name="response-object"></a>Objektet Response

Den `response` objektet har följande egenskaper:

| Egenskap  | Beskrivning                                               |
| --------- | --------------------------------------------------------- |
| _Brödtext_    | Ett objekt som innehåller brödtexten i svaret.         |
| _Rubriker_ | Ett objekt som innehåller svarshuvuden.             |
| _isRaw_   | Anger att formateringen ignoreras för svaret.    |
| _status_  | HTTP-statuskod i svaret.                     |

### <a name="accessing-the-request-and-response"></a>Åtkomst till förfrågan och svar 

När du arbetar med HTTP-utlösare kan du komma åt HTTP-begäran och svar objekt i något av tre sätt:

+ Från namngivna indata och utdata bindningar. På så sätt kan fungerar http-utlösare och bindningar på samma sätt som andra bindningen. I följande exempel anger response-objektet med en namngiven `response` bindning: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ Från `req` och `res` egenskaper på den `context` objekt. På så sätt kan du använda det vanliga mönstret att komma åt HTTP data från context-objektet i stället för att använda fullständigt `context.bindings.name` mönster. I följande exempel visas hur du kommer åt den `req` och `res` objekt på den `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Genom att anropa `context.done()`. En särskild typ av HTTP-bindning returnerar svaret som skickas till den `context.done()` metoden. Följande HTTP-utdatabindning definierar en `$return` utdataparameter:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    Den här bindningen utdata förväntar du kan ange svaret när du anropar `done()`enligt följande:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Version och package hantering

I följande tabell visas Node.js-version som används av varje högre version av körningsmiljön funktioner:

| Funktioner version | Node.js-version | 
|---|---|
| 1.x | 6.11.2 (låst av körningen) |
| 2.x  |> = 8.4.0 med aktuella LTS 8.9.4 rekommenderas. Ange version med hjälp av WEBSITE_NODE_DEFAULT_VERSION [appinställningen](functions-how-to-use-azure-function-app-settings.md#settings).|

Du kan se den aktuella versionen med hjälp av körningsmiljön genom att skriva ut `process.version` från någon funktion.

Följande steg kan du inkludera paket i funktionen appen: 

1. Gå till `https://<function_app_name>.scm.azurewebsites.net`.

2. Klicka på **Debug konsolen** > **CMD**.

3. Gå till `D:\home\site\wwwroot`, och dra sedan filen package.json till den **wwwroot** mappen på den övre delen av sidan.  
    Du kan även överföra filer till funktionen appen på andra sätt. Mer information finns i [hur du uppdaterar funktionen programfilerna](functions-reference.md#fileupdate). 

4. När filen package.json överförs kör den `npm install` i den **Kudu fjärrkörning konsolen**.  
    Den här åtgärden hämtar de paket som anges i package.json-filen och startar om funktionsapp.

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

Du ska definiera en `package.json` filen i roten på en funktionsapp i. Definiera filen kan alla funktioner i appen som delar samma cachelagrade paket, vilket ger bästa prestanda. Om det uppstår en versionskonflikt måste du lösa det genom att lägga till en `package.json` fil i mappen på en specifik funktion.  

## <a name="environment-variables"></a>Miljövariabler
För att få en miljövariabel eller en app som inställningsvärde kan använda `process.env`som visas i följande kodexempel:

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

När du skapar en funktionsapp som använder App Service-plan, rekommenderar vi att du väljer en enskild vCPU plan i stället för en plan med flera vCPUs. Idag funktioner körs effektivare JavaScript-funktioner på enskild vCPU virtuella datorer och med större virtuella datorer inte ger förväntade prestandaförbättringarna. Vid behov, du kan skala ut genom att lägga till flera enskild vCPU VM-instanser manuellt eller kan du aktivera automatisk skalning. Mer information finns i [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Stöd för TypeScript och CoffeeScript
Eftersom direktstöd ännu inte finns för automatisk kompilering av TypeScript eller CoffeeScript via körningen, behöver stödet hanteras utanför körning, vid tidpunkten för distribution. 

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

