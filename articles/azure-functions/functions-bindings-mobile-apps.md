---
title: Mobile Apps-bindningar för Azure Functions
description: Förstå hur du använder Azure Mobile Apps-bindningar i Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 6b5a45bfcfc02c6b313e868fb3ba037ce3ba48c9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967582"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps-bindningar för Azure Functions 

> [!NOTE]
> Azure Mobile Apps-bindningar är bara tillgängliga för Azure Functions 1.x. De stöds inte i Azure Functions 2.x.

Den här artikeln förklarar hur du arbetar med [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) bindningar i Azure Functions. Azure Functions stöder indata och utdata-bindningar för Mobile Apps.

Mobile Apps-bindningar kan du läsa och uppdatera datatabeller i mobilappar.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Mobile Apps-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet-paketet, version 1.x. Källkoden för paketet finns i den [azure-webjobs-sdk-tilläggen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Indata

Mobile Apps-indatabindning läser in en post från mobila tabellslutpunkt och skickar det till din funktion. I C# och F #-funktioner skickas ändringar som görs till posten automatiskt tillbaka till tabellen när funktionen har avslutas.

## <a name="input---example"></a>Indata - exempel

Se exempel språkspecifika:

* [C#-skript (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Indata - exempel på C#-skript

I följande exempel visas en indatabindning för Mobile Apps i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen utlöses av ett kömeddelande som har en postidentifierare. Funktionen läser den angivna posten och ändrar dess `Text` egenskapen.

Här är bindningsdata i den *function.json* fil:

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
]
}
```
Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

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

I följande exempel visas en indatabindning för Mobile Apps i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen utlöses av ett kömeddelande som har en postidentifierare. Funktionen läser den angivna posten och ändrar dess `Text` egenskapen.

Här är bindningsdata i den *function.json* fil:

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
]
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

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribut.

Information om attributegenskaper som som du kan konfigurera finns i [följande konfigurationsavsnittet](#input---configuration).

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `MobileTable` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
| **typ**|| Måste vara inställt på ”mobileTable”|
| **riktning**||Måste vara inställt på ”in”|
| **Namn**|| Namnet på indataparameter i funktionssignaturen.|
|**Tabellnamn** |**Tabellnamn**|Namnet på den mobila appen datatabell|
| **ID**| **Id** | Identifierare för posten som ska hämtas. Kan vara statisk eller baserat på utlösare som anropar funktionen. Exempel: Om du använder en kö-utlösare för din funktion sedan `"id": "{queueTrigger}"` använder strängvärdet för kömeddelandet som post-ID ska hämtas.|
|**anslutning**|**anslutning**|Namnet på en appinställning med URL: en för den mobila appen. Funktionen använder den här URL: en för att konstruera nödvändiga REST-åtgärder mot din mobilapp. Skapa en app-inställning i din funktionsapp som innehåller URL: en för den mobila appen och sedan ange namnet på appinställningen i den `connection` -egenskapen i din indatabindning. URL-Adressen liknar `http://<appname>.azurewebsites.net`.
|**ApiKey**|**ApiKey**|Namnet på en appinställning med API-nyckel för din mobilapp. Ange den API-nyckel om du [implementera en API-nyckel i mobilappen Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), eller [implementera en API-nyckel i din mobila app för .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Skapa en app-inställning i din funktionsapp som innehåller API-nyckel för att ge nyckeln, och sedan lägga till den `apiKey` -egenskapen i din indatabindning med namnet på appinställningen. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Dela inte API-nyckeln med din mobilapp-klienter. Det ska bara distribueras på ett säkert sätt till klienter på tjänstsidan, till exempel Azure Functions. Azure Functions innehåller dina anslutningsinformation och API-nycklar som appinställningar så att de inte är markerat i centrallagret för källkontroll. Detta skyddar känslig information.

## <a name="input---usage"></a>Indata - användning

I C#-funktioner, när posten med angivet ID finns det skickas till den namngivna [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parametern. När posten hittas, parametervärdet är `null`. 

I JavaScript-funktioner, posten skickades till den `context.bindings.<name>` objekt. När posten hittas, parametervärdet är `null`. 

I C# och F #-funktioner, alla ändringar du gör i indata post (indataparameter) skickas automatiskt tillbaka till tabellen när funktionen har avslutas. Du kan inte ändra en post i JavaScript-funktioner.

## <a name="output"></a>Resultat

Använd Mobile Apps-utdatabindning för att skriva en ny post i en tabell med Mobile Apps.  

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som utlöses av ett kömeddelande och skapa en post i en mobilapp-tabell.

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

### <a name="output---c-script-example"></a>Resultat – exempel på C#-skript

I följande exempel visas en Mobile Apps-utdatabindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen som utlöses av ett kömeddelande och skapar en ny post med hårdkodade värde för den `Text` egenskapen.

Här är bindningsdata i den *function.json* fil:

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
]
}
```

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Resultat – JavaScript-exempel

I följande exempel visas en Mobile Apps-utdatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen som utlöses av ett kömeddelande och skapar en ny post med hårdkodade värde för den `Text` egenskapen.

Här är bindningsdata i den *function.json* fil:

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

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribut.

Information om attributegenskaper som som du kan konfigurera finns i [utdata - konfigurationen](#output---configuration). Här är en `MobileTable` attributet exemplet i signaturen för metoden:

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

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `MobileTable` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
| **typ**|| Måste vara inställt på ”mobileTable”|
| **riktning**||Måste vara inställt på ”ut”|
| **Namn**|| Namnet på Utdataparametern i funktionssignaturen.|
|**Tabellnamn** |**Tabellnamn**|Namnet på den mobila appen datatabell|
|**anslutning**|**MobileAppUriSetting**|Namnet på en appinställning med URL: en för den mobila appen. Funktionen använder den här URL: en för att konstruera nödvändiga REST-åtgärder mot din mobilapp. Skapa en app-inställning i din funktionsapp som innehåller URL: en för den mobila appen och sedan ange namnet på appinställningen i den `connection` -egenskapen i din indatabindning. URL-Adressen liknar `http://<appname>.azurewebsites.net`.
|**ApiKey**|**ApiKeySetting**|Namnet på en appinställning med API-nyckel för din mobilapp. Ange den API-nyckel om du [implementera en API-nyckel i mobilappsserverdelen Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), eller [implementera en API-nyckel i mobilappsserverdelen .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Skapa en app-inställning i din funktionsapp som innehåller API-nyckel för att ge nyckeln, och sedan lägga till den `apiKey` -egenskapen i din indatabindning med namnet på appinställningen. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Dela inte API-nyckeln med din mobilapp-klienter. Det ska bara distribueras på ett säkert sätt till klienter på tjänstsidan, till exempel Azure Functions. Azure Functions innehåller dina anslutningsinformation och API-nycklar som appinställningar så att de inte är markerat i centrallagret för källkontroll. Detta skyddar känslig information.

## <a name="output---usage"></a>Utdata - användning

I C#-skript-funktioner, använder en namngiven output-parameter av typen `out object` till utdata-post. I C#-klassbibliotek, den `MobileTable` attributet kan användas med någon av följande typer:

* `ICollector<T>` eller `IAsyncCollector<T>`, där `T` är antingen `JObject` eller någon typ med en `public string Id` egenskapen.
* `out JObject`
* `out T` eller `out T[]`, där `T` är valfri typ med en `public string Id` egenskapen.

I Node.js-funktion, använda `context.bindings.<name>` till utdata-post.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
