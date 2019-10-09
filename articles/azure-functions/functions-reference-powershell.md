---
title: Referens för PowerShell-utvecklare för Azure Functions
description: Lär dig hur du utvecklar funktioner med hjälp av PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 9163f2b7943a8022b88b2ed514f4a466e61a8d98
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029014"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell-guide för utvecklare

Den här artikeln innehåller information om hur du skriver Azure Functions med hjälp av PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

En PowerShell-funktion (funktion) i Azure representeras som ett PowerShell-skript som körs när den utlöses. Varje funktions skript har en relaterad `function.json`-fil som definierar hur funktionen fungerar, till exempel hur den utlöses och dess indata-och utdataparametrar. Mer information finns i [artikeln utlösare och bindning](functions-triggers-bindings.md). 

Precis som andra typer av funktioner, tar PowerShell-skript funktioner i parametrar som matchar namnen på alla angivna bindningar i filen `function.json`. En `TriggerMetadata`-parameter skickas också som innehåller ytterligare information om utlösaren som startade funktionen.

Den här artikeln förutsätter att du redan har läst [Azure Functions Developer-referensen](functions-reference.md). Du bör också ha slutfört funktionen [snabb start för PowerShell](functions-create-first-function-powershell.md) för att skapa din första PowerShell-funktion.

## <a name="folder-structure"></a>Mappstruktur

Den obligatoriska mappstrukturen för ett PowerShell-projekt ser ut ungefär så här. Standardvärdet kan ändras. Mer information finns i avsnittet [scriptFile](#configure-function-scriptfile) nedan.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

I projekt roten finns det en delad [`host.json`-](functions-host-json.md) fil som kan användas för att konfigurera Function-appen. Varje funktion har en mapp med en egen kod fil (. ps1) och en bindnings konfigurations fil (`function.json`). Namnet på function. JSON-filens överordnade katalog är alltid namnet på din funktion.

Vissa bindningar kräver förekomst av en `extensions.csproj`-fil. Bindnings tillägg, som krävs i [version 2. x](functions-versions.md) av Functions-körningen, definieras i filen `extensions.csproj` med de faktiska biblioteksfilerna i mappen `bin`. När du utvecklar lokalt måste du [Registrera bindnings tillägg](functions-bindings-register.md#extension-bundles). När du utvecklar funktioner i Azure Portal görs registreringen åt dig.

I PowerShell Function-appar kan du eventuellt ha en `profile.ps1` som körs när en Function-app börjar köras (på annat sätt vet som en *[kall start](#cold-start)* ). Mer information finns i [PowerShell-profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definiera ett PowerShell-skript som en funktion

Som standard söker Functions-körningen efter din funktion i `run.ps1`, där `run.ps1` delar samma överordnade katalog som dess motsvarande `function.json`.

Ditt skript har skickat ett antal argument vid körning. Om du vill hantera dessa parametrar lägger du till ett `param`-block överst i skriptet som i följande exempel:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata-parameter

Parametern `TriggerMetadata` används för att tillhandahålla ytterligare information om utlösaren. Ytterligare metadata varierar från bindning till bindning, men alla innehåller en `sys`-egenskap som innehåller följande data:

```powershell
$TriggerMetadata.sys
```

| Egenskap   | Description                                     | type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | När, i UTC, utlöstes funktionen        | DateTime |
| MethodName | Namnet på den funktion som har utlösts     | sträng   |
| RandGuid   | ett unikt GUID för den här körningen av funktionen | sträng   |

Varje utlösnings typ har en annan uppsättning metadata. Exempel: `$TriggerMetadata` för `QueueTrigger` innehåller `InsertionTime`, `Id`, `DequeueCount`, bland annat. För ytterligare information om köns utlösare metadata, gå till den [officiella dokumentationen för köade utlösare](functions-bindings-storage-queue.md#trigger---message-metadata). Läs dokumentationen om de [utlösare](functions-triggers-bindings.md) som du arbetar med för att se vad som ingår i utlösarens metadata.

## <a name="bindings"></a>Bindningar

I PowerShell konfigureras och definieras [bindningarna](functions-triggers-bindings.md) i en funktions funktion. JSON. Funktioner interagerar med bindningar på flera sätt.

### <a name="reading-trigger-and-input-data"></a>Läsa utlösare och indata

Utlösare och angivna bindningar läses som parametrar som skickas till din funktion. Angivna bindningar har en `direction` inställt på `in` i function. JSON. Egenskapen `name` som definieras i `function.json` är namnet på parametern i `param`-blocket. Eftersom PowerShell använder namngivna parametrar för bindning, spelar det ingen roll för parametrarna. Det är dock en bra idé att följa ordningen på de bindningar som definierats i `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Skriver utdata

I functions är en utgående bindning en `direction` inställd på att `out` i funktionen. JSON. Du kan skriva till en utgående bindning med hjälp av cmdleten `Push-OutputBinding`, som är tillgänglig för functions-körningen. I samtliga fall motsvarar egenskapen `name` för bindningen enligt definitionen i `function.json` den `Name`-parametern i `Push-OutputBinding`-cmdleten.

Följande visar hur du anropar `Push-OutputBinding` i funktions skriptet:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Du kan också skicka ett värde för en speciell bindning via pipelinen.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` fungerar annorlunda beroende på det angivna värdet för `-Name`:

* Om det angivna namnet inte kan matchas med en giltig utgående bindning, genereras ett fel.

* När bindningen för utdata accepterar en samling värden kan du anropa `Push-OutputBinding` upprepade gånger för att skicka flera värden.

* När bindningen för utdata bara accepterar ett singleton-värde, anropar `Push-OutputBinding` en andra gång ett fel.

#### <a name="push-outputbinding-syntax"></a>syntax för `Push-OutputBinding`

Följande är giltiga parametrar för att anropa `Push-OutputBinding`:

| Name | type | Position | Beskrivning |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Sträng | 1 | Namnet på den utgående bindning som du vill ange. |
| **`-Value`** | Object | 2 | Värdet för den utgående bindning som du vill ange, som accepteras från pipelinen ByValue. |
| **`-Clobber`** | SwitchParameter | Följande | Valfritt Tvingar värdet att anges för en angiven utgående bindning när den anges. | 

Följande vanliga parametrar stöds också: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Mer information finns i [om CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Push-OutputBinding exempel: HTTP-svar

En HTTP-utlösare returnerar ett svar med hjälp av en utgående bindning med namnet `response`. I följande exempel har utgående bindningen för `response` värdet "utdata #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Eftersom utdata är till HTTP, som endast accepterar ett singleton-värde, uppstår ett fel när `Push-OutputBinding` anropas en andra gång.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

För utdata som bara accepterar singleton-värden kan du använda parametern `-Clobber` om du vill åsidosätta det gamla värdet i stället för att försöka lägga till i en samling. I följande exempel förutsätter vi att du redan har lagt till ett värde. Genom att använda `-Clobber` åsidosätter svaret från följande exempel det befintliga värdet för att returnera värdet "utdata #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding exempel: Utgående bindning för kö

`Push-OutputBinding` används för att skicka data till utgående bindningar, t. ex. en [Azure Queue Storage utgående bindning](functions-bindings-storage-queue.md#output). I följande exempel har meddelandet som skrivs till kön värdet "utdata #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Utgående bindning för en lagrings kön accepterar flera värden för utdata. I det här fallet anropar du följande exempel efter de första skrivningarna till kön en lista med två objekt: "utdata #1" och "utdata #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

I följande exempel läggs ytterligare två värden till i insamlingen om du anropar de föregående två värdena:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

När det skrivs till kön innehåller meddelandet följande fyra värden: "utdata #1", "utdata #2", "utdata #3" och "utdata #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`-cmdlet

Du kan använda cmdleten `Get-OutputBinding` för att hämta de värden som för närvarande är inställda för dina utgående bindningar. Denna cmdlet hämtar en hash-tabellen som innehåller namnen på utgående bindningarna med respektive värden. 

Följande är ett exempel på hur du använder `Get-OutputBinding` för att returnera aktuella bindnings värden:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` innehåller även en parameter med namnet `-Name`, som kan användas för att filtrera den returnerade bindningen, som i följande exempel:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Jokertecken (*) stöds i `Get-OutputBinding`.

## <a name="logging"></a>Loggning

Loggning i PowerShell-funktioner fungerar som vanlig PowerShell-loggning. Du kan använda loggnings-cmdletar för att skriva till varje utdataström. Varje cmdlet mappar till en loggnings nivå som används av functions.

| Funktions loggnings nivå | Loggnings-cmdlet |
| ------------- | -------------- |
| Fel | **`Write-Error`** |
| Varning | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Skriver till loggning på _informations_ nivå. |
| Felsöka | **`Write-Debug`** |
| Spårning | **`Write-Progress`** <br /> **`Write-Verbose`** |

Förutom dessa cmdlets omdirigeras allt som skrivs till pipelinen till `Information`-loggnivå och visas med standardvärdet PowerShell-format.

> [!IMPORTANT]
> Att använda cmdletarna `Write-Verbose` eller `Write-Debug` är inte tillräckligt för att se utförlig loggning och fel söknings nivå. Du måste också konfigurera logg nivå tröskeln, som deklarerar vilken nivå av loggar du faktiskt bryr dig om. Mer information finns i [Konfigurera Function-programmets loggnings nivå](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurera Function-programmets loggnings nivå

Med Azure Functions kan du definiera tröskel nivån för att göra det enkelt att styra hur funktioner skriver till loggarna. Om du vill ange tröskelvärdet för alla spår som skrivs till-konsolen använder du egenskapen `logging.logLevel.default` i [`host.json` File][Host. JSON-referens]. Den här inställningen gäller för alla funktioner i din Function-app.

I följande exempel anges tröskelvärdet för att aktivera utförlig loggning för alla funktioner, men anger tröskelvärdet för att aktivera fel söknings loggning för en funktion med namnet `MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Mer information finns i [Host. JSON-referens].

### <a name="viewing-the-logs"></a>Visa loggarna

Om din Funktionsapp körs i Azure kan du använda Application Insights för att övervaka den. Läs [övervaknings Azure Functions](functions-monitoring.md) om du vill veta mer om att visa och fråga funktions loggar.

Om du kör din Funktionsapp lokalt för utveckling loggar standardinställningarna i fil systemet. Om du vill se loggarna i-konsolen ställer du in miljövariabeln `AZURE_FUNCTIONS_ENVIRONMENT` på `Development` innan du startar Funktionsapp.

## <a name="triggers-and-bindings-types"></a>Typer av utlösare och bindningar

Det finns ett antal utlösare och bindningar som är tillgängliga för dig att använda med din Function-app. Du hittar en fullständig lista över utlösare och bindningar [här](functions-triggers-bindings.md#supported-bindings).

Alla utlösare och bindningar representeras i kod som några få verkliga data typer:

* Hash
* sträng
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

De fem första typerna i den här listan är standard-.NET-typer. De sista två används endast av HttpTrigger- [utlösaren](#http-triggers-and-bindings).

Varje bindnings parameter i dina funktioner måste vara en av de här typerna.

### <a name="http-triggers-and-bindings"></a>HTTP-utlösare och bindningar

HTTP-och webhook-utlösare och HTTP-utgående bindningar använder begäran-och svars objekt för att representera HTTP-meddelanden.

#### <a name="request-object"></a>Begär ande objekt

Objektet för begäran som skickades till skriptet är av typen `HttpRequestContext`, som har följande egenskaper:

| Egenskap  | Description                                                    | type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Ett objekt som innehåller bröd texten i begäran. `Body` serialiseras till den bästa typen baserat på data. Om data till exempel är JSON, skickas de som en hash-tabell. Om datan är en sträng skickas den som en sträng. | object |
| **`Headers`** | En ord lista som innehåller begärandehuvuden.                | Ord lista < sträng, String ><sup>*</sup> |
| **`Method`** | HTTP-metoden för begäran.                                | sträng                    |
| **`Params`**  | Ett objekt som innehåller Dirigerings parametrarna för begäran. | Ord lista < sträng, String ><sup>*</sup> |
| **`Query`** | Ett objekt som innehåller frågeparametrarna.                  | Ord lista < sträng, String ><sup>*</sup> |
| **`Url`** | URL för begäran.                                        | sträng                    |

<sup>*</sup> Alla `Dictionary<string,string>`-nycklar är Skift läges känsliga.

#### <a name="response-object"></a>Svarsobjekt

Objektet Response som du ska skicka tillbaka är av typen `HttpResponseContext`, som har följande egenskaper:

| Egenskap      | Description                                                 | type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Ett objekt som innehåller bröd texten i svaret.           | object                    |
| **`ContentType`** | En kort hand för att ange innehålls typen för svaret. | sträng                    |
| **`Headers`** | Ett objekt som innehåller svarshuvuden.               | Ord lista eller hash   |
| **`StatusCode`**  | HTTP-statuskod för svaret.                       | sträng eller heltal             |

#### <a name="accessing-the-request-and-response"></a>Åtkomst till begäran och svar

När du arbetar med HTTP-utlösare kan du komma åt HTTP-begäran på samma sätt som med andra inkommande bindningar. Det är i `param`-blocket.

Använd ett `HttpResponseContext`-objekt för att returnera ett svar, som du ser i följande avsnitt:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Resultatet av att anropa den här funktionen är:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Typ-databyte för utlösare och bindningar

För vissa bindningar som BLOB-bindningen kan du ange typ av parameter.

Om du till exempel vill ha data från Blob Storage som anges som en sträng lägger du till följande typ konvertering till mitt `param`-block:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profil

I PowerShell är begreppet en PowerShell-profil. Om du inte är bekant med PowerShell-profiler, se [om profiler](/powershell/module/microsoft.powershell.core/about/about_profiles).

I PowerShell-funktioner körs profil skriptet när funktions programmet startar. Function-appar startar när de först distribueras och efter att de varit inaktiva ([kall start](#cold-start)).

När du skapar en Function-app med hjälp av verktyg, till exempel Visual Studio Code och Azure Functions Core Tools, skapas en standard `profile.ps1` åt dig. Standard profilen underhålls [av GitHub-lagringsplatsen Core tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) och innehåller:

* Automatisk MSI-autentisering till Azure.
* Möjligheten att aktivera Azure PowerShell `AzureRM` PowerShell-alias om du vill.

## <a name="powershell-version"></a>PowerShell-version

I följande tabell visas den PowerShell-version som används av varje huvud version av Functions-körningen:

| Funktions version | PowerShell-version                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5,1 (låst av körnings miljön) |
| 2x               | PowerShell Core 6                              |

Du kan se den aktuella versionen genom att skriva ut `$PSVersionTable` från vilken funktion som helst.

## <a name="dependency-management"></a>Beroendehantering

PowerShell Functions har stöd för att ladda ned och hantera [PowerShell Gallery](https://www.powershellgallery.com) -moduler av tjänsten. Genom att ändra Host. JSON och ange egenskapen managedDependency enabled till True kommer filen krav. psd1 att bearbetas. De angivna modulerna hämtas automatiskt och görs tillgängliga för funktionen. 

Det maximala antalet moduler som stöds för närvarande är 10. Den syntax som stöds är MajorNumber. * eller den exakta modul versionen som visas nedan. Azure AZ-modulen ingår som standard när en ny PowerShell Function-app skapas.

Språk arbetaren hämtar alla uppdaterade moduler vid en omstart.

host.json
```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Följande inställningar är tillgängliga för att ändra hur de hanterade beroendena hämtas och installeras. Din app-uppgradering kommer att starta inom MDMaxBackgroundUpgradePeriod och uppgraderings processen kommer att slutföras inom cirka MDNewSnapshotCheckPeriod.

| Funktionsapp inställning              | Standardvärde             | Beskrivning                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| MDMaxBackgroundUpgradePeriod      | "7.00:00:00" (7 dagar)     | Varje PS-anställd initierar sökning efter modul uppgraderingar i PS-galleriet om arbets process start och varje MDMaxBackgroundUpgradePeriod efter det. Om nya versioner av modulen är tillgängliga i PS-galleriet, kommer de att installeras till fil systemet som är tillgängligt för PS-arbetare. Om du minskar det här värdet kan din Function-app Hämta nyare modul versioner tidigare, men kommer även att öka resursanvändningen för appar (nätverks-I/O, CPU, lagring). Om du ökar det här värdet minskas användningen av resurs resursen, men det kan också dröja att leverera nya modul versioner till din app.      | 
| MDNewSnapshotCheckPeriod          | "01:00:00" (1 timme)       | Varje PS Worker måste startas om efter att de nya modulerna har installerats i fil systemet. Att starta om PS Worker kan påverka appens tillgänglighet eftersom det kan avbryta aktuella funktions anrop. Till dess att alla PS-arbetstagarna har startats om kan funktions anrop använda antingen den gamla versionen eller den nya versionen av modulen. Omstart av alla PS-arbetare kommer att slutföras inom MDNewSnapshotCheckPeriod. Om du ökar det här värdet minskas frekvensen för avbrott, men det kan också öka tids perioden när funktions anropen använder antingen den gamla eller den nya modulens versioner icke-deterministiskt. |
| MDMinBackgroundUpgradePeriod      | "1,00:00:00" (1 dag)     | För att undvika alltför stora modul uppgraderingar vid frekventa arbets starter, utförs ingen sökning efter modul uppgraderingar om en anställd redan har initierat den senaste MDMinBackgroundUpgradePeriod. |

> [!NOTE]
> Hanterade beroenden är beroende av åtkomst till www.powershellgallery.com för att hämta moduler. Du måste se till att funktions körningen har åtkomst till denna URL genom att lägga till eventuella nödvändiga brand Väggs regler.

Att dra nytta av dina egna anpassade moduler är lite annorlunda än hur du gör det normalt.

När du installerar modulen på den lokala datorn går den till någon av de globalt tillgängliga mapparna i `$env:PSModulePath`. Eftersom din funktion körs i Azure har du inte åtkomst till de moduler som är installerade på datorn. Detta kräver att `$env:PSModulePath` för en PowerShell Function-app skiljer sig från `$env:PSModulePath` i ett vanligt PowerShell-skript.

I functions innehåller `PSModulePath` två sökvägar:

* En `Modules`-mapp som finns i roten på din Funktionsapp.
* En sökväg till en `Modules`-mapp som finns i PowerShell-språket.

### <a name="function-app-level-modules-folder"></a>Mappens funktion på App-nivå `Modules`

Om du vill använda anpassade moduler kan du placera moduler som dina funktioner är beroende av i en `Modules`-mapp. I den här mappen är moduler automatiskt tillgängliga för functions-körningen. Alla funktioner i Function-appen kan använda dessa moduler. 

> [!NOTE]
> Moduler som anges i filen Requirements. psd1 laddas ned automatiskt och inkluderas i sökvägen så att du inte behöver ta med dem i mappen moduler. Dessa lagras lokalt i mappen $env: LOCALAPPDATA/AzureFunctions och i mappen/data/ManagedDependencies när den körs i molnet.

Om du vill dra nytta av funktionen för anpassad modul skapar du en `Modules`-mapp i roten för din Function-app. Kopiera modulerna som du vill använda i dina funktioner till den här platsen.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

I en modules-mapp ska din Function-app ha följande mappstruktur:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

När du startar en Function-app lägger PowerShell-språket till den här `Modules`-mappen i `$env:PSModulePath` så att du kan förlita dig på att modulen laddas automatiskt på samma sätt som i ett vanligt PowerShell-skript.

### <a name="language-worker-level-modules-folder"></a>Språk arbets nivå `Modules` mapp

Flera moduler används ofta av PowerShell-språket. Dessa moduler definieras i den sista positionen i `PSModulePath`. 

Den aktuella listan över moduler är följande:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modulen används för att arbeta med Arkiv, t. ex. `.zip`, `.nupkg` och andra.
* **ThreadJob**: En tråd baserad implementering av PowerShell-jobbets API: er.

Den senaste versionen av dessa moduler används av functions. Om du vill använda en speciell version av dessa moduler kan du ange den aktuella versionen i mappen `Modules` i din Function-app.

## <a name="environment-variables"></a>Miljövariabler

I funktioner visas [appinställningar](functions-app-settings.md), till exempel tjänst anslutnings strängar, som miljövariabler under körningen. Du kan komma åt de här inställningarna med hjälp av `$env:NAME_OF_ENV_VAR`, som du ser i följande exempel:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

När du kör lokalt läses AppData från projekt filen [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="concurrency"></a>Samtidighet

Som standard kan funktioner i PowerShell-körningsmiljön endast bearbeta ett anrop till en funktion i taget. Den samtidiga nivån kanske inte är tillräcklig i följande situationer:

* När du försöker hantera ett stort antal anrop samtidigt.
* När du har funktioner som anropar andra funktioner i samma Function-app.

Du kan ändra det här beteendet genom att ställa in följande miljö variabel till ett heltals värde:

```
PSWorkerInProcConcurrencyUpperBound
```

Du ställer in miljövariabeln i [appens inställningar](functions-app-settings.md) för Funktionsapp.

### <a name="considerations-for-using-concurrency"></a>Överväganden vid användning av samtidighet

PowerShell är ett _enda trådat_ skript språk som standard. Samtidighet kan dock läggas till med hjälp av flera PowerShell-körnings utrymmen i samma process. Mängden körnings utrymmen som skapas kommer att matcha inställningen för PSWorkerInProcConcurrencyUpperBound-programmet. Data flödet påverkas av mängden processor-och minnes mängd som är tillgängliga i den valda planen.

Azure PowerShell använder vissa kontexter och tillstånd på _processnivå_ för att hjälpa till att spara. Men om du aktiverar samtidighet i din Function-app och anropar åtgärder som ändrar tillstånd, kan du få licens villkoren. Dessa tävlings förhållanden är svåra att felsöka eftersom ett anrop är beroende av ett visst tillstånd och det andra anropet har ändrat tillståndet.

Det finns stor värde i samtidighet med Azure PowerShell eftersom vissa åtgärder kan ta lång tid. Du måste dock gå vidare med försiktighet. Om du misstänker att du råkar ut för ett konkurrens villkor ställer du in PSWorkerInProcConcurrencyUpperBound-appen på `1` och använder i stället [språket arbets process nivå isolering](functions-app-settings.md#functions_worker_process_count) för samtidighet.

## <a name="configure-function-scriptfile"></a>Konfigurera funktion `scriptFile`

Som standard körs en PowerShell-funktion från `run.ps1`, en fil som delar samma överordnade katalog som dess motsvarande `function.json`.

Egenskapen `scriptFile` i `function.json` kan användas för att hämta en mappstruktur som ser ut som i följande exempel:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

I det här fallet innehåller `function.json` för `myFunction`-egenskapen en `scriptFile`-egenskap som refererar till filen med den exporterade funktionen som ska köras.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Använd PowerShell-moduler genom att konfigurera en entryPoint

Den här artikeln har visat PowerShell-funktioner i standard skript filen `run.ps1` som skapats av mallarna.
Du kan dock även inkludera dina funktioner i PowerShell-moduler. Du kan referera till din speciella funktions kod i modulen med hjälp av fälten `scriptFile` och `entryPoint` i konfigurations filen function. JSON.

I det här fallet `entryPoint` är namnet på en funktion eller cmdlet i PowerShell-modulen som refereras i `scriptFile`.

Tänk på följande mappstruktur:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Där `PSFunction.psm1` innehåller:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

I det här exemplet innehåller konfigurationen för `myFunction` en `scriptFile`-egenskap som refererar till `PSFunction.psm1`, som är en PowerShell-modul i en annan mapp.  Egenskapen `entryPoint` refererar till funktionen `Invoke-PSTestFunc`, som är start punkten i modulen.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Med den här konfigurationen körs `Invoke-PSTestFunc` exakt som en `run.ps1` skulle.

## <a name="considerations-for-powershell-functions"></a>Att tänka på för PowerShell-funktioner

När du arbetar med PowerShell-funktioner bör du vara medveten om överväganden i följande avsnitt.

### <a name="cold-start"></a>Kall start

När du utvecklar Azure Functions i den [serverbaserade värd modellen](functions-scale.md#consumption-plan)är kall start en verklighet. *Kall start* avser den tid det tar för din Function-app att börja köra för att bearbeta en begäran. Kall start sker oftare i förbruknings planen eftersom din funktions app stängs av under perioder av inaktivitet.

### <a name="bundle-modules-instead-of-using-install-module"></a>Paketera moduler i stället för att använda `Install-Module`

Skriptet körs vid varje anrop. Undvik att använda `Install-Module` i skriptet. Använd i stället `Save-Module` innan du publicerar så att din funktion inte behöver slösa tid åt att ladda ned modulen. Om kall startar påverkar dina funktioner, bör du överväga att distribuera din Function-app till en [App Service plan](functions-scale.md#app-service-plan) inställd på *Always on* eller till en [Premium-plan](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)

[Host. JSON-referens]: functions-host-json.md
