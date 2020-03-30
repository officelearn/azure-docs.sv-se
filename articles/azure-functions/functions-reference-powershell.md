---
title: PowerShell-utvecklarreferens för Azure-funktioner
description: Förstå hur du utvecklar funktioner med hjälp av PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276743"
---
# <a name="azure-functions-powershell-developer-guide"></a>Utvecklarhandboken för Azure Functions PowerShell

Den här artikeln innehåller information om hur du skriver Azure-funktioner med PowerShell.

En PowerShell Azure-funktion (funktion) representeras som ett PowerShell-skript som körs när det utlöses. Varje funktionsskript `function.json` har en relaterad fil som definierar hur funktionen fungerar, till exempel hur den utlöses och dess in- och utdataparametrar. Mer information finns i [artikeln Utlösare och bindning](functions-triggers-bindings.md). 

Precis som andra typer av funktioner tar PowerShell-skriptfunktioner in parametrar som `function.json` matchar namnen på alla indatabindningar som definierats i filen. En `TriggerMetadata` parameter skickas också som innehåller ytterligare information om utlösaren som startade funktionen.

Den här artikeln förutsätter att du redan har läst [Utvecklarreferensen för Azure Functions](functions-reference.md). Du bör också ha slutfört [snabbstarten Funktioner för PowerShell för](functions-create-first-function-powershell.md) att skapa din första PowerShell-funktion.

## <a name="folder-structure"></a>Mappstrukturen

Den mappstruktur som krävs för ett PowerShell-projekt ser ut som följande. Den här standardinställningen kan ändras. Mer information finns i [avsnittet scriptFile](#configure-function-scriptfile) nedan.

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

I projektets rot finns en delad [`host.json`](functions-host-json.md) fil som kan användas för att konfigurera funktionsappen. Varje funktion har en mapp med en egen kodfil (.ps1) och bindningskonfigurationsfil (`function.json`). Namnet på filen function.json-filens överordnade katalog är alltid namnet på din funktion.

Vissa bindningar kräver att `extensions.csproj` en fil finns. Bindningstillägg, som krävs i [version 2.x och senare](functions-versions.md) versioner `extensions.csproj` av functions-körningen, `bin` definieras i filen med de faktiska biblioteksfilerna i mappen. När du utvecklar lokalt måste du [registrera bindningstillägg.](functions-bindings-register.md#extension-bundles) När du utvecklar funktioner i Azure-portalen görs den här registreringen åt dig.

I PowerShell-funktionsappar kan `profile.ps1` du eventuellt ha en som körs när en funktionsapp börjar köras (annars känd som en *[kallstart](#cold-start)*. Mer information finns i [PowerShell-profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definiera ett PowerShell-skript som en funktion

Som standard söker körtiden Funktioner efter `run.ps1`din `run.ps1` funktion i , där `function.json`samma överordnade katalog som motsvarande .

Skriptet skickas ett antal argument vid körning. Om du vill hantera `param` dessa parametrar lägger du till ett block överst i skriptet som i följande exempel:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametern TriggerMetadata

Parametern `TriggerMetadata` används för att ange ytterligare information om utlösaren. De ytterligare metadata varierar från bindning `sys` till bindning, men alla innehåller en egenskap som innehåller följande data:

```powershell
$TriggerMetadata.sys
```

| Egenskap   | Beskrivning                                     | Typ     |
|------------|-------------------------------------------------|----------|
| UtcNow (olika år)     | När funktionen i UTC utlöstes        | DateTime |
| Metodnamn | Namnet på funktionen som utlöstes     | sträng   |
| RandGuid (randguid)   | en unik guid till denna körning av funktionen | sträng   |

Varje utlösartyp har en annan uppsättning metadata. Till exempel `$TriggerMetadata` innehåller `QueueTrigger` for `InsertionTime`bland `Id` `DequeueCount`annat , , . Mer information om köutlösarens metadata finns i den [officiella dokumentationen för köutlösare](functions-bindings-storage-queue-trigger.md#message-metadata). Kontrollera dokumentationen på [utlösare](functions-triggers-bindings.md) du arbetar med för att se vad som kommer inuti utlösarmetadata.

## <a name="bindings"></a>Bindningar

I PowerShell konfigureras och definieras [bindningar](functions-triggers-bindings.md) i en funktions funktion.json. Funktioner interagerar med bindningar på flera olika sätt.

### <a name="reading-trigger-and-input-data"></a>Läsa utlösare och indata

Utlösare och indatabindningar läss som parametrar som skickas till din funktion. Indatabindningar `direction` har `in` en uppsättning till i function.json. Egenskapen `name` som `function.json` definieras i är namnet `param` på parametern i blocket. Eftersom PowerShell använder namngivna parametrar för bindning spelar ordningen på parametrarna ingen roll. Det är dock en bra idé att följa ordningen på `function.json`bindningarna som definieras i .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Skriva utdata

I Functions har en `direction` utdatabindning en uppsättning till `out` i function.json. Du kan skriva till en `Push-OutputBinding` utdatabindning med hjälp av cmdlet, som är tillgänglig för funktioner runtime. I samtliga fall `name` motsvarar egenskapen för `function.json` bindningen `Name` enligt definitionen `Push-OutputBinding` i cmdletens parameter.

Följande visar hur `Push-OutputBinding` du anropar i funktionsskriptet:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Du kan också skicka in ett värde för en viss bindning via pipelinen.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`fungerar annorlunda baserat på det värde `-Name`som angetts för:

* När det angivna namnet inte kan matchas till en giltig utdatabindning genereras ett fel.

* När utdatabindningen accepterar en samling `Push-OutputBinding` värden kan du upprepade gånger anropa för att skicka flera värden.

* När utdatabindningen bara accepterar `Push-OutputBinding` ett singleton-värde, ger ett fel om du anropar en andra gång.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`Syntax

Följande är giltiga parametrar `Push-OutputBinding`för samtal:

| Namn | Typ | Position | Beskrivning |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | Namnet på den utdatabindning som du vill ange. |
| **`-Value`** | Objekt | 2 | Värdet för den utdatabindning som du vill ange, som accepteras från pipeline byvalue. |
| **`-Clobber`** | SwitchParameter | Heter | (Valfritt) När det anges tvingar du det värde som ska ställas in för en angiven utdatabindning. | 

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

Mer information finns i [Om CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Exempel på Push-OutputBinding: HTTP-svar

En HTTP-utlösare returnerar ett `response`svar med hjälp av en utdatabindning med namnet . I följande exempel har utdatabindningen av `response` värdet "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Eftersom utdata endast är till HTTP, som endast accepterar `Push-OutputBinding` ett singleton-värde, genereras ett fel när det anropas en andra gång.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

För utdata som bara accepterar singleton-värden kan du använda `-Clobber` parametern för att åsidosätta det gamla värdet i stället för att försöka lägga till i en samling. I följande exempel förutsätts att du redan har lagt till ett värde. Genom `-Clobber`att använda åsidosätter svaret från följande exempel det befintliga värdet för att returnera ett värde av "utdata #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Exempel på push-outputBinding: Köutdatabindning

`Push-OutputBinding`används för att skicka data till utdatabindningar, till exempel en [Azure Queue storage-utdatabindning](functions-bindings-storage-queue-output.md). I följande exempel har meddelandet som skrivs till kön värdet "utdata #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Utdatabindningen för en lagringskö accepterar flera utdatavärden. I det här fallet anropar du följande exempel efter den första skriver till kön en lista med två objekt: "utdata #1" och "utdata #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

I följande exempel, när det anropas efter de två föregående, läggs ytterligare två värden till i utdatasamlingen:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

När meddelandet skrivs till kön innehåller det följande fyra värdena: "output #1", "output #2", "output #3" och "output #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`Cmdlet

Du kan `Get-OutputBinding` använda cmdlet för att hämta de värden som för närvarande är inställda för utdatabindningar. Den här cmdlet hämtar en hashtable som innehåller namnen på utdatabindningarna med sina respektive värden. 

Följande är ett exempel `Get-OutputBinding` på hur du använder för att returnera aktuella bindningsvärden:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`innehåller också en `-Name`parameter som kallas , som kan användas för att filtrera den returnerade bindningen, som i följande exempel:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Jokertecken (*) stöds `Get-OutputBinding`i .

## <a name="logging"></a>Loggning

Loggning i PowerShell-funktioner fungerar som vanlig PowerShell-loggning. Du kan använda loggnings cmdlets för att skriva till varje utdataström. Varje cmdlet mappar till en loggnivå som används av Functions.

| Loggningsnivå för funktioner | Loggning cmdlet |
| ------------- | -------------- |
| Fel | **`Write-Error`** |
| Varning | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Skriver till loggning på _informationsnivå._ |
| Felsökning | **`Write-Debug`** |
| Spårning | **`Write-Progress`** <br /> **`Write-Verbose`** |

Utöver dessa cmdlets omdirigeras allt som skrivs `Information` till pipelinen till loggnivån och visas med standard PowerShell-formateringen.

> [!IMPORTANT]
> Det `Write-Verbose` räcker `Write-Debug` inte att använda eller cmdlets för att se detaljerad och felsökningsnivåloggning. Du måste också konfigurera tröskelvärdet för loggnivå, som deklarerar vilken nivå av loggar du faktiskt bryr dig om. Mer information finns [i Konfigurera funktionsapploggnivån](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurera funktionsapploggnivån

Med Azure Functions kan du definiera tröskelnivån för att göra det enkelt att styra hur Functions skriver till loggarna. Om du vill ange tröskelvärdet för alla `logging.logLevel.default` spår som skrivits till konsolen använder du egenskapen i [ `host.json` filen][host.json-referensen]. Den här inställningen gäller för alla funktioner i funktionsappen.

I följande exempel anges tröskelvärdet för att aktivera utförlig loggning för alla funktioner, `MyFunction`men tröskelvärdet för felsökningsloggning för en funktion med namnet :

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

Mer information finns i [host.json reference].

### <a name="viewing-the-logs"></a>Visa loggarna

Om din funktionsapp körs i Azure kan du använda Application Insights för att övervaka den. Läs [övervakning av Azure Functions](functions-monitoring.md) om du vill veta mer om att visa och fråga funktionsloggar.

Om du kör din funktionsapp lokalt för utveckling loggar du som standard till filsystemet. Om du vill se loggarna `AZURE_FUNCTIONS_ENVIRONMENT` i konsolen `Development` ställer du in miljövariabeln till innan funktionsappen startas.

## <a name="triggers-and-bindings-types"></a>Triggers och bindningar typer

Det finns ett antal utlösare och bindningar tillgängliga för dig att använda med din funktionsapp. Den fullständiga listan över triggers och bindningar [hittar du här](functions-triggers-bindings.md#supported-bindings).

Alla utlösare och bindningar representeras i kod som några verkliga datatyper:

* Hashtable
* sträng
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

De första fem typerna i den här listan är standardtyper för .NET. De två sista används endast av [HttpTrigger-utlösaren](#http-triggers-and-bindings).

Varje bindningsparameter i dina funktioner måste vara en av dessa typer.

### <a name="http-triggers-and-bindings"></a>HTTP-utlösare och bindningar

HTTP- och webhook-utlösare och HTTP-utdatabindningar använder begäran och svarsobjekt för att representera HTTP-meddelanden.

#### <a name="request-object"></a>Begär objekt

Det begärandeobjekt som skickas till skriptet `HttpRequestContext`är av typen , som har följande egenskaper:

| Egenskap  | Beskrivning                                                    | Typ                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Ett objekt som innehåller brödtexten för begäran. `Body`serialiseras till den bästa typen baserat på data. Om data till exempel är JSON skickas de in som en hashtable. Om data är en sträng skickas de in som en sträng. | objekt |
| **`Headers`** | En ordlista som innehåller begäranden.                | Ordlista<sträng,sträng><sup>*</sup> |
| **`Method`** | HTTP-metoden för begäran.                                | sträng                    |
| **`Params`**  | Ett objekt som innehåller routningsparametrarna för begäran. | Ordlista<sträng,sträng><sup>*</sup> |
| **`Query`** | Ett objekt som innehåller frågeparametrarna.                  | Ordlista<sträng,sträng><sup>*</sup> |
| **`Url`** | Url:en för begäran.                                        | sträng                    |

<sup>*</sup>Alla `Dictionary<string,string>` nycklar är skiftlägeskänsliga.

#### <a name="response-object"></a>Svarsobjekt

Svarsobjektet som du bör skicka `HttpResponseContext`tillbaka är av typen , som har följande egenskaper:

| Egenskap      | Beskrivning                                                 | Typ                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Ett objekt som innehåller svarets brödtext.           | objekt                    |
| **`ContentType`** | En kort hand för att ställa in innehållstypen för svaret. | sträng                    |
| **`Headers`** | Ett objekt som innehåller svarsrubrikerna.               | Ordlista eller haschtabell   |
| **`StatusCode`**  | HTTP-statuskoden för svaret.                       | sträng eller int             |

#### <a name="accessing-the-request-and-response"></a>Komma åt begäran och svar

När du arbetar med HTTP-utlösare kan du komma åt HTTP-begäran på samma sätt som med någon annan indatabindning. Den är i `param` kvarteret.

Använd `HttpResponseContext` ett objekt för att returnera ett svar, som visas i följande:

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

Resultatet av att anropa den här funktionen skulle bli:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Typgjutning för utlösare och bindningar

För vissa bindningar som blob-bindningen kan du ange typen av parameter.

Om du till exempel vill att data från Blob-lagring ska `param` levereras som en sträng lägger du till följande typ i mitt block:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profil

I PowerShell finns konceptet med en PowerShell-profil. Om du inte är bekant med PowerShell-profiler läser du [Om profiler](/powershell/module/microsoft.powershell.core/about/about_profiles).

I PowerShell-funktioner körs profilskriptet när funktionsappen startar. Funktionsappar startar när de först distribueras och efter att ha blivit inaktiva ([kallstart](#cold-start)).

När du skapar en funktionsapp med verktyg, till exempel Visual `profile.ps1` Studio Code och Azure Functions Core Tools, skapas en standard för dig. Standardprofilen behålls [i GitHub-databasen för Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) och innehåller:

* Automatisk MSI-autentisering till Azure.
* Möjligheten att aktivera Azure `AzureRM` PowerShell PowerShell-alias om du vill.

## <a name="powershell-version"></a>PowerShell-version

I följande tabell visas den PowerShell-version som används av varje huvudversion av funktionskörningen:

| Version av funktioner | PowerShell-version                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (låst av körningen) |
| 2.x               | PowerShell Kärna 6                              |

Du kan se den `$PSVersionTable` aktuella versionen genom att skriva ut från valfri funktion.

## <a name="dependency-management"></a>Beroendehantering

Med funktioner kan du använda [PowerShell-galleriet](https://www.powershellgallery.com) för att hantera beroenden. När beroendehantering är aktiverat används filen requirements.psd1 för att automatiskt hämta nödvändiga moduler. Du aktiverar det här `managedDependency` beteendet genom att ange egenskapen till i roten till `true` filen [host.json](functions-host-json.md), som i följande exempel:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

När du skapar ett nytt PowerShell-funktionsprojekt aktiveras beroendehantering som standard, med [ `Az` Azure-modulen](/powershell/azure/new-azureps-module-az) inkluderad. Det maximala antalet moduler som för närvarande stöds är 10. Syntaxen som _`MajorNumber`_ `.*` stöds är eller exakt modulversion som visas i följande requirements.psd1-exempel:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

När du uppdaterar filen requirements.psd1 installeras uppdaterade moduler efter en omstart.

> [!NOTE]
> Hanterade beroenden kräver åtkomst till www.powershellgallery.com för att hämta moduler. När du kör lokalt kontrollerar du att körningen kan komma åt den här URL:en genom att lägga till nödvändiga brandväggsregler. 

Följande programinställningar kan användas för att ändra hur hanterade beroenden hämtas och installeras. Appuppgraderingen `MDMaxBackgroundUpgradePeriod`startar inom och uppgraderingsprocessen slutförs `MDNewSnapshotCheckPeriod`inom ungefär .

| Inställning av funktionsapp              | Standardvärde             | Beskrivning                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`(7 dagar)     | Varje PowerShell-arbetsprocess inleder sökandet efter moduluppgraderingar `MDMaxBackgroundUpgradePeriod` i PowerShell-galleriet vid processstart och varje efter det. När en ny modulversion är tillgänglig i PowerShell-galleriet installeras den i filsystemet och görs tillgänglig för PowerShell-arbetare. Om du minskar det här värdet kan funktionsappen få nyare modulversioner tidigare, men det ökar också appresursanvändningen (nätverks-I/O, CPU, lagring). Om du ökar det här värdet minskar appens resursanvändning, men det kan också fördröja leveransen av nya modulversioner till din app. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`(1 timme)       | När nya modulversioner har installerats i filsystemet måste varje PowerShell-arbetsprocess startas om. Om du startar om PowerShell-arbetare påverkas appens tillgänglighet eftersom den kan avbryta den aktuella funktionskörningen. Tills alla PowerShell-arbetsprocesser startas om kan funktionsanrop använda antingen de gamla eller de nya modulversionerna. Starta om alla PowerShell-arbetare som slutförs inom `MDNewSnapshotCheckPeriod`. Om du ökar detta värde minskar frekvensen av avbrott, men kan också öka tidsperioden när funktionstarop använder antingen de gamla eller de nya modulversionerna icke-deterministiskt. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`(1 dag)     | För att undvika överdrivna moduluppgraderingar vid frekventa omstarter av arbetare utförs inte sökandet `MDMinBackgroundUpgradePeriod`efter moduluppgraderingar när någon arbetare redan har initierat den kontrollen i den senaste . |

Utnyttja dina egna anpassade moduler är lite annorlunda än hur du skulle göra det normalt.

På den lokala datorn installeras modulen i en av de `$env:PSModulePath`globalt tillgängliga mapparna i . När du kör i Azure har du inte åtkomst till de moduler som är installerade på din dator. Det innebär `$env:PSModulePath` att för en PowerShell-funktionsapp skiljer sig från `$env:PSModulePath` i ett vanligt PowerShell-skript.

I Functions `PSModulePath` innehåller två sökvägar:

* En `Modules` mapp som finns i roten till funktionsappen.
* En sökväg `Modules` till en mapp som styrs av språkarbetaren PowerShell.

### <a name="function-app-level-modules-folder"></a>Funktionsmapp `Modules` på appnivå

Om du vill använda anpassade moduler kan du placera `Modules` moduler som dina funktioner är beroende av i en mapp. Från den här mappen är moduler automatiskt tillgängliga för funktionernas körning. Alla funktioner i funktionsappen kan använda dessa moduler. 

> [!NOTE]
> Moduler som anges i filen requirements.psd1 hämtas automatiskt och inkluderas i sökvägen så att du inte behöver inkludera dem i modulens mapp. Dessa lagras lokalt i `$env:LOCALAPPDATA/AzureFunctions` mappen och `/data/ManagedDependencies` i mappen när de körs i molnet.

Om du vill dra nytta av `Modules` funktionen för anpassade moduler skapar du en mapp i roten på funktionsappen. Kopiera de moduler som du vill använda i dina funktioner till den här platsen.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Med `Modules` en mapp bör funktionsappen ha följande mappstruktur:

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

När du startar funktionsappen lägger `Modules` PowerShell-språkarbetaren till den `$env:PSModulePath` här mappen så att du kan lita på att modulen laddas automatiskt på samma sätt som i ett vanligt PowerShell-skript.

### <a name="language-worker-level-modules-folder"></a>Mapp på `Modules` språkarbetsnivå

Flera moduler används ofta av Språkarbetaren PowerShell. Dessa moduler definieras i den `PSModulePath`sista positionen i . 

Den aktuella listan över moduler är följande:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modul som används `.zip` `.nupkg`för att arbeta med arkiv, till exempel , och andra.
* **ThreadJob**: En trådbaserad implementering av PowerShell-jobb-API:erna.

Som standard använder Functions den senaste versionen av dessa moduler. Om du vill använda en viss modulversion placerar du den specifika versionen i mappen i funktionsappen. `Modules`

## <a name="environment-variables"></a>Miljövariabler

I Funktioner visas [appinställningar](functions-app-settings.md), till exempel tjänstanslutningssträngar, som miljövariabler under körningen. Du kan komma `$env:NAME_OF_ENV_VAR`åt dessa inställningar med , som visas i följande exempel:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

När du kör lokalt läses appinställningarna från projektfilen [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="concurrency"></a>Samtidighet

Som standard kan Funktions powershell-körningen bara bearbeta en anrop av en funktion åt gången. Denna samtidighetsnivå kanske inte är tillräcklig i följande situationer:

* När du försöker hantera ett stort antal åkallor samtidigt.
* När du har funktioner som anropar andra funktioner i samma funktionsapp.

Du kan ändra det här beteendet genom att ange följande miljövariabel till ett heltalsvärde:

```
PSWorkerInProcConcurrencyUpperBound
```

Du anger den här miljövariabeln i [appinställningarna](functions-app-settings.md) för din funktionsapp.

### <a name="considerations-for-using-concurrency"></a>Överväganden för användning av samtidighet

PowerShell är som standard ett _enda skriptspråk._ Samtidighet kan dock läggas till med hjälp av flera PowerShell-runspaces i samma process. Mängden körningsområden som skapas matchar programinställningen PSWorkerInProcConcurrencyUpperBound. Dataflödet påverkas av mängden CPU och minne som är tillgängligt i den valda planen.

Azure PowerShell använder vissa kontexter och tillstånd _på processnivå_ för att rädda dig från överdriven maskinskrivning. Men om du aktiverar samtidighet i funktionsappen och anropar åtgärder som ändrar tillstånd kan du få konkurrensvillkor. Dessa ras villkor är svåra att felsöka eftersom en åkallan förlitar sig på ett visst tillstånd och den andra åkallan ändrade staten.

Det finns ett enormt värde i samtidighet med Azure PowerShell, eftersom vissa åtgärder kan ta avsevärd tid. Du måste dock gå försiktigt fram. Om du misstänker att du upplever ett konkurrenstillstånd ställer du in appinställningen PSWorkerInProcConcurrencyUpperBound på `1` och i stället använder isolering av [språkarbetsprocessnivå](functions-app-settings.md#functions_worker_process_count) för samtidighet.

## <a name="configure-function-scriptfile"></a>Konfigurera funktion`scriptFile`

Som standard körs en PowerShell-funktion från `run.ps1`, en fil som `function.json`delar samma överordnade katalog som motsvarande .

Egenskapen `scriptFile` i `function.json` kan användas för att hämta en mappstruktur som ser ut som följande exempel:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

I det här `function.json` `myFunction` fallet `scriptFile` innehåller för en egenskap som refererar till filen med den exporterade funktionen som ska köras.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Använda PowerShell-moduler genom att konfigurera en entryPoint

Den här artikeln har visat `run.ps1` PowerShell-funktioner i standardskriptfilen som genereras av mallarna.
Du kan dock också inkludera dina funktioner i PowerShell-moduler. Du kan referera till din specifika funktionskod i modulen med hjälp av `scriptFile` och `entryPoint` fälten i konfigurationsfilen function.json.

I det `entryPoint` här fallet är namnet på en funktion eller cmdlet i PowerShell-modulen som refereras i `scriptFile`.

Tänk på följande mappstruktur:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Om `PSFunction.psm1` den innehåller

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

I det här exemplet `myFunction` innehåller `scriptFile` konfigurationen `PSFunction.psm1`för en egenskap som refererar till , som är en PowerShell-modul i en annan mapp.  Egenskapen `entryPoint` refererar `Invoke-PSTestFunc` till funktionen, som är startpunkten i modulen.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Med den här `Invoke-PSTestFunc` konfigurationen körs `run.ps1` den precis som en skulle.

## <a name="considerations-for-powershell-functions"></a>Överväganden för PowerShell-funktioner

När du arbetar med PowerShell-funktioner bör du vara medveten om övervägandena i följande avsnitt.

### <a name="cold-start"></a>Kallstart

När du utvecklar Azure Functions i den [serverlösa värdmodellen](functions-scale.md#consumption-plan)är kalla starter verklighet. *Kallstart* refererar till den tid det tar för funktionsappen att börja köras för att bearbeta en begäran. Kallstart sker oftare i förbrukningsplanen eftersom funktionsappen stängs av under perioder av inaktivitet.

### <a name="bundle-modules-instead-of-using-install-module"></a>Buntmoduler istället för att använda`Install-Module`

Skriptet körs på alla anrop. Undvik `Install-Module` att använda i skriptet. Använd `Save-Module` istället innan du publicerar så att din funktion inte behöver slösa tid på att ladda ned modulen. Om kallstart påverkar dina funktioner bör du överväga att distribuera din funktionsapp till en [App Service-plan](functions-scale.md#app-service-plan) som alltid är *inställd på* eller till en [Premium-plan](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)

[Referens för host.json]: functions-host-json.md
