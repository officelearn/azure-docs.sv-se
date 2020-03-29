---
title: Bindningar av mobilappar för Azure-funktioner
description: Förstå hur du använder Azure Mobile Apps-bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 952a94797e01a3931fdd151461250af0c2590c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76120549"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Bindningar av mobilappar för Azure-funktioner 

> [!NOTE]
> Azure Mobile Apps-bindningar är endast tillgängliga för Azure Functions 1.x. De stöds inte i Azure Functions 2.x och högre.

I den här artikeln beskrivs hur du arbetar med [Azure Mobile Apps-bindningar](../app-service-mobile/app-service-mobile-value-prop.md) i Azure Functions. Azure Functions stöder in- och utdatabindningar för mobilappar.

Med bindningarna för mobilappar kan du läsa och uppdatera datatabeller i mobilappar.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - Funktioner 1.x

Bindningar för mobilappar finns i [Paketet Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet, version 1.x. Källkoden för paketet finns i [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) GitHub-databasen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Indata

Indatabindningen för mobilappar läser in en post från en slutpunkt för mobila bord och skickar den till din funktion. I funktionerna C# och F# skickas alla ändringar som görs i posten automatiskt tillbaka till tabellen när funktionen avslutas.

## <a name="input---example"></a>Ingång - exempel

Se det språkspecifika exemplet:

* [C#-skript (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Input - C# skriptexempel

I följande exempel visas en indatabindning för mobilappar i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen utlöses av ett kömeddelande som har en postidentifierare. Funktionen läser den angivna posten och `Text` ändrar egenskapen.

Här är bindningsdata i *filen function.json:*

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
[Konfigurationsavsnittet](#input---configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

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

### <a name="input---javascript"></a>Ingång - JavaScript

I följande exempel visas en indatabindning för mobilappar i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen utlöses av ett kömeddelande som har en postidentifierare. Funktionen läser den angivna posten och `Text` ändrar egenskapen.

Här är bindningsdata i *filen function.json:*

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
[Konfigurationsavsnittet](#input---configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Indata - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [attributet MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Information om attributegenskaper som du kan konfigurera finns [i följande konfigurationsavsnitt](#input---configuration).

## <a name="input---configuration"></a>Ingång - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `MobileTable` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
| **Typ**| Saknas | Måste ställas in på "mobileTable"|
| **riktning**| Saknas |Måste ställas in på "i"|
| **Namn**| Saknas | Namnet på indataparametern i funktionssignaturen.|
|**Tablename** |**Tablename**|Namn på mobilappens datatabell|
| **Id**| **Id** | Identifieraren för den post som ska hämtas. Kan vara statisk eller baserat på utlösaren som anropar funktionen. Om du till exempel använder en köutlösare för din funktion använder du `"id": "{queueTrigger}"` strängvärdet för kömeddelandet som post-ID för att hämta.|
|**Anslutning**|**Anslutning**|Namnet på en appinställning som har mobilappens WEBBADRESS. Funktionen använder den här URL:en för att konstruera de REST-åtgärder som krävs mot din mobilapp. Skapa en appinställning i funktionsappen som innehåller mobilappens URL och ange `connection` sedan namnet på appinställningen i egenskapen i indatabindningen. Webbadressen ser `http://<appname>.azurewebsites.net`ut som .
|**apiKey (apiKey)**|**ApiKey (ApiKey)**|Namnet på en appinställning som har appens API-nyckel. Ange API-nyckeln om du [implementerar en API-nyckel i mobilappen Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)eller [implementerar en API-nyckel i .NET-mobilappen](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Om du vill ange nyckeln skapar du en appinställning i funktionsappen som innehåller API-nyckeln och lägger sedan till egenskapen `apiKey` i indatabindningen med namnet på appinställningen. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Dela inte API-nyckeln med dina mobilappklienter. Det bör endast distribueras säkert till tjänst-side klienter, som Azure Functions. Azure Functions lagrar din anslutningsinformation och API-nycklar som appinställningar så att de inte checkas in i källkontrolldatabasen. Detta skyddar din känsliga information.

## <a name="input---usage"></a>Inmatning - användning

När posten med det angivna ID:t hittas i C#-funktioner skickas den till den namngivna [JObject-parametern.](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) När posten inte hittas är `null`parametervärdet . 

I JavaScript-funktioner skickas posten `context.bindings.<name>` till objektet. När posten inte hittas är `null`parametervärdet . 

I funktionerna C# och F# skickas alla ändringar du gör i indataposten (indataparameter) automatiskt tillbaka till tabellen när funktionen avslutas. Du kan inte ändra en post i JavaScript-funktioner.

## <a name="output"></a>Resultat

Använd utdatabindningen för mobilappar för att skriva en ny post till en tabell för mobilappar.  

## <a name="output---example"></a>Utdata - exempel

Se det språkspecifika exemplet:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [Javascript](#output---javascript-example)

### <a name="output---c-example"></a>Utdata - C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som utlöses av ett kömeddelande och skapar en post i en tabell över mobilappar.

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

### <a name="output---c-script-example"></a>Utdata - C#-skriptexempel

I följande exempel visas en utdatabindning för mobilappar i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen utlöses av ett kömeddelande och skapar en ny post med `Text` hårt kodat värde för egenskapen.

Här är bindningsdata i *filen function.json:*

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

[Konfigurationsavsnittet](#output---configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Utdata - JavaScript-exempel

I följande exempel visas en utdatabindning för mobilappar i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen utlöses av ett kömeddelande och skapar en ny post med `Text` hårt kodat värde för egenskapen.

Här är bindningsdata i *filen function.json:*

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

[Konfigurationsavsnittet](#output---configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Utdata - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [attributet MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Information om attributegenskaper som du kan konfigurera finns i [Utdata - konfiguration](#output---configuration). Här är `MobileTable` ett attributexempel i en metodsignatur:

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

Ett fullständigt exempel finns i [Exempel på Utdata - C#.](#output---c-example)

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `MobileTable` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
| **Typ**| Saknas | Måste ställas in på "mobileTable"|
| **riktning**| Saknas |Måste ställas in på "ut"|
| **Namn**| Saknas | Namn på utdataparameter i funktionssignaturen.|
|**Tablename** |**Tablename**|Namn på mobilappens datatabell|
|**Anslutning**|**MobileAppUriSetting**|Namnet på en appinställning som har mobilappens WEBBADRESS. Funktionen använder den här URL:en för att konstruera de REST-åtgärder som krävs mot din mobilapp. Skapa en appinställning i funktionsappen som innehåller mobilappens URL och ange `connection` sedan namnet på appinställningen i egenskapen i indatabindningen. Webbadressen ser `http://<appname>.azurewebsites.net`ut som .
|**apiKey (apiKey)**|**ApiKeySetting**|Namnet på en appinställning som har appens API-nyckel. Ange API-nyckeln om du [implementerar en API-nyckel i serverdelen Node.js-mobilappen](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)eller [implementerar en API-nyckel i serverdelen för .NET-mobilappen](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Om du vill ange nyckeln skapar du en appinställning i funktionsappen som innehåller API-nyckeln och lägger sedan till egenskapen `apiKey` i indatabindningen med namnet på appinställningen. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Dela inte API-nyckeln med dina mobilappklienter. Det bör endast distribueras säkert till tjänst-side klienter, som Azure Functions. Azure Functions lagrar din anslutningsinformation och API-nycklar som appinställningar så att de inte checkas in i källkontrolldatabasen. Detta skyddar din känsliga information.

## <a name="output---usage"></a>Utdata - användning

I C#-skriptfunktioner använder du en `out object` namngiven utdataparameter av typen för att komma åt utdataposten. I klassbibliotek för C#kan `MobileTable` attributet användas med någon av följande typer:

* `ICollector<T>`eller `IAsyncCollector<T>`, `T` var `JObject` är antingen `public string Id` eller någon typ med en egenskap.
* `out JObject`
* `out T`eller `out T[]`, `T` var är `public string Id` någon typ med en egenskap.

I Node.js-funktioner `context.bindings.<name>` använder du för att komma åt utdataposten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)
