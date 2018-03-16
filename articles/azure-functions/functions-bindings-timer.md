---
title: "Timer som utlösare för Azure Functions"
description: "Förstå hur du använder timer-utlösare i Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: 
ms.openlocfilehash: bd1a2643d9faf65d664c786169c38f01767fb7e5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Timer som utlösare för Azure Functions 

Den här artikeln förklarar hur du arbetar med timer utlösare i Azure Functions. En timer som utlösare kan du köra en funktion på ett schema. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Paket

Timer-utlösare har angetts i den [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet. Källkoden för paketet är i den [azure-webjobs-sdk-tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exempel

Finns i det språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som körs var femte minut:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Exempel på C#-skript

I följande exempel visas en timer som utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionsanrop är på grund av en missade schema förekomst.

Här är de bindande data den *function.json* fil:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är skriptkod C#:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>F #-exempel

I följande exempel visas en timer som utlösare bindning i en *function.json* fil och en [F # skriptfunktion](functions-reference-fsharp.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionsanrop är på grund av en missade schema förekomst.

Här är de bindande data den *function.json* fil:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är skriptkod F #:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript-exempel

I följande exempel visas en timer som utlösare bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionsanrop är på grund av en missade schema förekomst.

Här är de bindande data den *function.json* fil:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är JavaScript-skriptkod:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>Attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Attributets konstruktorn har ett CRON-uttryck som visas i följande exempel:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

Du kan ange en `TimeSpan` i stället för ett CRON-uttryck om funktionen appen körs i en apptjänstplan (inte en plan för förbrukning).

En komplett exempel finns [C#-exempel](#c-example).

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `TimerTrigger` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste anges till ”timerTrigger”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**Riktning** | Saknas | Måste anges till ”i”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar timer-objekt i funktionskoden. | 
|**schedule**|**ScheduleExpression**|Du kan definiera scheman med ett CRON-uttryck på förbrukning-plan. Om du använder en App Service-Plan kan du också använda en `TimeSpan` sträng. I följande avsnitt beskrivs CRON-uttryck. Du kan placera schema-uttrycket i en appinställning och ange egenskapen till ett värde som kapslas in i  **%**  tecken, som i följande exempel: ”% NameOfAppSettingWithCRONExpression %”. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>CRON-format 

En [CRON-uttryck](http://en.wikipedia.org/wiki/Cron#CRON_expression) för Azure Functions-timer-utlösare innehåller fälten sex: 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>Många av CRON-uttryck som du hittar online utelämna den `{second}` fältet. Om du kopierar från en av dem, lägger du till den saknade `{second}` fältet.

### <a name="cron-time-zones"></a>CRON tidszoner

Standardtidszon används med CRON-uttryck är Coordinated Universal Time (UTC). Om du vill att din CRON-uttryck baserat på en annan tidszon, skapa en ny appinställning för funktionen appen med namnet `WEBSITE_TIME_ZONE`. Ange värdet till namnet på tidszonen som visas i den [Microsoft tidszon Index](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Till exempel *Eastern, normaltid* är UTC-05:00. Har din timer Utlös brand på 10:00 AM GMT varje dag, använder du följande CRON-uttryck i för UTC-tid:

```json
"schedule": "0 0 15 * * *",
``` 

Alternativt kan du lägga till en ny appinställning för funktionen appen med namnet `WEBSITE_TIME_ZONE` och ange värdet **Eastern, normaltid**.  Följande CRON-uttryck kan sedan användas för 10:00 AM GMT: 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>CRON-exempel

Här följer några exempel på CRON-uttryck som du kan använda för utlösaren i Azure Functions timer. 

Att utlösa en gång var femte minut:

```json
"schedule": "0 */5 * * * *"
```

Att utlösa en gång längst upp i varje timme:

```json
"schedule": "0 0 * * * *",
```

Att utlösa en gång varannan timme:

```json
"schedule": "0 0 */2 * * *",
```

Att utlösa en gång i timmen från 9: 00 och 17: 00:

```json
"schedule": "0 0 9-17 * * *",
```

Att utlösa på 9:30:00 varje dag:

```json
"schedule": "0 30 9 * * *",
```

Att utlösa på 9:30:00 varje vardag:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>Användning

När en timer utlösaren funktionen anropas på [timer-objekt](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) skickades till funktionen. Följande JSON är ett exempel representation av timer-objektet. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>Skalbarhet

Timer-utlösare stöder flera instanser skalbar. En instans av en viss timerfunktion körs i alla instanser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en Snabbstart som använder en timer som utlösare](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
