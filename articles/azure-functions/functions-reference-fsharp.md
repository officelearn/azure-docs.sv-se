---
title: Azure Functions F#-utvecklarreferens
description: Förstå hur du utvecklar Azure-funktioner med F#-skriptet.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276769"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F# Utvecklarreferens

F# för Azure Functions är en lösning för att enkelt köra små bitar av kod, eller "funktioner", i molnet. Data flödar in i din F#-funktion via funktionsargument. Argumentnamn anges i `function.json`och det finns fördefinierade namn för åtkomst till saker som funktionslogger och annulleringstoken. 

>[!IMPORTANT]
>F#-skript (.fsx) stöds endast av [version 1.x](functions-versions.md#creating-1x-apps) av Azure Functions-körningen. Om du vill använda F# med version 2.x och senare versioner av körningen måste du använda ett förkompilerat F#-klassbiblioteksprojekt (.fs). Du skapar, hanterar och publicerar ett F#-klassbiblioteksprojekt med Visual Studio på samma sätt som ett [C#-klassbiblioteksprojekt](functions-dotnet-class-library.md). Mer information om Functions-versioner finns i [översikt över Azure Functions runtime-versioner](functions-versions.md).

Den här artikeln förutsätter att du redan har läst [utvecklarreferensen för Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Så här fungerar FSX
En `.fsx` fil är ett F#-skript. Det kan ses som ett F# projekt som finns i en enda fil. Filen innehåller både koden för ditt program (i det här fallet din Azure-funktion) och direktiv för hantering av beroenden.

När du `.fsx` använder en för en Azure-funktion inkluderas vanligtvis nödvändiga sammansättningar automatiskt för dig, så att du kan fokusera på funktionen i stället för "standardkod".

## <a name="folder-structure"></a>Mappstrukturen

Mappstrukturen för ett F#-skriptprojekt ser ut så här:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Det finns en delad [host.json-fil](functions-host-json.md) som kan användas för att konfigurera funktionsappen. Varje funktion har en egen kodfil (.fsx) och bindningskonfigurationsfilen (function.json).

De bindningstillägg som krävs i [version 2.x och senare versioner](functions-versions.md) av funktionskörningen `bin` `extensions.csproj` definieras i filen, med de faktiska biblioteksfilerna i mappen. När du utvecklar lokalt måste du [registrera bindningstillägg.](./functions-bindings-register.md#extension-bundles) När du utvecklar funktioner i Azure-portalen görs den här registreringen åt dig.

## <a name="binding-to-arguments"></a>Bindning till argument
Varje bindning stöder vissa argument, som beskrivs i [Azure Functions-utlösare och bindningar utvecklarreferens](functions-triggers-bindings.md). Ett av argumenten som binder en blob-utlösare stöder är till exempel en POCO, som kan uttryckas med hjälp av en F#-post. Ett exempel:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Din F# Azure-funktion tar ett eller flera argument. När vi talar om Azure Functions-argument refererar vi till *indataargument* och *utdataargument.* Ett indataargument är precis vad det låter som: indata till din F# Azure-funktion. Ett *utdataargument* är `byref<>` föränderliga data eller ett argument som fungerar som ett sätt att skicka data tillbaka *från* din funktion.

I exemplet ovan `blob` är ett indataargument och `output` är ett utdataargument. Observera att `byref<>` vi `output` använde för (du behöver `[<Out>]` inte lägga till anteckningen). Med `byref<>` hjälp av en typ kan funktionen ändra vilken post eller vilket objekt som argumentet refererar till.

När en F#-post används som indatatyp måste `[<CLIMutable>]` postdefinitionen markeras med för att Azure Functions-ramverket ska kunna ange fälten på lämpligt sätt innan posten skickas till din funktion. Under huven `[<CLIMutable>]` genererar setters för skivegenskaperna. Ett exempel:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

En F#-klass kan också användas för både in- och ut-argument. För en klass behöver egenskaper vanligtvis getters och setters. Ett exempel:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Loggning
För att logga utdata till dina [streamingloggar](../app-service/troubleshoot-diagnostic-logs.md) i F#, bör din funktion ta ett argument av typ [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). För konsekvens rekommenderar vi att `log`det här argumentet heter . Ett exempel:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Asynkrona
Arbetsflödet `async` kan användas, men resultatet måste `Task`returnera en . Detta kan göras `Async.StartAsTask`med , till exempel:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Avbokningstoken
Om din funktion behöver hantera avstängning graciöst, kan du ge det ett [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argument. Detta kan kombineras `async`med till exempel:

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
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
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

## <a name="referencing-external-assemblies"></a>Referera till externa sammansättningar
På samma sätt kan ramförsamlingsreferenser läggas till med direktivet. `#r "AssemblyName"`

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Följande sammansättningar läggs automatiskt till av Azure Functions-värdmiljön:

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

Dessutom är följande sammansättningar specialmonterade och kan refereras med ett enkelt `#r "AssemblyName"`namn (t.ex.

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Om du behöver referera till en privat sammansättning kan `bin` du ladda upp sammansättningsfilen till en mapp i förhållande till din funktion och referera till den med hjälp av filnamnet (t.ex.  `#r "MyAssembly.dll"`). Information om hur du laddar upp filer till funktionsmappen finns i följande avsnitt om pakethantering.

## <a name="editor-prelude"></a>Förspel till redaktör
En redigerare som stöder F# Compiler Services känner inte till de namnområden och sammansättningar som Azure Functions automatiskt innehåller. Därför kan det vara användbart att inkludera ett förspel som hjälper redigeraren att hitta de sammansättningar du använder och att uttryckligen öppna namnområden. Ett exempel:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

När Azure Functions kör din kod bearbetar den källan med `COMPILED` definierad, så att editorförspelet ignoreras.

<a name="package"></a>

## <a name="package-management"></a>Pakethantering
Om du vill använda NuGet-paket i `project.json` en F#-funktion lägger du till en fil i funktionens mapp i funktionsappens filsystem. Här är `project.json` en exempelfil som lägger `Microsoft.ProjectOxford.Face` till en NuGet-paketreferens till version 1.1.0:

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

Endast .NET Framework 4.6 stöds, så `project.json` se till `net46` att filen anger som visas här.

När du `project.json` laddar upp en fil hämtar körningen paketen och lägger automatiskt till referenser till paketsammansättningarna. Du behöver inte lägga `#r "AssemblyName"` till direktiv. Lägg bara `open` till de `.fsx` nödvändiga uttalandena i filen.

Du kanske vill placera automatiskt referenser församlingar i din redaktör förspel, för att förbättra din redaktör interaktion med F # Kompilera tjänster.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Så här `project.json` lägger du till en fil i din Azure-funktion
1. Börja med att se till att din funktionsapp körs, vilket du kan göra genom att öppna din funktion i Azure-portalen. Detta ger också tillgång till streamingloggar där paketinstallationsutdata visas.
2. Om du `project.json` vill ladda upp en fil använder du någon av de metoder som beskrivs i [hur du uppdaterar funktionsappfiler](functions-reference.md#fileupdate). Om du använder [kontinuerlig distribution för Azure-funktioner](functions-continuous-deployment.md)kan du lägga till en `project.json` fil i din mellanlagringsgren för att experimentera med den innan du lägger till den i distributionsgrenen.
3. När `project.json` filen har lagts till visas utdata som liknar följande exempel i funktionens direktuppspelningslogg:

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
Om du vill hämta en miljövariabel eller ett appinställningsvärde använder du `System.Environment.GetEnvironmentVariable`till exempel:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Återanvända FSX-kod
Du kan använda `.fsx` kod från `#load` andra filer med hjälp av ett direktiv. Ett exempel:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Sökvägarna till `#load` direktivet är relativa till `.fsx` platsen för filen.

* `#load "logger.fsx"`läser in en fil som finns i funktionsmappen.
* `#load "package\logger.fsx"`läser in en `package` fil som finns i mappen i funktionsmappen.
* `#load "..\shared\mylogger.fsx"`läser in en `shared` fil som finns i mappen på samma nivå `wwwroot`som funktionsmappen, det vill de vill ha, direkt under .

Direktivet `#load` fungerar bara `.fsx` med (F# script) `.fs` filer, och inte med filer.

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [F# Guide](/dotnet/articles/fsharp/index)
* [Metodtips för Azure-funktioner](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* [Testning av Azure Functions](functions-test-a-function.md)
* [Skala azure-funktioner](functions-scale.md)

