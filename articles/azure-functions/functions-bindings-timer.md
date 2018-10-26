---
title: Timerutlösare för Azure Functions
description: Förstå hur du använder timerutlösare i Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/08/2018
ms.author: glenga
ms.custom: ''
ms.openlocfilehash: d81794f3fe91d1c0e922b1c0e930e308ee566a44
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087465"
---
# <a name="timer-trigger-for-azure-functions"></a>Timerutlösare för Azure Functions 

Den här artikeln förklarar hur du arbetar med timerutlösare i Azure Functions. En timer som utlösare kan du köra en funktion på ett schema. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Timerutlösare finns i den [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet, version 2.x. Källkoden för paketet finns i den [azure-webjobs-sdk-tilläggen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Timerutlösare finns i den [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet, version 3.x. Källkoden för paketet finns i den [azure-webjobs-sdk-tilläggen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="c-example"></a>C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som körs var femte minut:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if(myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Exempel på C#-skript

I följande exempel visas en timer som utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionsanrop är på grund av en förekomst av missade schema.

Här är bindningsdata i den *function.json* fil:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är C#-skriptkoden:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if(myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>F #-exempel

I följande exempel visas en timer som utlösare bindning i en *function.json* fil och en [F #-skriptfunktion](functions-reference-fsharp.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionsanrop är på grund av en förekomst av missade schema.

Här är bindningsdata i den *function.json* fil:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är F #-skriptkoden:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript-exempel

I följande exempel visas en timer som utlösare bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionsanrop är på grund av en förekomst av missade schema.

Här är bindningsdata i den *function.json* fil:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är JavaScript-kod:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

### <a name="java-example"></a>Java-exemplet

Följande exempel funktion utlöser och kör var femte minut. Den `@TimerTrigger` anteckningen i funktionen definierar schemat med samma strängformat som [CRON-uttryck](http://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

## <a name="attributes"></a>Attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Attributets konstruktorn tar ett CRON-uttryck eller en `TimeSpan`. Du kan använda `TimeSpan` endast om appen körs på en App Service plan. I följande exempel visas ett CRON-uttryck:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `TimerTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till ”timerTrigger”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till ”in”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar timer-objekt i funktionskoden. | 
|**schedule**|**ScheduleExpression**|En [CRON-uttryck](#cron-expressions) eller en [TimeSpan](#timespan) värde. En `TimeSpan` kan bara användas för en funktionsapp som körs på en App Service Plan. Du kan placera schema-uttrycket i en appinställning och ange egenskapen till appinställningen namn och är inneslutna i **%** tecken, som i följande exempel: ”% ScheduleAppSetting %”. |
|**runOnStartup**|**runOnStartup**|Om `true`, funktionen anropas när körningen startar. Till exempel startar körningen när funktionsappen aktiveras efter inaktivitet på grund av inaktivitet. När appen startas om på grund av funktionen ändringar, och när appen skalas ut. Så **runOnStartup** sällan om någonsin sättas till `true`, särskilt i produktion. |
|**useMonitor**|**useMonitor**|Ange `true` eller `false` att indikera om schemat ska övervakas. Övervakning av schema kvarstår schema förekomster som hjälper till att upprätthålla schemat underhålls korrekt även om funktionen app-instanserna startas om. Om inte har angetts uttryckligen är standardvärdet `true` för scheman som har ett intervall som är större än 1 minut. För scheman som utlöser mer än en gång per minut som standard används `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Vi rekommenderar mot inställningen **runOnStartup** till `true` i produktion. Med den här inställningen är kod som kör vid tidpunkter med hög oförutsägbart. I vissa inställningar för produktion, kan dessa extra körningar resultera i avsevärt högre kostnader för appar i förbrukningsplaner. Till exempel med **runOnStartup** aktiverat utlösaren i anropas när appen skalas. Kontrollera att du förstår produktion beteendet för dina funktioner innan du aktiverar **runOnStartup** i produktion.   

## <a name="usage"></a>Användning

När en timerutlösare anropas den [timer-objekt](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) skickades till funktionen. Följande JSON är en exempel-representation av timer-objekt. 

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

Den `IsPastDue` egenskapen är `true` när den aktuella funktionsanrop är senare än förväntat. Till exempel orsaka en omstart av funktionen app ett anrop till missas.

## <a name="cron-expressions"></a>CRON-uttryck 

Azure Functions använder den [NCronTab](https://github.com/atifaziz/NCrontab) bibliotek som ska tolka CRON-uttryck. Ett CRON-uttryck innehåller sex fält:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Varje fält kan ha något av följande typer av värden:

|Typ  |Exempel  |När det utlöses  |
|---------|---------|---------|
|Ett specifikt värde |<nobr>”0-5 *** *”</nobr>|vid hh:05:00 där hh är varje timme (en gång i timmen)|
|Alla värden (`*`)|<nobr>”0 * 5 ** *”</nobr>|vid 5:mm: 00 varje dag, mm är där varje minut av timme (60 gånger per dag)|
|Ett intervall (`-` operatorn)|<nobr>”5 – 7 **** *”</nobr>|på hh:mm:05, hh:mm:06 och hh:mm:07 där HH: mm är varje minut av varje timme (3 gånger en minut)|  
|En uppsättning värden (`,` operatorn)|<nobr>”5,8,10 **** *”</nobr>|på hh:mm:05, hh:mm:08 och hh:mm:10 där HH: mm är varje minut av varje timme (3 gånger en minut)|
|Ett intervallvärde (`/` operatorn)|<nobr>”0 * / 5 *** *”</nobr>|vid hh:05:00, hh:10:00, hh:15:00 och så vidare till hh:55:00 där hh är varje Timma (12 gånger i timmen)|

Om du vill ange månader eller dagar kan du använda numeriska värden, namn eller förkortningar namn på:

* I dagar är de numeriska värdena 0-6 där 0 börjar med söndag.
* Namn är på engelska. Exempel: `Monday`, `January`.
* Är skiftlägeskänsliga.
* Namnen kan förkortas. Tre bokstäver är den rekommenderade förkortning.  Exempel: `Mon`, `Jan`. 

### <a name="cron-examples"></a>CRON-exempel

Här följer några exempel på CRON-uttryck som du kan använda för timerutlösare i Azure Functions.

|Exempel|När det utlöses  |
|---------|---------|
|`"0 */5 * * * *"`|en gång var femte minut|
|`"0 0 * * * *"`|en gång högst upp på varje timme|
|`"0 0 */2 * * *"`|en gång varannan timme|
|`"0 0 9-17 * * *"`|en gång i timmen från 9: 00 till 17: 00|
|`"0 30 9 * * *"`|kl. 9:30 varje dag|
|`"0 30 9 * * 1-5"`|kl. 9:30 varje vardag|
|`"0 30 9 * Jan Mon"`|kl. 9:30 varje måndag i januari|
>[!NOTE]   
>Du kan hitta CRON-uttrycksexempel online, men många av dem utelämnar den `{second}` fält. Om du kopierar från en av dem kan du lägga till den saknade `{second}` fält. Vanligtvis vill du noll i fältet, inte en asterisk.

### <a name="cron-time-zones"></a>CRON-tidszoner

Siffrorna i ett CRON-uttryck avser en tid och datum, inte ett tidsintervall. Till exempel en 5 i den `hour` fält som refererar till 05:00:00, inte var 5: e timme.

Standardtidszon som används med CRON-uttryck är Coordinated Universal Time (UTC). Om du vill att din CRON-uttryck baserat på en annan tidszon, skapa en app-inställning för din funktionsapp med namnet `WEBSITE_TIME_ZONE`. Ange värdet till namnet på tidszonen som visas i den [Microsoft tidszon Index](https://technet.microsoft.com/library/cc749073). 

Till exempel *Eastern, normaltid* är UTC-05:00. Ha din timer mallutlösaren fire på 10:00 EST varje dag, Använd följande CRON-uttryck som-konton för UTC-tidszonen:

```json
"schedule": "0 0 15 * * *"
``` 

Eller skapa en app-inställning för din funktionsapp med namnet `WEBSITE_TIME_ZONE` och ange värdet **Eastern, normaltid**.  Sedan använder följande CRON-uttryck: 

```json
"schedule": "0 0 10 * * *"
``` 

När du använder `WEBSITE_TIME_ZONE`, tiden justeras efter tidsändringar i den specifika tidszonen, till exempel sommartid. 

## <a name="timespan"></a>Tidsintervall

 En `TimeSpan` kan bara användas för en funktionsapp som körs på en App Service Plan.

Till skillnad från ett CRON-uttryck, en `TimeSpan` värdet anger tidsintervallet mellan varje funktionsanrop. När en funktion är klar när du har kört längre än det angivna intervallet anropar timern direkt funktionen igen.

Uttryckt i form av en sträng i `TimeSpan` format är `hh:mm:ss` när `hh` är mindre än 24. När de två första siffrorna är 24 eller större, formatet är `dd:hh:mm`. Här följer några exempel:

|Exempel |När det utlöses  |
|---------|---------|
|”01: 00:00” | varje timma        |
|”00: 01:00”|Varje minut         |
|"24:00:00" | var 24: e dag        |

## <a name="scale-out"></a>Skalbarhet

Om en funktionsapp skalas ut till flera instanser, körs bara en enda instans av en timerutlöst funktion i alla instanser.

## <a name="function-apps-sharing-storage"></a>Funktionsappar att lagringsutrymmet delas

Om du delar ett Storage-konto i flera funktionsappar, se till att varje funktionsapp har en annan `id` i *host.json*. Du kan utelämna den `id` egenskapen eller manuellt ange varje funktionsapp `id` på ett annat värde. Timer utlösaren använder ett storage-Lås för att se till att endast en timer-instans när en funktionsapp skalas ut till flera instanser. Om två funktionsappar delar samma `id` och var och en använder en timer som utlösare, endast en timer körs.

## <a name="retry-behavior"></a>Funktionssätt för återförsök

Till skillnad från kö-utlösare igen inte timerutlösare efter en funktion misslyckas. När en funktion inte är inte den anropas igen förrän nästa gång på schemat.

## <a name="troubleshooting"></a>Felsökning

Information om vad du gör när timerutlösare inte fungerar som förväntat, finns i [Investigating och rapportera problem med timer-utlösta funktionerna som utlöser inte](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en Snabbstart som använder en timer som utlösare](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
