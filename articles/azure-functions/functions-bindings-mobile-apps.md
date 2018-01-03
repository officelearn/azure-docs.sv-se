---
title: "Mobile Apps bindningar för Azure Functions"
description: "Förstå hur du använder Azure Mobile Apps bindningar i Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: a1e4f15747031ba75ba5ae589557750919a71853
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps bindningar för Azure Functions 

Den här artikeln förklarar hur du arbetar med [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) bindningar i Azure Functions. Azure Functions stöder indata och utdata bindningar för Mobile Apps.

Mobile Apps-bindningar kan du läsa och uppdatera datatabeller i mobila appar.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="input"></a>Indata

Mobile Apps-indatabindning läser en post från en mobil tabell slutpunkt och skickar det till din funktion. I C# och F # funktioner skickas ändringar som görs till posten automatiskt tillbaka till tabellen när funktionen avslutas korrekt.

## <a name="input---example"></a>Indata - exempel

Finns i det språkspecifika:

* [C#-skript (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Indata - exempel på C#-skript

I följande exempel visas en inkommande Mobile Apps-bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen utlöses av ett kömeddelande som har en postidentifierare. Funktionen läser den angivna posten och ändrar dess `Text` egenskapen.

Här är de bindande data den *function.json* fil:

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
Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

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

### <a name="input---javascript"></a>Indata - JavaScript

I följande exempel visas en inkommande Mobile Apps-bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen utlöses av ett kömeddelande som har en postidentifierare. Funktionen läser den angivna posten och ändrar dess `Text` egenskapen.

Här är de bindande data den *function.json* fil:

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
Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Indata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribut som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps).

Information om egenskaper för attribut som du kan konfigurera finns [konfigurationsavsnittet följande](#input---configuration).

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `MobileTable` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
| **typ**|| Måste anges till ”mobileTable”|
| **riktning**||Måste anges till ”i”|
| **Namn**|| Namnet på Indataparametern i funktionssignaturen.|
|**tableName** |**TableName**|Namnet på den mobila appen datatabell|
| **ID**| **ID** | Identifierare för att hämta posten. Kan vara statiska eller baserat på utlösaren som anropar funktionen. Om du sedan använda en kö utlösare för din funktion, till exempel `"id": "{queueTrigger}"` använder strängvärdet för kön meddelandet som post-ID för att hämta.|
|**anslutning**|**Anslutning**|Namnet på en app som har mobila app-URL. URL: en använder funktionen för att skapa nödvändiga REST-åtgärder mot din mobila app. Skapa en appinställning i funktionen appen som innehåller URL för den mobila appen och sedan ange namnet på appinställningen i den `connection` egenskap i den inkommande bindningen. URL-Adressen ser ut som `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|Namnet på en appinställning med din mobila app API-nyckel. Ange API nyckel om du [implementera en API-nyckel i din mobila app Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), eller [implementera en API-nyckel i din mobila app .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Skapa en appinställning i funktionen appen som innehåller API-nyckeln för att ge nyckeln, och lägger till den `apiKey` egenskap i den inkommande bindningen med namnet för appinställningen. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> API-nyckeln inte dela med dina klienter för mobila appar. Det bör bara distribueras på ett säkert sätt till tjänsten på klientsidan klienter, till exempel Azure Functions. Azure Functions lagrar dina anslutningsinformationen och API-nycklar som app-inställningar så att de inte kontrolleras i din källkontroll. Detta skyddar känslig information.

## <a name="input---usage"></a>Indata - användning

I C#-funktioner, när det finns poster med angivet ID, den skickas till den namngivna [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parameter. Om posten inte hittas, är parametervärdet `null`. 

I JavaScript-funktioner, posten har överförts till den `context.bindings.<name>` objekt. Om posten inte hittas, är parametervärdet `null`. 

I C# och F # funktioner, alla ändringar du gör inkommande post (Indataparametern) skickas automatiskt tillbaka till tabellen när funktionen avslutas korrekt. Du kan inte ändra en post i JavaScript-funktioner.

## <a name="output"></a>Resultat

Använda Mobile Apps utdata bindning för att skriva en ny post i en tabell för Mobile Apps.  

## <a name="output---example"></a>Output - exempel

Finns i det språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Utdata - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som utlöses av ett meddelande i kön och skapar en post i en tabell för mobila appar.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Utdata - exempel på C#-skript

I följande exempel visas en Mobile Apps bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen utlöses av ett meddelande i kön och skapar en ny post med ett hårdkodat värde för den `Text` egenskapen.

Här är de bindande data den *function.json* fil:

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

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Utdata - JavaScript-exempel

I följande exempel visas en Mobile Apps bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen utlöses av ett meddelande i kön och skapar en ny post med ett hårdkodat värde för den `Text` egenskapen.

Här är de bindande data den *function.json* fil:

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

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribut som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps).

Information om egenskaper för attribut som du kan konfigurera finns [utdata - konfiguration](#output---configuration). Här är en `MobileTable` attributet exempel i en signatur:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

En komplett exempel finns [utdata - C#-exempel](#output---c-example).

## <a name="output---configuration"></a>Output - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `MobileTable` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
| **typ**|| Måste anges till ”mobileTable”|
| **riktning**||Måste anges till ”out”|
| **Namn**|| Namn på Utdataparametern i funktionssignatur.|
|**tableName** |**TableName**|Namnet på den mobila appen datatabell|
|**anslutning**|**MobileAppUriSetting**|Namnet på en app som har mobila app-URL. URL: en använder funktionen för att skapa nödvändiga REST-åtgärder mot din mobila app. Skapa en appinställning i funktionen appen som innehåller URL för den mobila appen och sedan ange namnet på appinställningen i den `connection` egenskap i den inkommande bindningen. URL-Adressen ser ut som `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|Namnet på en appinställning med din mobila app API-nyckel. Ange API nyckel om du [implementera en API-nyckel i mobilappsserverdelen Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), eller [implementera en API-nyckel i mobilappsserverdelen .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Skapa en appinställning i funktionen appen som innehåller API-nyckeln för att ge nyckeln, och lägger till den `apiKey` egenskap i den inkommande bindningen med namnet för appinställningen. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> API-nyckeln inte dela med dina klienter för mobila appar. Det bör bara distribueras på ett säkert sätt till tjänsten på klientsidan klienter, till exempel Azure Functions. Azure Functions lagrar dina anslutningsinformationen och API-nycklar som app-inställningar så att de inte kontrolleras i din källkontroll. Detta skyddar känslig information.

## <a name="output---usage"></a>Utdata - användning

I C# skript funktion, använder en namngiven output-parameter av typen `out object` åtkomst till utdata-posten. I C#-klassbibliotek, den `MobileTable` attributet kan användas med någon av följande typer:

* `ICollector<T>`eller `IAsyncCollector<T>`, där `T` är antingen `JObject` eller någon typ med en `public string Id` egenskap.
* `out JObject`
* `out T`eller `out T[]`, där `T` är valfri typ med en `public string Id` egenskap.

I Node.js-funktion, använda `context.bindings.<name>` till utdata-posten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
