---
title: "Azure Functions F # för utvecklare | Microsoft Docs"
description: "Förstå hur du utvecklar Azure Functions med F #."
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, webhooks, dynamiska beräkning, serverlösa arkitektur, F #"
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: functions
ms.devlang: fsharp
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/09/2016
ms.author: syclebsc
ms.openlocfilehash: 039306b093d92b66883edcca10e42f7b1dbc7245
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F # för utvecklare
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

F # för Azure Functions är en lösning för att enkelt köra små delar av kod eller ”funktioner”, i molnet. Data flödar in din F #-funktion via funktionsargument. Argumentnamn anges i `function.json`, och det finns fördefinierade namn för att komma åt sådant som funktionen loggning och annullering token.

Den här artikeln förutsätter att du redan har läst den [Azure Functions för utvecklare](functions-reference.md).

## <a name="how-fsx-works"></a>Så här fungerar .fsx
En `.fsx` filen är ett F # skript. Det kan betraktas som ett F #-projekt som ingår i en enda fil. Filen innehåller både koden för programmet (i det här fallet din Azure-funktion) och riktlinjer för att hantera beroenden.

När du använder en `.fsx` för en Azure-funktion ofta krävs sammansättningar ingår automatiskt för dig, så att du kan fokusera på koden funktion i stället för ”standardtext”.

## <a name="binding-to-arguments"></a>Bindning till argument
Varje bindning stöder vissa argument, enligt anvisningarna i den [Azure Functions-utlösare och bindningar utvecklare](functions-triggers-bindings.md). Till exempel är en argumentet bindningar har stöd för en blob-utlösare en POCO som kan uttryckas i en F #-post. Exempel:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

F # Azure funktionen tar ett eller flera argument. När vi pratar om Azure Functions argument vi refererar till *inkommande* argument och *utdata* argument. En indataargument är exakt vad det låter som: indata till din F # Azure-funktion. En *utdata* argumentet är föränderliga data eller en `byref<>` argumentet som fungerar som ett sätt att skicka data tillbaka *ut* för din funktion.

I exemplet ovan, `blob` är ett indataargument och `output` finns ett argument som utdata. Observera att vi använde `byref<>` för `output` (behöver inte lägga till den `[<Out>]` anteckningen). Med hjälp av en `byref<>` kan din funktion att ändra vilken post eller argumentet refererar till objektet.

När en post F # används som en Indatatyp post definition måste markeras med `[<CLIMutable>]` för att tillåta Azure Functions framework ange fälten korrekt innan du skickar posten till funktionen. Under huven, `[<CLIMutable>]` genererar set-metoder för post-egenskaper. Exempel:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

En F #-klass kan också användas för både i och ut argument. För en klass behöver vanligtvis egenskaper get och set. Exempel:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Loggning
Logga utdata till din [direktuppspelningsloggar](../app-service/web-sites-enable-diagnostic-log.md) F #, din funktion bör ta ett argument av typen `TraceWriter`. För konsekvens, rekommenderar vi det här argumentet heter `log`. Exempel:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Asynkrona
Den `async` arbetsflöde kan användas, men resultatet måste returnera en `Task`. Detta kan göras med `Async.StartAsTask`, till exempel:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Annullering Token
Om din funktion behöver hantera avstängning utan problem, kan du ge det ett [ `CancellationToken` ](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argumentet. Detta kan kombineras med `async`, till exempel:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importera namnområden
Namnområden kan öppnas på vanligt sätt:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Följande namnområden öppnas automatiskt:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Refererar till externa sammansättningar
På liknande sätt framework sammansättningsreferenser kan läggas till med den `#r "AssemblyName"` direktiv.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Följande sammansättningar läggs automatiskt till i Azure Functions värdmiljön:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Dessutom kan följande sammansättningar är särskilda fall och kan refereras av simplename (t.ex. `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Om du behöver referera till en privat sammansättning kan du överföra sammansättningsfilen i en `bin` mapp i förhållande till din funktion och referens den med hjälp av filen namn (t.ex.)  `#r "MyAssembly.dll"`). Information om hur du överför filer i mappen funktionen finns i följande avsnitt på paketet management.

## <a name="editor-prelude"></a>Redigeraren Prelude
En redigerare som stöder F #-kompilatorn Services är inte medveten om namnområden och sammansättningar som automatiskt lägger till Azure Functions. Därför kan det vara användbart att inkludera en prelude som hjälper redigeraren hitta sammansättningar som du använder, och att uttryckligen öppna namnområden. Exempel:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

När Azure Functions utförs koden, bearbetar den källa med `COMPILED` definierats så editor prelude kommer att ignoreras.

<a name="package"></a>

## <a name="package-management"></a>Hantering av paketet
Använd NuGet-paket i en F #-funktionen genom att lägga till en `project.json` -filen till den funktionen mapp i appen funktionen filsystem. Här är ett exempel `project.json` -fil som lägger till en NuGet-paketet referens till `Microsoft.ProjectOxford.Face` version 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Endast .NET Framework 4.6 stöds, så se till att din `project.json` filen anger `net46` som visas här.

När du överför en `project.json` fil körningsmiljön hämtar paketen och lägger automatiskt till referenser till paketet sammansättningar. Du behöver inte lägga till `#r "AssemblyName"` direktiven. Lägg bara till de nödvändiga `open` instruktioner till din `.fsx` fil.

Du kanske vill placera automatiskt referenser sammansättningar i din editor prelude, att förbättra din editor interaktion med F # kompilera tjänster.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Hur du lägger till en `project.json` filen till din Azure-funktion
1. Börja genom att kontrollera att funktionen appen körs det gör du genom att öppna din funktion i Azure-portalen. Detta ger även åtkomst till direktuppspelningsloggar där paketet installationen utdata visas.
2. Att överföra en `project.json` fil ska du använda en av metoderna som beskrivs i [hur du uppdaterar funktionen programfilerna](functions-reference.md#fileupdate). Om du använder [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md), kan du lägga till en `project.json` filen till din fristående gren för att testa den innan du lägger till den i din distribution gren.
3. Efter den `project.json` fil har lagts till, visas utdata som liknar följande exempel i din funktion strömning loggen:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Miljövariabler
För att få en miljövariabel eller en app som inställningsvärde använda `System.Environment.GetEnvironmentVariable`, till exempel:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Återanvända .fsx kod
Du kan använda koden från andra `.fsx` filer med hjälp av en `#load` direktiv. Exempel:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Sökvägar som förser de `#load` direktivet är i förhållande till platsen för din `.fsx` fil.

* `#load "logger.fsx"` läser in en fil i mappen funktion.
* `#load "package\logger.fsx"` läser in en fil i den `package` mappen i mappen funktion.
* `#load "..\shared\mylogger.fsx"` läser in en fil i den `shared` mappen på samma nivå som funktionen mappen, som är direkt under `wwwroot`.

Den `#load` direktiv fungerar bara med `.fsx` (F #) skriptfiler och inte med `.fs` filer.

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [F # Guide](/dotnet/articles/fsharp/index)
* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
* [Azure Functions testning](functions-test-a-function.md)
* [Azure Functions skalning](functions-scale.md)

