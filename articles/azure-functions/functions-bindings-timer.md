---
title: "Azure Functions timer som utlösare | Microsoft Docs"
description: "Förstå hur du använder timer-utlösare i Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
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
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 12beb090a95a31c7e83ae03a920016bdfbf474e3
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="azure-functions-timer-trigger"></a>Azure Functions timer som utlösare

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln förklarar hur du konfigurerar och koden timer utlösare i Azure Functions. Azure Functions har en timer utlösaren bindning som låter dig köra din funktionskod baserat på ett definierat schema. 

Timer-utlösare stöder flera instanser skalbar. En instans av en viss timerfunktion körs i alla instanser.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>Timerutlösare
Timer-utlösaren till en funktion använder följande JSON-objekt i den `bindings` matris med function.json:

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

Värdet för `schedule` är en [CRON-uttryck](http://en.wikipedia.org/wiki/Cron#CRON_expression) som innehåller dessa sex fält: 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>Många av cron-uttryck som du hittar online utelämna den `{second}` fältet. Om du kopierar från en av dem kan du behöva justera extra `{second}` fältet. Specifika exempel finns [schemalägga exempel](#examples) nedan.

Standardtidszon används med CRON-uttryck är Coordinated Universal Time (UTC). Om du vill att din CRON-uttryck baserat på en annan tidszon, skapa en ny appinställning för funktionen appen med namnet `WEBSITE_TIME_ZONE`. Ange värdet till namnet på tidszonen som visas i den [Microsoft tidszon Index](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Till exempel *Eastern, normaltid* är UTC-05:00. Har din timer Utlös brand på 10:00 AM GMT varje dag, använder du följande CRON-uttryck i för UTC-tid:

```json
"schedule": "0 0 15 * * *",
``` 

Alternativt kan du lägga till en ny appinställning för funktionen appen med namnet `WEBSITE_TIME_ZONE` och ange värdet **Eastern, normaltid**.  Följande CRON-uttryck kan sedan användas för 10:00 AM GMT: 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>Schema-exempel
Här följer några exempel på CRON-uttryck som du kan använda för den `schedule` egenskapen. 

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

<a name="usage"></a>

## <a name="trigger-usage"></a>Utlösaren användning
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

<a name="sample"></a>

## <a name="trigger-sample"></a>Utlösaren exempel
Anta att du har följande tidsinställda utlösaren den `bindings` matris med function.json:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Se exemplet språkspecifika som läser timer-objekt för att se om det är försenat.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Utlösaren exemplet i C# #
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

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Utlösaren exemplet i F # #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Utlösaren exemplet i Node.js
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

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

