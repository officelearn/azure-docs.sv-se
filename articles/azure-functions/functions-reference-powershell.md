---
title: Referens för PowerShell-utvecklare för Azure Functions
description: Lär dig hur du utvecklar funktioner med hjälp av PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276743"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell-guide för utvecklare

Den här artikeln innehåller information om hur du skriver Azure Functions med hjälp av PowerShell.

En PowerShell-funktion (funktion) i Azure representeras som ett PowerShell-skript som körs när den utlöses. Varje funktions skript har en relaterad `function.json` fil som definierar hur funktionen fungerar, till exempel hur den utlöses och dess indata-och utdataparametrar. Mer information finns i [artikeln utlösare och bindning](functions-triggers-bindings.md). 

Precis som andra typer av funktioner, tar PowerShell-skript funktioner i parametrar som matchar namnen på alla angivna bindningar i `function.json` filen. En `TriggerMetadata` parameter skickas också som innehåller ytterligare information om utlösaren som startade funktionen.

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

I projekt roten finns det en delad [`host.json`](functions-host-json.md) fil som kan användas för att konfigurera Function-appen. Varje funktion har en mapp med sin egen kod fil (. ps1) och bindnings konfigurations`function.json`fil (). Namnet på function. JSON-filens överordnade katalog är alltid namnet på din funktion.

Vissa bindningar kräver en `extensions.csproj` fils förekomst. Bindnings tillägg som krävs i [version 2. x och senare versioner](functions-versions.md) av Functions-körningen definieras i `extensions.csproj` filen med de faktiska biblioteksfilerna i `bin` mappen. När du utvecklar lokalt måste du [Registrera bindnings tillägg](functions-bindings-register.md#extension-bundles). När du utvecklar funktioner i Azure Portal görs registreringen åt dig.

I PowerShell Function-appar kan du välja att ha en `profile.ps1` som körs när en Function-app börjar köras (på annat sätt vet som en *[kall start](#cold-start)*). Mer information finns i [PowerShell-profil](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definiera ett PowerShell-skript som en funktion

Som standard söker Functions-körningen efter din funktion `run.ps1`i, `run.ps1` där delar samma överordnade katalog som dess motsvarande `function.json`.

Ditt skript har skickat ett antal argument vid körning. Om du vill hantera dessa parametrar lägger `param` du till ett block överst i skriptet som i följande exempel:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata-parameter

`TriggerMetadata` Parametern används för att ange ytterligare information om utlösaren. Ytterligare metadata varierar från bindning till bindning, men alla innehåller en `sys` egenskap som innehåller följande data:

```powershell
$TriggerMetadata.sys
```

| Egenskap   | Beskrivning                                     | Typ     |
|------------|-------------------------------------------------|----------|
| UtcNow     | När, i UTC, utlöstes funktionen        | DateTime |
| MethodName | Namnet på den funktion som har utlösts     | sträng   |
| RandGuid   | ett unikt GUID för den här körningen av funktionen | sträng   |

Varje utlösnings typ har en annan uppsättning metadata. Till exempel, `$TriggerMetadata` för `QueueTrigger` innehåller `InsertionTime` `Id` `DequeueCount`andra saker. För ytterligare information om köns utlösare metadata, gå till den [officiella dokumentationen för köade utlösare](functions-bindings-storage-queue-trigger.md#message-metadata). Läs dokumentationen om de [utlösare](functions-triggers-bindings.md) som du arbetar med för att se vad som ingår i utlösarens metadata.

## <a name="bindings"></a>Bindningar

I PowerShell konfigureras och definieras [bindningarna](functions-triggers-bindings.md) i en funktions funktion. JSON. Funktioner interagerar med bindningar på flera sätt.

### <a name="reading-trigger-and-input-data"></a>Läsa utlösare och indata

Utlösare och angivna bindningar läses som parametrar som skickas till din funktion. Angivna bindningar har en `direction` inställt på `in` i function. JSON. `name` Egenskapen som definieras i `function.json` är namnet på parametern i `param` blocket. Eftersom PowerShell använder namngivna parametrar för bindning, spelar det ingen roll för parametrarna. Det är dock en bra idé att följa ordningen på de bindningar som definierats i `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Skriver utdata

I functions har en utgående bindning en `direction` angiven till `out` i funktionen. JSON. Du kan skriva till en utgående bindning genom att använda `Push-OutputBinding` cmdleten, som är tillgänglig för functions-körningen. I `name` samtliga fall motsvarar egenskapen för bindningen som definieras i `function.json` den `Name` parametern för `Push-OutputBinding` cmdleten.

Följande visar hur du anropar `Push-OutputBinding` funktions skriptet:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Du kan också skicka ett värde för en speciell bindning via pipelinen.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`beter sig på olika sätt baserat på det värde som `-Name`har angetts för:

* Om det angivna namnet inte kan matchas med en giltig utgående bindning, genereras ett fel.

* När bindningen för utdata accepterar en samling värden kan du anropa `Push-OutputBinding` flera gånger för att skicka flera värden flera gånger.

* När bindningen för utdata bara tar emot ett singleton- `Push-OutputBinding` värde uppstår ett fel när en sekund anropas.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`uttryck

Följande är giltiga parametrar för att anropa `Push-OutputBinding`:

| Name | Typ | Position | Beskrivning |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Sträng | 1 | Namnet på den utgående bindning som du vill ange. |
| **`-Value`** | Objekt | 2 | Värdet för den utgående bindning som du vill ange, som accepteras från pipelinen ByValue. |
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

En HTTP-utlösare returnerar ett svar med hjälp `response`av en utgående bindning med namnet. I följande exempel har utgående bindningen för `response` värdet "utdata #1":

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

För utdata som bara accepterar singleton-värden kan du använda `-Clobber` parametern för att åsidosätta det gamla värdet i stället för att försöka lägga till i en samling. I följande exempel förutsätter vi att du redan har lagt till ett värde. Genom att `-Clobber`använda, åsidosätter svaret från följande exempel det befintliga värdet för att returnera värdet "utdata #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding exempel: utgående bindning för kö

`Push-OutputBinding`används för att skicka data till utgående bindningar, t. ex. en [Azure Queue Storage utgående bindning](functions-bindings-storage-queue-output.md). I följande exempel har meddelandet som skrivs till kön värdet "utdata #1":

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

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`kommandon

Du kan använda `Get-OutputBinding` cmdleten för att hämta de värden som för närvarande är inställda för dina utgående bindningar. Denna cmdlet hämtar en hash-tabellen som innehåller namnen på utgående bindningarna med respektive värden. 

Följande är ett exempel på hur du `Get-OutputBinding` kan använda för att returnera aktuella bindnings värden:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`innehåller också en parameter som `-Name`kallas, som kan användas för att filtrera den returnerade bindningen, som i följande exempel:

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
| Felsökning | **`Write-Debug`** |
| Spårning | **`Write-Progress`** <br /> **`Write-Verbose`** |

Förutom dessa cmdlets omdirigeras allt som skrivs till pipelinen till `Information` logg nivån och visas med standardvärdet för PowerShell.

> [!IMPORTANT]
> Det `Write-Verbose` räcker inte `Write-Debug` att använda-eller-cmdletarna för att se utförlig loggning och fel söknings nivå. Du måste också konfigurera logg nivå tröskeln, som deklarerar vilken nivå av loggar du faktiskt bryr dig om. Mer information finns i [Konfigurera Function-programmets loggnings nivå](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurera Function-programmets loggnings nivå

Med Azure Functions kan du definiera tröskel nivån för att göra det enkelt att styra hur funktioner skriver till loggarna. Om du vill ange tröskelvärdet för alla spår som skrivs till-konsolen `logging.logLevel.default` använder du egenskapen i [ `host.json` filen][Host. JSON-referens]. Den här inställningen gäller för alla funktioner i din Function-app.

I följande exempel anges tröskelvärdet för att aktivera utförlig loggning för alla funktioner, men anger tröskelvärdet för att aktivera fel söknings loggning för `MyFunction`en funktion med namnet:

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

Om du kör din Funktionsapp lokalt för utveckling loggar standardinställningarna i fil systemet. Om du vill se loggarna i-konsolen anger `AZURE_FUNCTIONS_ENVIRONMENT` du miljövariabeln `Development` innan du startar Funktionsapp.

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

Request-objektet som skickas till skriptet är av typen `HttpRequestContext`, som har följande egenskaper:

| Egenskap  | Beskrivning                                                    | Typ                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Ett objekt som innehåller bröd texten i begäran. `Body`serialiseras till den bästa typen baserat på data. Om data till exempel är JSON, skickas de som en hash-tabell. Om datan är en sträng skickas den som en sträng. | objekt |
| **`Headers`** | En ord lista som innehåller begärandehuvuden.                | Ord listans<sträng, sträng><sup>*</sup> |
| **`Method`** | HTTP-metoden för begäran.                                | sträng                    |
| **`Params`**  | Ett objekt som innehåller Dirigerings parametrarna för begäran. | Ord listans<sträng, sträng><sup>*</sup> |
| **`Query`** | Ett objekt som innehåller frågeparametrarna.                  | Ord listans<sträng, sträng><sup>*</sup> |
| **`Url`** | URL för begäran.                                        | sträng                    |

<sup>*</sup>Alla `Dictionary<string,string>` nycklar är inte Skift läges känsliga.

#### <a name="response-object"></a>Svarsobjekt

Objektet Response som du ska skicka tillbaka är av typen `HttpResponseContext`, som har följande egenskaper:

| Egenskap      | Beskrivning                                                 | Typ                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Ett objekt som innehåller bröd texten i svaret.           | objekt                    |
| **`ContentType`** | En kort hand för att ange innehålls typen för svaret. | sträng                    |
| **`Headers`** | Ett objekt som innehåller svarshuvuden.               | Ord lista eller hash   |
| **`StatusCode`**  | HTTP-statuskod för svaret.                       | sträng eller heltal             |

#### <a name="accessing-the-request-and-response"></a>Åtkomst till begäran och svar

När du arbetar med HTTP-utlösare kan du komma åt HTTP-begäran på samma sätt som med andra inkommande bindningar. Den är i `param` blocket.

Använd ett `HttpResponseContext` objekt för att returnera ett svar, som du ser i följande avsnitt:

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

Om du till exempel vill ha data från Blob Storage som anges som en sträng lägger du till följande typ konvertering `param` till mitt block:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell-profil

I PowerShell är begreppet en PowerShell-profil. Om du inte är bekant med PowerShell-profiler, se [om profiler](/powershell/module/microsoft.powershell.core/about/about_profiles).

I PowerShell-funktioner körs profil skriptet när funktions programmet startar. Function-appar startar när de först distribueras och efter att de varit inaktiva ([kall start](#cold-start)).

När du skapar en Function-app med hjälp av verktyg, till exempel Visual Studio Code och Azure Functions Core Tools `profile.ps1` , skapas en standard åt dig. Standard profilen underhålls [av GitHub-lagringsplatsen Core tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) och innehåller:

* Automatisk MSI-autentisering till Azure.
* Möjligheten att aktivera Azure PowerShell `AzureRM` PowerShell-alias om du vill.

## <a name="powershell-version"></a>PowerShell-version

I följande tabell visas den PowerShell-version som används av varje huvud version av Functions-körningen:

| Funktions version | PowerShell-version                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5,1 (låst av körnings miljön) |
| 2x               | PowerShell Core 6                              |

Du kan se den aktuella versionen genom att `$PSVersionTable` skriva ut från vilken funktion som helst.

## <a name="dependency-management"></a>Beroendehantering

Med funktioner kan du utnyttja [PowerShell-galleriet](https://www.powershellgallery.com) för att hantera beroenden. När beroende hantering är aktiverat används filen Requirements. psd1 för att automatiskt hämta nödvändiga moduler. Du aktiverar det här beteendet genom `managedDependency` att ange `true` egenskapen till i roten i [Host. JSON-filen](functions-host-json.md), som i följande exempel:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

När du skapar ett nytt PowerShell Functions-projekt aktive ras beroende hantering som standard, med Azure [ `Az` -modulen](/powershell/azure/new-azureps-module-az) som ingår. Det maximala antalet moduler som stöds för närvarande är 10. Den syntax som stöds _`MajorNumber`_ `.*` är eller en exakt version av modulen, som visas i följande krav. psd1 exempel:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

När du uppdaterar filen Requirements. psd1 installeras uppdaterade moduler efter en omstart.

> [!NOTE]
> Hanterade beroenden kräver åtkomst till www.powershellgallery.com för att hämta moduler. När du kör lokalt kontrollerar du att körnings miljön har åtkomst till den här URL: en genom att lägga till eventuella nödvändiga brand Väggs regler. 

Följande program inställningar kan användas för att ändra hur de hanterade beroendena hämtas och installeras. Din app-uppgradering startar `MDMaxBackgroundUpgradePeriod`inom och uppgraderings processen slutförs inom ungefär `MDNewSnapshotCheckPeriod`.

| Funktionsapp inställning              | Standardvärde             | Beskrivning                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`(7 dagar)     | Varje PowerShell-arbetsprocess initierar sökning efter modul uppgraderingar på den PowerShell-galleriet processen startar och var `MDMaxBackgroundUpgradePeriod` som än är. När en ny version av modulen är tillgänglig i PowerShell-galleriet, installeras den på fil systemet och görs tillgänglig för PowerShell-arbetare. Genom att minska det här värdet kan din Function-app Hämta nyare modul versioner tidigare, men det ökar också resursanvändningen för appar (nätverks-I/O, CPU, lagring). Om du ökar det här värdet minskar appens resursanvändning, men det kan också dröja att leverera nya versioner av modulen till din app. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`(1 timme)       | När nya versioner av modulen har installerats i fil systemet måste varje PowerShell-arbetsprocess startas om. Att starta om PowerShell-arbetskraftar påverkar appens tillgänglighet eftersom den kan avbryta den aktuella funktions körningen. Till dess att alla PowerShell-arbetsprocesser har startats om kan funktions anrop använda antingen den gamla versionen eller de nya modulerna. Startar om alla PowerShell-anställda i `MDNewSnapshotCheckPeriod`. Om du ökar det här värdet minskas frekvensen för avbrott, men det kan också öka tids perioden när funktions anropen använder antingen den gamla eller den nya modulens versioner icke-deterministiskt. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`(1 dag)     | För att undvika alltför stora modul uppgraderingar vid frekventa arbets starter, utförs ingen sökning efter modul uppgraderingar när en anställd redan har initierat den här `MDMinBackgroundUpgradePeriod`kontrollen. |

Att dra nytta av dina egna anpassade moduler är lite annorlunda än hur du gör det normalt.

På den lokala datorn installeras modulen i någon av de globalt tillgängliga mapparna i `$env:PSModulePath`. När du kör i Azure har du inte åtkomst till de moduler som är installerade på datorn. Det innebär att `$env:PSModulePath` för en PowerShell Function-app skiljer sig `$env:PSModulePath` från i ett vanligt PowerShell-skript.

I functions `PSModulePath` , innehåller två sökvägar:

* En `Modules` mapp som finns i roten för din Function-app.
* En sökväg till en `Modules` mapp som styrs av PowerShell-språket.

### <a name="function-app-level-modules-folder"></a>Funktion app-Level `Modules` -mapp

Om du vill använda anpassade moduler kan du placera moduler som dina funktioner är beroende av `Modules` i en mapp. I den här mappen är moduler automatiskt tillgängliga för functions-körningen. Alla funktioner i Function-appen kan använda dessa moduler. 

> [!NOTE]
> Moduler som anges i filen Requirements. psd1 laddas ned automatiskt och inkluderas i sökvägen så att du inte behöver ta med dem i mappen moduler. Dessa lagras lokalt i `$env:LOCALAPPDATA/AzureFunctions` mappen och i `/data/ManagedDependencies` mappen när de körs i molnet.

Om du vill dra nytta av funktionen för anpassad modul skapar `Modules` du en mapp i roten för din Function-app. Kopiera modulerna som du vill använda i dina funktioner till den här platsen.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

I en `Modules` mapp ska din Function-app ha följande mappstruktur:

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

När du startar en Function-app lägger PowerShell-språket till den `Modules` här mappen till `$env:PSModulePath` så att du kan förlita dig på att modulen laddas automatiskt upp precis som i ett vanligt PowerShell-skript.

### <a name="language-worker-level-modules-folder"></a>Mapp för språk `Modules` arbets nivå

Flera moduler används ofta av PowerShell-språket. Dessa moduler definieras i den sista positionen i `PSModulePath`. 

Den aktuella listan över moduler är följande:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modulen används för att arbeta med Arkiv, `.zip`t `.nupkg`. ex., och andra.
* **ThreadJob**: en tråd baserad implementering av PowerShell-jobbets API: er.

Som standard använder funktionen den senaste versionen av dessa moduler. Om du vill använda en speciell version av en modul, ska du `Modules` ange den här versionen i mappen i din Function-app.

## <a name="environment-variables"></a>Miljövariabler

I funktioner visas [appinställningar](functions-app-settings.md), till exempel tjänst anslutnings strängar, som miljövariabler under körningen. Du kan komma åt de här `$env:NAME_OF_ENV_VAR`inställningarna med, som du ser i följande exempel:

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

Det finns stor värde i samtidighet med Azure PowerShell eftersom vissa åtgärder kan ta lång tid. Du måste dock gå vidare med försiktighet. Om du misstänker att du råkar ut för ett konkurrens tillstånd ställer du in appens `1` PSWorkerInProcConcurrencyUpperBound till och använder i stället [språket arbets process nivå isolering](functions-app-settings.md#functions_worker_process_count) för samtidighet.

## <a name="configure-function-scriptfile"></a>Konfigurera funktion`scriptFile`

Som standard körs en PowerShell-funktion från `run.ps1`, en fil som delar samma överordnade katalog som dess motsvarande. `function.json`

`scriptFile` Egenskapen i `function.json` kan användas för att hämta en mappstruktur som ser ut som i följande exempel:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

I det här fallet innehåller `function.json` egenskapen `myFunction` för en `scriptFile` egenskap som refererar till filen med den exporterade funktionen som ska köras.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Använd PowerShell-moduler genom att konfigurera en entryPoint

Den här artikeln har visat PowerShell-funktioner i `run.ps1` standard skript filen som genereras av mallarna.
Du kan dock även inkludera dina funktioner i PowerShell-moduler. Du kan referera till din speciella funktions kod i modulen med hjälp av `scriptFile` fälten `entryPoint` och i konfigurations filen function. JSON.

I det här fallet `entryPoint` är namnet på en funktion eller en cmdlet i PowerShell-modulen som refereras `scriptFile`i.

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

I det här exemplet innehåller konfigurationen för `myFunction` en `scriptFile` egenskap som refererar `PSFunction.psm1`, som är en PowerShell-modul i en annan mapp.  `entryPoint` Egenskapen refererar till `Invoke-PSTestFunc` funktionen, som är start punkten i modulen.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Med den här konfigurationen körs `Invoke-PSTestFunc` de exakt som en `run.ps1` .

## <a name="considerations-for-powershell-functions"></a>Att tänka på för PowerShell-funktioner

När du arbetar med PowerShell-funktioner bör du vara medveten om överväganden i följande avsnitt.

### <a name="cold-start"></a>Kall start

När du utvecklar Azure Functions i den [serverbaserade värd modellen](functions-scale.md#consumption-plan)är kall start en verklighet. *Kall start* avser den tid det tar för din Function-app att börja köra för att bearbeta en begäran. Kall start sker oftare i förbruknings planen eftersom din funktions app stängs av under perioder av inaktivitet.

### <a name="bundle-modules-instead-of-using-install-module"></a>Paketera moduler i stället för att använda`Install-Module`

Skriptet körs vid varje anrop. Undvik att `Install-Module` använda i skriptet. Använd `Save-Module` i stället före publicering så att din funktion inte behöver slösa tid på att ladda ned modulen. Om kall startar påverkar dina funktioner, bör du överväga att distribuera din Function-app till en [App Service plan](functions-scale.md#app-service-plan) inställd på *Always on* eller till en [Premium-plan](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)

[Referens för host.json]: functions-host-json.md
