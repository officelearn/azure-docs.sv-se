---
title: 'Azure Functions F #-utvecklarreferens | Microsoft Docs'
description: 'Förstå hur du utvecklar Azure Functions med F #-skript.'
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: 'Azure functions, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur, F #'
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: b7cb3a7094ef2c11df63c9e5595355d4076e2ccd
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025437"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F #-Utvecklarreferens

F # för Azure Functions är en lösning för att enkelt köra små delar av kod eller ”funktioner”, i molnet. Data flödar till din F #-funktion via funktionsargument. Argumentnamn anges i `function.json`, och det finns fördefinierade namnen för att komma åt saker som funktionen loggare och uppsägning token. 

>[!IMPORTANT]
>F #-skript (.fsx) stöds endast av [version 1.x](functions-versions.md#creating-1x-apps) av Azure Functions-körningen. Om du vill använda F # med version 2.x-körningen, måste du använda en förkompilerade F # klassbiblioteksprojektet (.fs). Skapa, hantera och publicera en F # klassbiblioteksprojektet med Visual Studio precis som en [C#-klassbiblioteksprojektet](functions-dotnet-class-library.md). Läs mer om funktioner versioner [översikten över Azure Functions runtime versioner](functions-versions.md).

Den här artikeln förutsätter att du redan har läst den [Azure Functions för utvecklare](functions-reference.md).

## <a name="how-fsx-works"></a>Hur .fsx fungerar
En `.fsx` filen är en F #-skript. Det kan betraktas som ett F #-projekt som finns i en enda fil. Filen innehåller både koden för ditt program (i det här fallet din Azure-funktion) och direktiv för att hantera beroenden.

När du använder en `.fsx` för en Azure-funktion, ofta krävs sammansättningar inkluderas automatiskt åt dig, så att du kan fokusera på koden för funktionen i stället för ”formaterad”.

## <a name="folder-structure"></a>mappstruktur

Mappstrukturen för en F #-skript projektet ser ut som följande:

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

Det finns en delad [host.json](functions-host-json.md) -fil som kan användas för att konfigurera funktionsappen. Varje funktion har sina egna kodfilen (.fsx) och bindningen konfigurationsfil (function.json).

Bindningen-tillägg som krävs i [version 2.x](functions-versions.md) funktioner runtime definieras i den `extensions.csproj` -fil med faktiska library-filer i den `bin` mapp. När du utvecklar lokalt, måste du [registrera tillägg av bindning](functions-triggers-bindings.md#local-development-azure-functions-core-tools). När du utvecklar funktioner i Azure-portalen görs denna registrering för dig.

## <a name="binding-to-arguments"></a>Bindning till argument
Varje bindningen stöder en uppsättning argument som beskrivs i den [Azure Functions-utlösare och bindningar utvecklarreferens](functions-triggers-bindings.md). En av de argument-bindningar som har stöd för en blob-utlösare är till exempel en POCO som kan uttryckas i en F #-post. Exempel:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Din F # Azure Function tar ett eller flera argument. När vi pratar om Azure Functions argument vi refererar till *inkommande* argument och *utdata* argument. Inkommande argument är precis vad det låter som: ange att din F # Azure Function. En *utdata* argumentet är föränderliga data eller en `byref<>` argument som fungerar som ett sätt att överföra data tillbaka *ut* på din funktion.

I exemplet ovan, `blob` är ett argument som indata och `output` är ett argument som utdata. Observera att vi använde `byref<>` för `output` (behöver inte lägga till den `[<Out>]` anteckning). Med hjälp av en `byref<>` kan din funktion för att ändra vilken post eller objekt som argumentet refererar till.

När du använder en F #-post som en Indatatyp post definitionen måste markeras med `[<CLIMutable>]` avsätts Azure Functions-ramverk för att ställa in fälten korrekt innan de överförs posten till din funktion. Under huven, `[<CLIMutable>]` genererar set-metoder för post-egenskaper. Exempel:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

En F #-klass kan också användas för både i och ut argument. För en klass behöver vanligtvis egenskaper get- och set-metoder. Exempel:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Loggning
Logga utdata till din [direktuppspelningsloggar](../app-service/web-sites-enable-diagnostic-log.md) i F #, funktionen tar ett argument av typen `TraceWriter`. För konsekvens, rekommenderar vi det här argumentet heter `log`. Exempel:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
Den `async` arbetsflöde kan användas, men resultatet måste returnera ett `Task`. Detta kan göras med `Async.StartAsTask`, till exempel:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Annulleringen Token
Om din funktion behöver hantera avstängning utan problem, kan du ge den en [ `CancellationToken` ](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argumentet. Detta kan kombineras med `async`, till exempel:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importera namnområden
Namnområden kan öppnas som vanligt:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Följande namnrymder öppnas automatiskt:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Refererar till externa sammansättningar
På samma sätt framework sammansättningsreferenser kan läggas till med den `#r "AssemblyName"` direktiv.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Följande sammansättningar läggs automatiskt till i Azure-funktioner som är värd för miljön:

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

Dessutom kan följande sammansättningar är speciella bokstäver och kan refereras till av simplename (t.ex. `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Om du vill referera till en privat sammansättning, du kan ladda upp sammansättningsfilen i en `bin` mappen i förhållande till din funktion och referens den med hjälp av filen namn (t.ex.)  `#r "MyAssembly.dll"`). Information om hur du överför filer till mappen funktionen finns i följande avsnitt på pakethantering.

## <a name="editor-prelude"></a>Redigeraren Prelude
En redigerare som har stöd för F #-kompilatorn Services kommer inte att märka namnområden och sammansättningar som automatiskt lägger till Azure Functions. Därför kan det vara användbart att inkludera en prelude som hjälper dig att hitta sammansättningar som du använder redigeraren, och att uttryckligen öppna namnområden. Exempel:

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

När Azure Functions körs din kod kan den bearbetar källan med `COMPILED` definierats så att redigerare prelude kommer att ignoreras.

<a name="package"></a>

## <a name="package-management"></a>Pakethantering
Om du vill använda NuGet-paket i en F #-funktion lägger du till en `project.json` filen till funktionens mapp på den funktionsapp filsystem. Här är ett exempel `project.json` -fil som lägger till en NuGet-paketreferens `Microsoft.ProjectOxford.Face` version 1.1.0:

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

När du laddar upp en `project.json` körnings-fil hämtar paketen och lägger automatiskt till referenser till sammansättningar för paketet. Du behöver inte lägga till `#r "AssemblyName"` direktiv. Lägg bara till de nödvändiga `open` -uttryck för att din `.fsx` fil.

Du kan välja att automatiskt placera referenser sammansättningar i din redigeraren prelude, att förbättra din redigerare interaktion med F # kompilera tjänster.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Hur du lägger till en `project.json` filen till din Azure-funktion
1. Börja genom att kontrollera att din funktionsapp körs som du kan göra genom att öppna din funktion i Azure-portalen. Detta ger även åtkomst till direktuppspelningsloggarna där paketet installationen utdata visas.
2. Ladda upp en `project.json` fil ska du använda någon av metoderna som beskrivs i [så här uppdaterar du funktionen appfiler](functions-reference.md#fileupdate). Om du använder [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md), du kan lägga till en `project.json` filen till din tillfälliga gren för att experimentera med den innan du lägger till den i din gren för distribution.
3. Efter den `project.json` fil läggs till, visas utdata som liknar följande exempel i din funktion strömning loggen:

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
Hämta en miljövariabel eller en app som inställningsvärde `System.Environment.GetEnvironmentVariable`, till exempel:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Återanvända .fsx kod
Du kan använda kod från andra `.fsx` filer med hjälp av en `#load` direktiv. Exempel:

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

Sökvägar som förser den `#load` direktivet är i förhållande till platsen för din `.fsx` fil.

* `#load "logger.fsx"` läser in en fil i mappen funktion.
* `#load "package\logger.fsx"` läser in en fil som finns i den `package` mappen i mappen funktion.
* `#load "..\shared\mylogger.fsx"` läser in en fil som finns i den `shared` mappen på samma nivå som funktionen mappen, det vill säga direkt under `wwwroot`.

Den `#load` direktiv fungerar bara med `.fsx` (skriptu F #) filer, och inte med `.fs` filer.

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [F #-Guide](/dotnet/articles/fsharp/index)
* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
* [Testa för Azure Functions](functions-test-a-function.md)
* [Azure Functions skalning](functions-scale.md)

