---
title: Timer som utlösare för Azure Functions
description: Förstå hur du använder timer-utlösare i Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: a4895c0c58d1cdb0430b7418ba24dd85157ecdd3
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308167"
---
# <a name="timer-trigger-for-azure-functions"></a>Timer som utlösare för Azure Functions 

Den här artikeln förklarar hur du arbetar med timer utlösare i Azure Functions. En timer som utlösare kan du köra en funktion på ett schema. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - fungerar 1.x

Timer-utlösare har angetts i den [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet version 2.x. Källkoden för paketet är i den [azure-webjobs-sdk-tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Timer-utlösare har angetts i den [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet version 3.x. Källkoden för paketet är i den [azure-webjobs-sdk-tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub-lagringsplatsen.

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
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
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

Attributets konstruktorn har ett CRON-uttryck eller en `TimeSpan`. Du kan använda `TimeSpan` endast om funktionsapp körs på en App Service-plan. I följande exempel visas ett CRON-uttryck:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `TimerTrigger` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till ”timerTrigger”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till ”i”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar timer-objekt i funktionskoden. | 
|**schedule**|**ScheduleExpression**|En [CRON-uttryck](#cron-expressions) eller en [TimeSpan](#timespan) värde. En `TimeSpan` kan endast användas för en funktionsapp som körs på en App Service-Plan. Du kan placera schema-uttrycket i en appinställning och ange egenskapen till appen inställningsnamn kapslas in i **%** tecken, som i följande exempel: ”% ScheduleAppSetting %”. |
|**RunOnStartup**|**RunOnStartup**|Om `true`, funktionen anropas när körningen startar. Exempelvis startar körningen när appen funktionen aktiveras efter att inaktiveras på grund av inaktivitet. När funktionen appen startas om på grund av funktionen ändringar och när appen funktionen skalas ut. Så **runOnStartup** bör sällan om någonsin anges till `true`, vilket gör koden köra vid hög oväntade tidpunkter.|
|**UseMonitor**|**UseMonitor**|Ange till `true` eller `false` att indikera om schemat bör övervakas. Övervakning av schemat kvarstår schema förekomster till stöd för schemat underhålls på rätt sätt när du startar om funktionen app-instanser. Om den inte har angetts explicit är standardvärdet `true` för scheman som har ett intervall som är större än 1 minut. För scheman som utlöser mer än en gång per minut som standard är `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

När en timer utlösaren funktionen anropas på [timer-objekt](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) skickades till funktionen. Följande JSON är ett exempel representation av timer-objektet. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

Den `IsPastDue` egenskapen är `true` när den aktuella funktionsanrop är senare än förväntat. Till exempel kan en funktion app omstart orsaka ett anrop till missas.

## <a name="cron-expressions"></a>CRON-uttryck 

Ett CRON-uttryck för Azure Functions timer-utlösare innehåller sex fält: 

`{second} {minute} {hour} {day} {month} {day-of-week}`

Varje fält kan ha något av följande typer av värden:

|Typ  |Exempel  |När den utlöses  |
|---------|---------|---------|
|Ett specifikt värde |<nobr>”0-5 *** *”</nobr>|vid hh:05:00 där hh är varje Timma (en gång i timmen)|
|Alla värden (`*`)|<nobr>”0 * 5 ** *”</nobr>|vid 5:mm: 00 varje dag, mm är där varje minuten i timmen (60 gånger per dag)|
|Ett intervall (`-` operatorn)|<nobr>”5-7 **** *”</nobr>|på hh:mm:05, hh:mm:06 och hh:mm:07 där HH är minuten i timmen (3 gånger minut)|  
|En uppsättning värden (`,` operatorn)|<nobr>”5,8,10 **** *”</nobr>|på hh:mm:05, hh:mm:08 och hh:mm:10 där HH är minuten i timmen (3 gånger minut)|
|Ett intervallvärde (`/` operatorn)|<nobr>”0 * / 5 *** *”</nobr>|hh:05:00, hh:10:00, hh:15:00 och så vidare till hh:55:00 där hh är varje Timma (12 gånger i timmen)|

Du kan använda tre bokstäver förkortningar i stället för numeriska värden om du vill ange månader eller dagar. Till exempel använda Jan för januari eller Sun för söndag.

### <a name="cron-examples"></a>CRON-exempel

Här följer några exempel på CRON-uttryck som du kan använda för utlösaren i Azure Functions timer.

|Exempel|När den utlöses  |
|---------|---------|
|”0 * / 5 *** *”|en gång var femte minut|
|”0 0 *** *”|en gång längst upp i varje timme|
|”0 0 * / 2 ** *”|en gång varannan timme|
|”0 0-9-17 ** *”|en gång i timmen från 9: 00 och 17: 00|
|”0 30 9 ** *”|på 9:30:00 varje dag|
|”0 30 9 ** 1-5”|på 9:30:00 varje vardag|

>[!NOTE]   
>Du kan hitta CRON exempel på uttryck online, men många av dem utelämna den `{second}` fältet. Om du kopierar från en av dem, lägger du till den saknade `{second}` fältet. Vanligtvis vill du noll i fältet, inte en asterisk.

### <a name="cron-time-zones"></a>CRON tidszoner

Siffrorna i ett CRON-uttryck referera till ett datum och klockslag, inte ett tidsintervall. Till exempel en 5 i den `hour` fältet refererar till 5:00:00, inte var 5: e timme.

Standardtidszon används med CRON-uttryck är Coordinated Universal Time (UTC). Om du vill att din CRON-uttryck baserat på en annan tidszon, skapa en appinställning för appen funktion med namnet `WEBSITE_TIME_ZONE`. Ange värdet till namnet på tidszonen som visas i den [Microsoft tidszon Index](https://technet.microsoft.com/library/cc749073). 

Till exempel *Eastern, normaltid* är UTC-05:00. Har din timer Utlös brand på 10:00 AM GMT varje dag, använder du följande CRON-uttryck i för UTC-tid:

```json
"schedule": "0 0 15 * * *",
``` 

Eller skapa en appinställning för appen funktion med namnet `WEBSITE_TIME_ZONE` och ange värdet **Eastern, normaltid**.  Sedan använder du följande CRON-uttryck: 

```json
"schedule": "0 0 10 * * *",
``` 

## <a name="timespan"></a>TimeSpan

 En `TimeSpan` kan endast användas för en funktionsapp som körs på en App Service-Plan.

Till skillnad från ett CRON-uttryck, en `TimeSpan` värdet anger tidsintervallet mellan varje funktionsanrop. När en funktion är klar när du har kört längre än det angivna intervallet anropar timern direkt funktionen igen.

Uttryckt i form av en sträng i `TimeSpan` format är `hh:mm:ss` när `hh` är mindre än 24. När de två första siffrorna är 24 eller större, formatet är `dd:hh:mm`. Här följer några exempel:

|Exempel |När den utlöses  |
|---------|---------|
|”01: 00:00” | varje timma        |
|”00: 01:00”|Varje minut         |
|"24:00:00" | var 24: e dag        |

## <a name="scale-out"></a>Skalbarhet

Om en funktionsapp skalas ut till flera instanser, körs bara en instans av en funktion som utlöses av timer över alla instanser.

## <a name="function-apps-sharing-storage"></a>Funktionen appar som delar lagring

Om du delar ett lagringskonto över flera funktionen appar, se till att varje funktionsapp har en annan `id` i *host.json*. Du kan hoppa över den `id` egenskapen eller manuellt ange varje funktionsapp `id` till ett annat värde. Timer-utlösaren använder lagring Lås så att endast en timer-instans när en funktionsapp skalas ut till flera instanser. Om två funktionen appar med samma `id` och varje använder en timer som utlösare, körs bara en timer.

## <a name="retry-behavior"></a>Omförsök

Till skillnad från kön-utlösaren igen inte timer utlösaren efter en funktion misslyckas. När en funktion inte är inte den anropade igen förrän nästa gång enligt schemat.

## <a name="troubleshooting"></a>Felsökning

Information om vad du gör om timern utlösaren inte fungerar som förväntat finns [Investigating och rapportera problem med timer utlöses funktioner som utlöser inte](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en Snabbstart som använder en timer som utlösare](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
