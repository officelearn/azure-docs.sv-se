---
title: Azure Functions Mobile Apps bindningar | Microsoft Docs
description: "Förstå hur du använder Azure Mobile Apps bindningar i Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
ms.openlocfilehash: d2c0e4e233761584bad2df05a8e702e4fc77e84f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-mobile-apps-bindings"></a>Azure Functions Mobile Apps bindningar
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln förklarar hur du konfigurerar och code [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) bindningar i Azure Functions. Azure Functions stöder indata och utdata bindningar för Mobile Apps.

Mobile Apps indata och utdata-bindningar kan du [läsa från och skriva till datatabeller](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) i din mobila app.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Mobile Apps indatabindning
Mobile Apps-indatabindning läser en post från en mobil tabell slutpunkt och skickar det till din funktion. I C# och F # funktioner skickas ändringar som görs till posten automatiskt tillbaka till tabellen när funktionen avslutas korrekt.

Mobile Apps indata för en funktion använder följande JSON-objekt i den `bindings` matris med function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

Observera följande:

* `id`kan vara statisk eller det kan vara baserad på utlösaren som anropar funktionen. Om du använder till exempel en [kö utlösaren]() för din funktion sedan `"id": "{queueTrigger}"` använder strängvärdet för kön meddelandet som post-ID för att hämta.
* `connection`ska innehålla namnet på en appinställning i funktionen appen, som i sin tur innehåller URL-Adressen till din mobila app. URL: en använder funktionen för att skapa nödvändiga REST-åtgärder mot din mobila app. Du [skapa en appinställningen i appen funktionen]() som innehåller din mobila app-URL (som ser ut så `http://<appname>.azurewebsites.net`), ange namnet på appinställningen i den `connection` egenskap i den inkommande bindningen. 
* Du måste ange `apiKey` om du [implementera en API-nyckel i mobilappsserverdelen Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), eller [implementera en API-nyckel i mobilappsserverdelen .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Gör du [skapa en appinställningen i appen funktionen]() som innehåller API-nyckeln, Lägg sedan till den `apiKey` egenskap i den inkommande bindningen med namnet för appinställningen. 
  
  > [!IMPORTANT]
  > Den här API-nyckeln måste inte delas med din mobila app-klienter. Det bör bara distribueras på ett säkert sätt till tjänsten på klientsidan klienter, till exempel Azure Functions. 
  > 
  > [!NOTE]
  > Azure Functions lagrar dina anslutningsinformationen och API-nycklar som app-inställningar så att de inte kontrolleras i din källkontroll. Detta skyddar känslig information.
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>Inkommande användning
Det här avsnittet visar hur du använder Mobile Apps-indatabindning i funktionskoden. 

När posten med den angivna tabellen och post-ID hittas skickas till den namngivna [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parameter (eller i Node.js, den skickas till den `context.bindings.<name>` objekt). Om posten inte hittas, parametern är `null`. 

I C# och F # funktioner, alla ändringar du gör inkommande post (Indataparametern) skickas automatiskt tillbaka till tabellen Mobile Apps när funktionen avslutas korrekt. I Node.js-funktion, använda `context.bindings.<name>` att komma åt Indataposten. Du kan inte ändra en post i Node.js.

<a name="inputsample"></a>

## <a name="input-sample"></a>Indata-exempel
Anta att du har följande function.json, som hämtar Mobilapp tabell post med ID: t för utlösaren kömeddelande:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```

Finns det språkspecifika exempel som använder Indataposten från bindningen. C# och F #-exempel också ändra postens `text` egenskapen.

* [C#](#inputcsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Inkommande exemplet i C# #

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Inkommande exemplet i Node.js

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Mobile Apps-utdatabindning
Använda Mobile Apps utdata bindning för att skriva en ny post till en slutpunkt för Mobile Apps-tabellen.  

Mobilappar som utdata för en funktion använder följande JSON-objekt i den `bindings` matris med function.json:

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

Observera följande:

* `connection`ska innehålla namnet på en appinställning i funktionen appen, som i sin tur innehåller URL-Adressen till din mobila app. URL: en använder funktionen för att skapa nödvändiga REST-åtgärder mot din mobila app. Du [skapa en appinställningen i appen funktionen]() som innehåller din mobila app-URL (som ser ut så `http://<appname>.azurewebsites.net`), ange namnet på appinställningen i den `connection` egenskap i den inkommande bindningen. 
* Du måste ange `apiKey` om du [implementera en API-nyckel i mobilappsserverdelen Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), eller [implementera en API-nyckel i mobilappsserverdelen .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Gör du [skapa en appinställningen i appen funktionen]() som innehåller API-nyckeln, Lägg sedan till den `apiKey` egenskap i den inkommande bindningen med namnet för appinställningen. 
  
  > [!IMPORTANT]
  > Den här API-nyckeln måste inte delas med din mobila app-klienter. Det bör bara distribueras på ett säkert sätt till tjänsten på klientsidan klienter, till exempel Azure Functions. 
  > 
  > [!NOTE]
  > Azure Functions lagrar dina anslutningsinformationen och API-nycklar som app-inställningar så att de inte kontrolleras i din källkontroll. Detta skyddar känslig information.
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>Användning av utdata
Det här avsnittet visar hur du använder din Mobile Apps utdata bindningen i din funktionskoden. 

I C#-funktioner, använder en namngiven output-parameter av typen `out object` åtkomst till utdata-posten. I Node.js-funktion, använda `context.bindings.<name>` till utdata-posten.

<a name="outputsample"></a>

## <a name="output-sample"></a>Exempel på utdata
Anta att du har följande function.json, som definierar en kö-utlösare och en Mobile Apps-utdata:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

Se exemplet språkspecifika som skapar en post i tabellen Mobile Apps slutpunkten med innehållet i kön meddelandet.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Utdata exemplet i C# #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Utdata exemplet i Node.js

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

