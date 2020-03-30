---
title: Timer-utlösare för Azure-funktioner
description: Förstå hur du använder timerutlösare i Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 566d6ccf43024692e19bcd6639fe5cfbbba0660d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056414"
---
# <a name="timer-trigger-for-azure-functions"></a>Timer-utlösare för Azure-funktioner 

I den här artikeln beskrivs hur du arbetar med timerutlösare i Azure Functions. Med en timerutlösare kan du köra en funktion enligt ett schema. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - Funktioner 1.x

Timer-utlösaren finns i [Paketet Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, version 2.x. Källkoden för paketet finns i [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub-databasen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket - Funktioner 2.x och högre

Timer-utlösaren finns i [Paketet Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, version 3.x. Källkoden för paketet finns i [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub-databasen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som körs varje gång minuterna har ett värde som är delbart med fem (t.ex. om funktionen börjar klockan 18:57:00, kommer nästa prestanda att vara klockan 19:00:00). Objektet [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) skickas in i funktionen.

```cs
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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en timerutlösarebindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionstallelsen beror på en missad schemaförekomst. Objektet [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) skickas in i funktionen.

Här är bindningsdata i *filen function.json:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är C# skriptkoden:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en timerutlösarebindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionstallelsen beror på en missad schemaförekomst. Ett [timerobjekt](#usage) skickas in i funktionen.

Här är bindningsdata i *filen function.json:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är JavaScript-koden:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel används en timerutlösarebindning vars konfiguration beskrivs i *filen function.json.* Den faktiska [Python-funktionen](functions-reference-python.md) som använder bindningen beskrivs * __i init__.py-filen.* Objektet som skickas till funktionen är av typen [azure.functions.TimerRequest-objekt](/python/api/azure-functions/azure.functions.timerrequest). Funktionslogiken skriver till loggarna som anger om den aktuella åkallan beror på en missad schemaförekomst. 

Här är bindningsdata i *filen function.json:*

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Här är Python-koden:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

# <a name="java"></a>[Java](#tab/java)

Följande exempelfunktion utlöser och körs var femte minut. Anteckningen `@TimerTrigger` i funktionen definierar schemat med samma strängformat som [CRON-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Attributets konstruktor tar ett `TimeSpan`CRON-uttryck eller ett . Du kan `TimeSpan` bara använda om funktionsappen körs på en App Service-plan. `TimeSpan`stöds inte för förbrukning eller elastiska premiumfunktioner.

I följande exempel visas ett CRON-uttryck:

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Anteckningen `@TimerTrigger` i funktionen definierar schemat med samma strängformat som [CRON-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `TimerTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas in på "timerTrigger". Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste ställas in på "in". Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar timerobjektet i funktionskoden. | 
|**Schema**|**SchemaUttryck**|Ett [CRON-uttryck](#ncrontab-expressions) eller ett [TimeSpan-värde.](#timespan) A `TimeSpan` kan endast användas för en funktionsapp som körs på en appserviceplan. Du kan placera schemauttrycket i en appinställning och ställa in **%** den här egenskapen på appinställningsnamnet som är insvept i tecken, som i det här exemplet: "%ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Om `true`anropas funktionen när körningen startar. Körtiden startar till exempel när funktionsappen aktiveras efter att den har gått inaktiv på grund av inaktivitet. när funktionsappen startas om på grund av funktionsändringar och när funktionsappen skalas ut. Så **runOnStartup** bör sällan eller `true`aldrig ställas in på , särskilt i produktion. |
|**användaMonitor**|**AnvändMonitor**|Ställ `true` in `false` på eller ange om schemat ska övervakas. Schemaövervakning kvarstår schemaförekomster för att säkerställa att schemat underhålls korrekt även när funktionsappinstanser startas om. Om den inte uttryckligen `true` anges är standardinställningen för scheman som har ett upprepningsintervall som är större än eller lika med 1 minut. För scheman som utlöser mer än `false`en gång per minut är standardvärdet .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Vi rekommenderar att du inte `true` ställer in **runOnStartup** i produktion. Om du använder den här inställningen körs koden vid mycket oförutsägbara tidpunkter. I vissa produktionsinställningar kan dessa extra körningar resultera i betydligt högre kostnader för appar som finns i förbrukningsplaner. Med **runOnStartup** aktiverat anropas utlösaren till exempel när funktionsappen skalas. Se till att du har full förståelse för produktionsbeteendet för dina funktioner innan du aktiverar **runOnStartup** i produktionen.   

## <a name="usage"></a>Användning

När en timerutlösarfunktion anropas skickas ett timerobjekt in i funktionen. Följande JSON är en exempelrepresentation av timerobjektet.

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

Egenskapen `IsPastDue` `true` är när den aktuella funktionsyrkningen är senare än schemalagd. En omstart av funktionsappen kan till exempel orsaka att en anrop missas.

## <a name="ncrontab-expressions"></a>NCRONTAB-uttryck 

Azure Functions använder [NCronTab-biblioteket](https://github.com/atifaziz/NCrontab) för att tolka NCRONTAB-uttryck. Ett NCRONTAB-uttryck liknar ett CRON-uttryck förutom att det innehåller ytterligare ett sjätte fält i början som ska användas för tidsprecision i sekunder:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Varje fält kan ha någon av följande typer av värden:

|Typ  |Exempel  |När utlöses  |
|---------|---------|---------|
|Ett specifikt värde |<nobr>"0 5 * * * *"</nobr>|på hh:05:00 där hh är varje timme (en gång i timmen)|
|Alla värden`*`( )|<nobr>"0 * 5 * * *"</nobr>|vid 5:mm:00 varje dag, där mm är varje minut av timmen (60 gånger om dagen)|
|Ett intervall`-` (operatör)|<nobr>"5-7 * * * * *"</nobr>|på hh:mm:05,hh:mm:06 och hh:mm:07 där hh:mm är varje minut i varje timme (3 gånger per minut)|
|En uppsättning värden`,` (operator)|<nobr>"5,8,10 * * * * *"</nobr>|på hh:mm:05,hh:mm:08 och hh:mm:10 där hh:mm är varje minut i varje timme (3 gånger per minut)|
|Ett intervallvärde`/` (operator)|<nobr>"0 */5 * * * *"</nobr>|på hh:00:00, hh:05:00, hh:10:00, och så vidare genom hh:55:00 där hh är varje timme (12 gånger i timmen)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB exempel

Här är några exempel på NCRONTAB-uttryck som du kan använda för timerutlösaren i Azure Functions.

|Exempel|När utlöses  |
|---------|---------|
|`"0 */5 * * * *"`|en gång var femte minut|
|`"0 0 * * * *"`|en gång högst upp i varje timme|
|`"0 0 */2 * * *"`|en gång varannan timme|
|`"0 0 9-17 * * *"`|en gång i timmen från 09.00 till 17.00|
|`"0 30 9 * * *"`|vid 09:30 varje dag|
|`"0 30 9 * * 1-5"`|vid 09:30 varje vardag|
|`"0 30 9 * Jan Mon"`|vid 09:30 varje måndag i januari|


### <a name="ncrontab-time-zones"></a>TIDSZONER FÖR NCRONTAB

Siffrorna i ett CRON-uttryck refererar till en tid och ett datum, inte ett tidsintervall. En 5:a i `hour` fältet refererar till exempel till 05:00, inte var femte timme.

Standardtidszonen som används med CRON-uttrycken är Coordinated Universal Time (UTC). Om du vill ha cron-uttrycket baserat på en annan `WEBSITE_TIME_ZONE`tidszon skapar du en appinställning för funktionsappen . Ange värdet till namnet på önskad tidszon enligt [Microsofts tidszonsindex](https://technet.microsoft.com/library/cc749073).

  > [!NOTE]
  > `WEBSITE_TIME_ZONE`stöds för närvarande inte i Linux-förbrukningsplanen.

*Östlig standardtid* är till exempel UTC-05:00. Om du vill att timern ska utlösa brand klockan 10:00 EST varje dag använder du följande NCRONTAB-uttryck som står för UTC-tidszon:

```
"0 0 15 * * *"
``` 

Eller skapa en appinställning för `WEBSITE_TIME_ZONE` funktionsappen som heter och ange värdet till **Eastern Standard Time**.  Använder sedan följande NCRONTAB-uttryck: 

```
"0 0 10 * * *"
``` 

När du `WEBSITE_TIME_ZONE`använder justeras tiden för tidsändringar i den specifika tidszonen, till exempel sommartid. 

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` kan endast användas för en funktionsapp som körs på en appserviceplan.

Till skillnad från `TimeSpan` ett CRON-uttryck anger ett värde tidsintervallet mellan varje funktionsanrop. När en funktion har slutförts efter att ha kört längre än det angivna intervallet anropar timern omedelbart funktionen igen.

Formatet uttrycks som `TimeSpan` en `hh:mm:ss` `hh` sträng när det är mindre än 24. När de två första siffrorna är 24 `dd:hh:mm`eller högre är formatet . Här följer några exempel:

|Exempel |När utlöses  |
|---------|---------|
|"01:00:00" | varje timme        |
|"00:01:00"|varje minut         |
|"24:00:00" | var 24:e dag        |
|"1.00:00:00" | Varje dag        |

## <a name="scale-out"></a>Utskalning

Om en funktionsapp skalas ut till flera instanser körs endast en enda instans av en timerutlöst funktion i alla instanser.

## <a name="function-apps-sharing-storage"></a>Dela lagring av funktionsappar

Om du delar lagringskonton mellan funktionsappar som inte distribueras till apptjänsten kan du behöva uttryckligen tilldela värd-ID till varje app.

| Version av funktioner | Inställning                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (och högre)  | `AzureFunctionsWebHost__hostid`miljövariabel |
| 1.x               | `id`i *host.json*                                  |

Du kan utelämna identifieringsvärdet eller manuellt ange identifieringskonfigurationen för varje funktionsapp till ett annat värde.

Timerutlösaren använder ett lagringslås för att säkerställa att det bara finns en timerinstans när en funktionsapp skalas ut till flera instanser. Om två funktionsappar delar samma identifieringskonfiguration och var och en använder en timerutlösare körs bara en timer.

## <a name="retry-behavior"></a>Beteende för återförsök

Till skillnad från köutlösaren försöker inte timerutlösaren igen när en funktion har misslyckats. När en funktion misslyckas anropas den inte igen förrän nästa gång enligt schemat.

## <a name="troubleshooting"></a>Felsökning

Information om vad som ska göra när timerutlösaren inte fungerar som förväntat finns i [Undersöka och rapportera problem med timerutlösta funktioner som inte utlöses](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en snabbstart som använder en timerutlösare](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)
