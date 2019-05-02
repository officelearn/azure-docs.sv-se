---
title: PowerShell för utvecklare för Azure Functions
description: Förstå hur du utvecklar funktioner med hjälp av PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: 71ac525e2af7473ca9ce0a8f60268e76eccd1a9a
ms.sourcegitcommit: 111a7b3e19d5515ce7036287cea00a7204ca8b56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64530390"
---
# <a name="azure-functions-powershell-developer-guide"></a>Utvecklarguide för Azure Functions PowerShell

Den här artikeln innehåller information om hur du skriver Azure Functions med hjälp av PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

En PowerShell-funktionen visas som ett PowerShell-skript som körs när den utlöses. Varje funktion skript har en relaterad function.json som definierar hur funktionen fungerar, till exempel hur den utlöses och indata-och utdataparametrar. Mer information finns i den [utlösare och bindningen artikeln](functions-triggers-bindings.md). 

PowerShell-skript tar in-parametrar som matchar namnen på alla indatabindningar som definierats i function.json som andra typer av funktioner. En `TriggerMetadata` parametern skickas också som innehåller ytterligare information för utlösaren som startats funktionen.

Den här artikeln förutsätter att du redan har läst den [Azure Functions för utvecklare](functions-reference.md). Du bör ha slutfört också den [Functions-Snabbstart för PowerShell](functions-create-first-function-powershell.md) att skapa din första PowerShell-funktion.

## <a name="folder-structure"></a>mappstruktur

Det ser ut som följande nödvändiga mappstrukturen för ett PowerShell-projekt. Du kan ändra denna standardinställning. Mer information finns i den [skriptfil](#configure-function-scriptfile) nedan.

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

I roten av projektet, det finns en delad [host.json](functions-host-json.md) -fil som kan användas för att konfigurera funktionsappen. Varje funktion har en mapp med en egen kodfilen (.ps1) och bindningen konfigurationsfil (function.json). Namnet på `function.json`'s överordnad katalog är alltid namnet på din funktion.

Vissa bindningar kräver förekomsten av en `extensions.csproj`. Bindande tillägg som krävs i [version 2.x](functions-versions.md) av Functions-körning har definierats i den `extensions.csproj` -fil med faktiska library-filer i den `bin` mapp. När du utvecklar lokalt, måste du [registrera tillägg av bindning](functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). När du utvecklar funktioner i Azure-portalen görs denna registrering för dig.

I PowerShell-Funktionsappar, kanske du också en `profile.ps1` som körs när en funktionsapp börjar köras (annars känner till som en  *[kallstart](#cold-start)*. Mer information finns i [PowerShell profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definiera ett PowerShell-skript som en funktion

Som standard söker Functions-körning efter din funktion i `run.ps1`, där `run.ps1` delar samma överordnad katalog som dess motsvarande `function.json`.

Skriptet skickas ett antal argument på körning. Om du vill hantera dessa parametrar kan du lägga till en `param` block längst upp i skriptet som i följande exempel:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata parameter

Den `TriggerMetadata` används för att ange ytterligare information om utlösaren. Ytterligare metadata varierar från en bindning till en bindning men de alla innehåller en `sys` egenskap som innehåller följande data:

```powershell
$TriggerMetadata.sys
```

| Egenskap    | Beskrivning                                     | Typ     |
|------------|-------------------------------------------------|----------|
| utcNow     | När, i UTC, funktionen utlöstes        | DateTime |
| Metodnamn | Namnet på den funktion som utlöstes     | string   |
| RandGuid   | ett unikt guid för den här körningen av funktion | string   |

Varje Utlösartyp har en annan uppsättning metadata. Till exempel den `$TriggerMetadata` för `QueueTrigger` innehåller den `InsertionTime`, `Id`, `DequeueCount`, bland annat. Mer information om den köutlösare metadata går du till den [officiella dokumentationen för utlösare](functions-bindings-storage-queue.md#trigger---message-metadata). I dokumentationen på den [utlösare](functions-triggers-bindings.md) du arbetar med för att se vad kommer inuti utlösare metadata.

## <a name="bindings"></a>Bindningar

I PowerShell [bindningar](functions-triggers-bindings.md) konfigureras och definieras i en funktion function.json. Funktioner som interagerar med bindningar flera olika sätt.

### <a name="reading-trigger-and-input-data"></a>Läsa utlösare och indata

Utlösare och bindningar för indata är skrivskyddade som parametrar för din funktion. Indatabindningar har en `direction` inställd `in` i function.json. Den `name` egenskapen som definierats i `function.json` är namnet på parametern i den `param` block. Eftersom PowerShell använder namngivna parametrar för bindning, spelar ordningen för parametrarna. Det är dock en bra idé att följa ordningen på de bindningar som definierats i den `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Skriva utdata

I funktioner, en utdatabindning har en `direction` inställd `out` i function.json. Du kan skriva till en utdatabindning med hjälp av den `Push-OutputBinding` cmdlet, som är tillgänglig för Functions-körning. I samtliga fall den `name` egenskapen för bindningen som definierats i `function.json` motsvarar den `Name` -parametern för den `Push-OutputBinding` cmdlet.

Följande visar hur du anropar `Push-OutputBinding` i funktionen skriptet:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Du kan även skicka in ett värde för en specifik bindning genom pipelinen.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` fungerar på olika sätt beroende på det angivna värdet för `-Name`:

* När det angivna namnet inte kan matchas med en giltig utdatabindning, returneras ett fel.

* När utdatabindningen tar emot en uppsättning värden, kan du anropa `Push-OutputBinding` flera gånger för att skicka flera värden.

* När utdatabindningen accepterar endast en singleton-värde, anropa `Push-OutputBinding` en andra gång genererar ett fel.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Syntax

Följande är giltiga parametrar för att anropa `Push-OutputBinding`:

| Namn | Typ | Position | Beskrivning |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | Namnet på utdatabindningen som du vill ange. |
| **`-Value`** | Object | 2 | Värdet för utdatabindningen du vill ange, som accepteras från pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | med namnet | (Valfritt) När du anger tvingar du värdet som ska anges för en angiven utdatabindning. | 

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

En HTTP-utlösare returnerar ett svar med hjälp av en utdatabindning som heter `response`. I följande exempel utdatabindningen av `response` har värdet för ”utdata #1”:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Eftersom utdatan är till HTTP, som tar emot ett singleton värde, ett fel inträffar när `Push-OutputBinding` anropas en gång.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Du kan använda för utdata som bara godkänna singleton-värden, den `-Clobber` parametern för att åsidosätta det gamla värdet istället för att försöka lägga till i en samling. I följande exempel förutsätter att du redan har lagt till ett värde. Med hjälp av `-Clobber`, svaret från exemplet nedan åsidosätter det befintliga värdet för att returnera ett värde på ”utdata #3”:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding exempel: Meddelandekö utan utdatabindning

`Push-OutputBinding` används för att skicka data till utdatabindningar, till exempel en [Azure Queue storage-utdatabindning](functions-bindings-storage-queue.md#output). I exemplet nedan har värdet ”utdata #1” i det meddelande som skrivits till kön:

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Utdata-bindning för en lagringskö accepterar flera utdatavärden. I det här fallet anropar exemplet nedan efter först skriver till kön en lista med två objekt: ”utdata #1” och ”utdata #2”.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

I följande exempel, när den anropas efter de föregående två läggs två fler värden till samlingen utdata:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

När skrivs till kön meddelandet innehåller dessa fyra värden: ”utdata #1”, ”utdata #2”, ”utdata #3” och ”utdata #4”.

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` Cmdlet:

Du kan använda den `Get-OutputBinding` cmdlet för att hämta de värden som ställts in för utdatabindningar. Denna cmdlet hämtar en hash-tabell som innehåller namnen på utdatabindningar med deras respektive värden. 

Följande är ett exempel på hur du använder `Get-OutputBinding` att returnera bindningsvärden för aktuella:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` innehåller även en parameter med namnet `-Name`, som kan användas för att filtrera returnerade bindningen, som i följande exempel:

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

Loggning i PowerShell-funktioner fungerar som vanliga PowerShell-loggning. Du kan använda cmdlets för loggning för att skriva till varje utdataströmmen. Varje cmdlet mappas till en Loggnivå som används av funktioner.

| Functions loggningsnivå | Loggning cmdlet |
| ------------- | -------------- |
| Fel | **`Write-Error`** |
| Varning | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Skriver till _Information_ på loggning. |
| Felsökning | **`Write-Debug`** |
| Spårning | **`Write-Progress`** <br /> **`Write-Verbose`** |

Förutom dessa cmdlets något skrivs till pipelinen omdirigeras till den `Information` loggen och det visas med PowerShell standardformatering.

> [!IMPORTANT]
> Med hjälp av den `Write-Verbose` eller `Write-Debug` cmdletar inte är tillräckligt för att se utförlig loggning och felsökningsloggning nivå. Du måste också konfigurera log nivå tröskelvärdet deklarerar vilken nivå av loggar som verkligen intresserar dig. Mer information finns i [konfigurerar loggningsnivå för funktionen app](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurera loggningsnivå för funktionen app

Functions kan du definiera tröskelvärdet för att göra det lätt att styra funktionerna sätt skriver till loggarna. Ange tröskelvärdet för alla spårningar som skrivs till konsolen och den `logging.logLevel.default` -egenskapen i den [ `host.json` filen][referens för host.json]. Den här inställningen gäller för alla funktioner i din funktionsapp.

I följande exempel anger tröskelvärdet för att aktivera utförlig loggning för alla funktioner, men anger tröskelvärdet för att aktivera felsökningsloggning en funktion som heter `MyFunction`:

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

Mer information finns i [referens för host.json].

### <a name="viewing-the-logs"></a>Visa loggar

Om din Funktionsapp körs i Azure, kan du kan använda Application Insights för att övervaka den. Läs [övervaka Azure Functions](functions-monitoring.md) vill veta mer om att visa och fråga funktionsloggar.

Om du kör appen lokalt för utveckling, loggar du standard till filsystemet. Om du vill se loggarna i konsolen, ange den `AZURE_FUNCTIONS_ENVIRONMENT` miljövariabeln till `Development` innan du startar appen.

## <a name="triggers-and-bindings-types"></a>Utlösare och bindningar typer

Det finns ett antal utlösare och bindningar som är tillgängliga för dig att använda med din funktionsapp. En fullständig lista över utlösare och bindningar [hittar du här](functions-triggers-bindings.md#supported-bindings).

Alla utlösare och bindningar representeras i koden som några verkliga datatyper:

* Hash-tabell
* string
* byte
* int
* double
* HttpRequestContext
* HttpResponseContext

De första fem typerna i den här listan är standard .NET-typer. De sista två används endast av den [HttpTrigger utlösaren](#http-triggers-and-bindings).

Varje bindningsparametern i dina funktioner måste vara något av de här typerna.

### <a name="http-triggers-and-bindings"></a>HTTP-utlösare och bindningar

HTTP- och webhook-utlösare och HTTP-utdata bindningar använda begäranden och svar-objekt som representerar den HTTP-meddelanden.

#### <a name="request-object"></a>Objekt

Det objekt som skickas till skriptet är av typen `HttpRequestContext`, som har följande egenskaper:

| Egenskap   | Beskrivning                                                    | Typ                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Ett objekt som innehåller brödtext för begäran. `Body` serialiseras till den bästa typen baserat på data. Till exempel om data är JSON, skickas den som en hash-tabell. Om data är en sträng, skickas den i som en sträng. | objekt |
| **`Headers`** | En ordlista som innehåller de begärda rubrikerna.                | Dictionary < sträng, sträng ><sup>*</sup> |
| **`Method`** | HTTP-metod för begäran.                                | string                    |
| **`Params`**  | Ett objekt som innehåller parametrarna routning av begäran. | Dictionary < sträng, sträng ><sup>*</sup> |
| **`Query`** | Ett objekt som innehåller frågeparametrarna.                  | Dictionary < sträng, sträng ><sup>*</sup> |
| **`Url`** | URL för begäran.                                        | string                    |

<sup>*</sup> Alla `Dictionary<string,string>` nycklar är skiftlägeskänsliga.

#### <a name="response-object"></a>Svarsobjekt

Svarsobjekt som du ska skicka tillbaka är av typen `HttpResponseContext`, som har följande egenskaper:

| Egenskap       | Beskrivning                                                 | Typ                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Ett objekt som innehåller brödtexten i svaret.           | objekt                    |
| **`ContentType`** | En kort hand för att ställa in innehållstyp för svar. | string                    |
| **`Headers`** | Ett objekt som innehåller svarshuvuden.               | Ordlista eller hash-tabell   |
| **`StatusCode`**  | HTTP-statuskod i svaret.                       | sträng eller int             |

#### <a name="accessing-the-request-and-response"></a>Åtkomst till begäranden och svar

När du arbetar med HTTP-utlösare kan komma du åt HTTP-begäran på samma sätt som med andra indatabindning. Det är den `param` block.

Använd en `HttpResponseContext` objektet för att returnera ett svar, enligt följande:

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

### <a name="type-casting-for-triggers-and-bindings"></a>Typ-omvandling för utlösare och bindningar

För vissa bindningar som blob-bindningen, du kan ange vilken typ av parametern.

Till exempel om du vill att data från Blob storage som angetts som en sträng, lägger du till följande typ till min `param` block:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profil

I PowerShell finns konceptet med en PowerShell-profil. Om du inte är bekant med PowerShell-profiler finns i [om profiler](/powershell/module/microsoft.powershell.core/about/about_profiles).

I PowerShell-funktioner kör skriptet profil när appen startar. Funktionsappar startar när de först distribueras och efter som inaktiv ([kallstart](#cold-start)).

När du skapar en funktionsapp med hjälp av verktyg som Visual Studio Code och Azure Functions Core Tools, en standard `profile.ps1` skapas åt dig. Standardprofilen underhålls [på Core Tools GitHub-lagringsplatsen](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) och innehåller:

* Automatisk MSI-autentisering till Azure.
* Möjligheten att aktivera Azure PowerShell `AzureRM` PowerShell alias om du vill ha.

## <a name="powershell-version"></a>PowerShell-version

I följande tabell visas de PowerShell-version som används av varje huvudversion av Functions-körning:

| Functions-version | PowerShell-version                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (låst av körningen) |
| 2.x               | PowerShell Core 6                              |

Du kan se den aktuella versionen genom att skriva ut `$PSVersionTable` från valfri funktion.

## <a name="dependency-management"></a>Beroendehantering

PowerShell-funktioner stödjer hantera Azure-moduler med tjänsten. Filen requirements.psd1 genom att ändra host.json och ange egenskapen managedDependency aktiverade till SANT, kommer att behandlas. De senaste Azure-modulerna ska hämtas och blir tillgängliga för funktionen automatiskt.

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
}
```

Att använda dina egna anpassade moduler eller moduler från den [PowerShell-galleriet](https://powershellgallery.com) är lite annorlunda än gör det normalt.

När du installerar modulen på den lokala datorn är den i någon av mapparna globalt tillgänglig i din `$env:PSModulePath`. Eftersom din funktion körs i Azure kan du inte åtkomst till de moduler som är installerat på datorn. Detta kräver att den `$env:PSModulePath` för en PowerShell-funktionen app skiljer sig från `$env:PSModulePath` i en vanlig PowerShell-skript.

I funktioner, `PSModulePath` innehåller två sökvägar:

* En `Modules` mapp som finns i roten för din Funktionsapp.
* En sökväg till en `Modules` mappen som finns i PowerShell språk worker.

### <a name="function-app-level-modules-folder"></a>Funktionen appnivå `Modules` mapp

Om du vill använda anpassade moduler eller PowerShell-moduler från PowerShell-galleriet, kan du placera moduler där dina funktioner beror i en `Modules` mapp. Från den här mappen är moduler automatiskt tillgängliga för functions-körning. En funktion i funktionsappen kan använda dessa moduler.

Om du vill dra nytta av den här funktionen, skapa en `Modules` mapp i roten av din funktionsapp. Spara de moduler som du vill använda i dina funktioner i den här platsen.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Använd `Save-Module` att spara alla moduler som använder dina funktioner eller kopiera dina egna anpassade moduler till den `Modules` mapp. Med en modulmappen ska funktionsappen ha följande mappstrukturen:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

När du startar din funktionsapp, PowerShell språk worker läggs `Modules` mappen till den `$env:PSModulePath` så att du kan lita på modulen autoloading precis som i en vanlig PowerShell-skript.

### <a name="language-worker-level-modules-folder"></a>Worker-språknivå `Modules` mapp

Flera moduler används ofta av PowerShell språk arbetsprocessen. Dessa moduler har definierats i sista positionen för `PSModulePath`. 

Den aktuella listan över moduler är följande:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modulen används för att arbeta med Arkiv, som `.zip`, `.nupkg`, med mera.
* **ThreadJob**: En tråd-baserad implementering av jobbet PowerShell API: er.

Den senaste versionen av modulerna används av funktioner. Om du vill använda en specifik version av dessa moduler kan du placera den specifika versionen på den `Modules` mapp på din funktionsapp.

## <a name="environment-variables"></a>Miljövariabler

I funktioner, [appinställningar](functions-app-settings.md), till exempel tjänstanslutning strängar, visas som miljövariabler under körning. Du kan komma åt de här inställningarna med hjälp av `$env:NAME_OF_ENV_VAR`, enligt följande exempel:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

När du kör lokalt appinställningar läses från den [local.settings.json](functions-run-local.md#local-settings-file) projektfilen.

## <a name="concurrency"></a>Samtidighet

Som standard kan endast PowerShell Functions-runtime behandla ett anrop till en funktion i taget. Den här nivån för samtidighet kan dock inte är tillräckligt i följande situationer:

* När du försöker hantera ett stort antal anrop på samma gång.
* När du har funktioner som anropar andra funktioner i samma funktionsapp.

Du kan ändra det här beteendet genom att ange följande miljövariabler till ett heltalsvärde:

```
PSWorkerInProcConcurrencyUpperBound
```

Du anger den här miljövariabeln i den [appinställningar](functions-app-settings.md) på din Funktionsapp.

### <a name="considerations-for-using-concurrency"></a>Att tänka på när samtidighet

PowerShell är en _enda tråd_ skriptspråk som standard. Samtidighet kan dock läggas till med hjälp av flera PowerShell-körningsutrymmen i samma process. Den här funktionen fungerar så PowerShell för Azure Functions-runtime.

Det finns vissa nackdelar med den här metoden.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>Samtidighet är endast lika bra ut som den dator som den körs på

Om din funktionsapp körs på en [App Service-plan](functions-scale.md#app-service-plan) som stöder bara en enda kärna och samtidighet kommer inte att mycket. Det beror på att det finns inga ytterligare kärnor för att avlasta belastningen. I det här fallet prestanda kan variera beroende den enda kärnan har kontext-växlar du mellan körningsutrymmen.

Den [förbrukningsplan](functions-scale.md#consumption-plan) körs med endast en kärna, så att du inte kan använda samtidighet. Om du vill dra full nytta av samtidighet i stället distribuera dina funktioner till en funktionsapp som körs på en dedikerad App Service-plan med tillräckligt med kärnor.

#### <a name="azure-powershell-state"></a>Azure PowerShell-tillstånd

Azure PowerShell använder vissa _processnivå_ kontexter och tillstånd för att spara du från överflödiga att skriva. Men om du aktiverar samtidighet i din funktionsapp och anropa åtgärder som ändrar tillstånd, kan du få med konkurrenstillstånd. Dessa konkurrenstillstånd är svåra att felsöka eftersom ett anrop som förlitar sig på en viss status och det andra anropet ändrade tillståndet.

Det finns stora värdet i samtidighet med Azure PowerShell, eftersom vissa åtgärder kan ta lång tid. Du måste dock fortsätta med försiktighet. Om du misstänker att du har ett konkurrenstillstånd, ange samtidigheten tillbaka till `1` och försök sedan igen.

## <a name="configure-function-scriptfile"></a>Konfigurera funktionen `scriptFile`

Som standard körs en PowerShell-funktionen från `run.ps1`, en fil som delar samma överordnad katalog som dess motsvarande `function.json`.

Den `scriptFile` -egenskapen i den `function.json` kan användas för att få en mappstruktur som ser ut som i följande exempel:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

I det här fallet den `function.json` för `myFunction` innehåller en `scriptFile` egenskap som refererar till filen med exporterade funktionen ska köras.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Använda PowerShell-moduler genom att konfigurera en startpunkt

Den här artikeln har visat PowerShell-funktioner i standard `run.ps1` skriptfilen som genererats av mallarna.
Du kan dock även innehålla dina funktioner i PowerShell-moduler. Du kan referera till specifika Funktionskoden i modulen med hjälp av den `scriptFile` och `entryPoint` fält i function.json' konfigurationsfilen.

I det här fallet `entryPoint` är namnet på en funktion eller cmdlet i PowerShell-modulen som refereras i `scriptFile`.

Överväg följande mappstrukturen:

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

I det här exemplet, konfigurationen för `myFunction` innehåller en `scriptFile` egenskap som refererar till `PSFunction.psm1`, vilket är en PowerShell-modul i en annan mapp.  Den `entryPoint` egenskapen refererar till den `Invoke-PSTestFunc` som är startpunkten i modulen.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Med den här konfigurationen måste den `Invoke-PSTestFunc` genomförs exakt som en `run.ps1` skulle.

## <a name="considerations-for-powershell-functions"></a>Överväganden för PowerShell-funktioner

När du arbetar med PowerShell-funktioner måste du vara medveten om överväganden i följande avsnitt.

### <a name="cold-start"></a>Kallstart

När du utvecklar Azure Functions i den [serverlös värdmodell](functions-scale.md#consumption-plan), kalla startar är verklighet. *Kallstart* refererar till tid det tar för din funktionsapp att börja köra för att bearbeta en begäran. Kallstart sker mer ofta i förbrukningen planera eftersom din funktionsapp hämtar stängs av under perioder av inaktivitet.

### <a name="bundle-modules-instead-of-using-install-module"></a>Paket moduler istället för att använda `Install-Module`

Skriptet körs på varje anrop. Undvik att använda `Install-Module` i ditt skript. I stället använda `Save-Module` innan du publicerar så att din funktion inte behöver slösa bort gången du hämtar modulen. Om kallstarter som påverkar dina funktioner, Överväg att distribuera funktionsappen till en [apptjänstplan](functions-scale.md#app-service-plan) inställd *alltid på* eller till en [premiumplan](functions-scale.md#premium-plan-public-preview).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

[referens för host.json]: functions-host-json.md
