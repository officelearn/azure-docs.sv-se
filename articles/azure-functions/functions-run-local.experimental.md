---
title: Arbeta med Azure Functions Core Tools | Microsoft Docs
description: Lär dig mer om att koda och testa Azure functions från Kommandotolken eller terminal på din lokala dator innan du kör dem på Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: glenga
ms.openlocfilehash: a5acc65609b7d8387eca0ec4e091bf823d9d1d67
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402702"
---
# <a name="work-with-azure-functions-core-tools"></a>Arbeta med Azure Functions Core Tools

Azure Functions Core Tools kan du utveckla och testa dina funktioner på den lokala datorn från Kommandotolken eller terminal. Din lokala funktioner kan ansluta levande Azure-tjänster och du kan felsöka dina funktioner på den lokala datorn med hjälp av den fullständiga Functions-körningen. Du kan även distribuera en funktionsapp till din Azure-prenumeration.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Utveckla funktioner på din lokala dator och publicera dem på Azure med hjälp av Core Tools följer du de här stegen:

> [!div class="checklist"]
> * [Installera de viktigaste verktygen och beroenden.](#v2)
> * [Skapa ett funktionsapprojekt från en mall för vissa språk.](#create-a-local-functions-project)
> * [Registrera utlösare och bindningen tillägg.](#register-extensions)
> * [Definiera lagring och andra anslutningar.](#local-settings-file)
> * [Skapa en funktion från en utlösare och språkspecifika mall.](#create-func)
> * [Kör funktionen lokalt](#start)
> * [Publicera projektet på Azure](#publish)

## <a name="core-tools-versions"></a>Core Tools versioner

Det finns två versioner av Azure Functions Core Tools. Vilken version du använder beror på din lokala utvecklingsmiljö [val av språk](supported-languages.md), och nivå av support som krävs:

+ Version 1.x: har stöd för version 1.x av körningen. Den här versionen av verktygen stöds endast på Windows-datorer och installeras från en [npm-paketet](https://docs.npmjs.com/getting-started/what-is-npm). Med den här versionen kan du skapa funktioner i experimentella språk som inte stöds officiellt. Mer information finns i [språk som stöds i Azure Functions](supported-languages.md)

+ [Version 2.x](#v2): har stöd för [version 2.x av runtime](functions-versions.md). Den här versionen stöder [Windows](#windows-npm), [macOS](#brew), och [Linux](#linux). Använder plattformsspecifika pakethanterare eller npm för installation.

Om inget annat anges i exemplen i den här artikeln gäller för version 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Installera Azure Functions Core Tools

[Azure Functions Core Tools] innehåller en version av samma körning som driver Azure Functions-runtime som du kan köra på din lokala utvecklingsdator. Det ger också kommandon för att skapa funktioner, ansluta till Azure och distribuera function-projekt.

### <a name="v2"></a>Version 2.x

Version 2.x av verktygen använder Azure Functions-runtime 2.x som bygger på .NET Core. Den här versionen stöds för alla plattformar som .NET Core 2.x stöder, inklusive [Windows](#windows-npm), [macOS](#brew), och [Linux](#linux). Du måste först installera .NET Core 2.x SDK.

#### <a name="windows-npm"></a>Windows

Följande steg Använd npm för att installera Core Tools på Windows. Du kan också använda [Chocolatey](https://chocolatey.org/). Mer information finns i den [Core Tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Installera [.NET Core 2.x SDK för Windows](https://www.microsoft.com/net/download/windows).

2. Installera [Node.js], vilket inkluderar npm. För version 2.x av verktyg, endast Node.js 8.5 och senare versioner stöds.

3. Installera Core Tools-paketet:

    ```bash
    npm install -g azure-functions-core-tools
    ```

#### <a name="brew"></a>MacOS med Homebrew

Följande steg använda Homebrew för att installera de viktigaste verktygen på macOS.

1. Installera [.NET Core 2.x SDK för macOS](https://www.microsoft.com/net/download/macos).

2. Installera [Homebrew](https://brew.sh/), om det inte redan är installerat.

3. Installera Core Tools-paketet:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

#### <a name="linux"></a> Linux (Debian/Ubuntu) med APT

I följande anvisningar används [APT](https://wiki.debian.org/Apt) installera Core Tools på din Ubuntu/Debian Linux-distribution. Andra Linux-distributioner finns i den [Core Tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installera [.NET Core 2.x SDK för Linux](https://www.microsoft.com/net/download/linux).

2. Registrera Microsoft-produktnyckeln som tillförlitliga:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Kontrollera din Ubuntu-server kör något av versionerna som är lämplig i tabellen nedan. Om du vill lägga till apt källan, kör du:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Linux-distribution | Version |
    | --------------- | ----------- |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux myntverket 18    | `xenial`  |

4. Installera Core Tools-paketet:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

En functions projektkatalogen innehåller filerna som [host.json](functions-host-json.md) och [local.settings.json](#local-settings-file), tillsammans med undermappar som innehåller koden för enskilda funktioner. Den här katalogen är motsvarigheten till en funktionsapp i Azure. Läs mer om funktioner mappstrukturen i den [utvecklarguide för Azure Functions](functions-reference.md#folder-structure).

Version 2.x måste du välja ett standardspråk för ditt projekt när den har initierats och alla funktioner har lagts till användning standardmallarna för språk. I version 1.x, du ange vilket språk varje gång du skapar en funktion.

I terminalfönstret eller från en kommandotolk, kör du följande kommando för att skapa projektet och lokal Git-lagringsplats:

```bash
func init MyFunctionProj
```

När du anger ett projektnamn, skapas en ny mapp med det namnet och initierats. Annars initieras den aktuella mappen.  
I version 2.x, när du kör kommandot du måste välja en runtime för ditt projekt. Om du planerar att utveckla JavaScript-funktioner, välja **noden**:

```output
Select a worker runtime:
dotnet
node
```

Använd upp/ned piltangenterna för att välja ett språk, och tryck sedan på RETUR. Utdata ser ut som i följande exempel för en JavaScript-projektet:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` stöder följande alternativ, som är version 2.x endast, om inget annat anges:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Initierar en C#-skript (.csx) projekt. Du måste ange `--csx` i efterföljande kommandon. |
| **`--docker`** | Skapa en docker-fil för en behållare med en basavbildning som baseras på valt `--worker-runtime`. Använd det här alternativet när du planerar att publicera till en anpassad Linux-behållare. |
| **`--force`** | Initiera projektet även om det finns befintliga filer i projektet. Den här inställningen skriver över befintliga filer med samma namn. Andra filer i projektmappen påverkas inte. |
| **`--no-source-control -n`** | Standard skapas för en Git-lagringsplats i version 1.x. I version 2.x, git-lagringsplatsen är inte skapas som standard. |
| **`--source-control`** | Styr om en git-databas skapas. Som standard är inte en databas skapas. När `true`, en databas har skapats. |
| **`--worker-runtime`** | Anger CLR för projektet. Värden som stöds är `dotnet`, `node` (JavaScript), `java`, och `python`. När inte har angetts uppmanas du att välja din runtime under initieringen. |

> [!IMPORTANT]
> Som standard version 2.x av de viktigaste verktygen skapar funktionen app-projekt för .NET-runtime som [C#-klass projekt](functions-dotnet-class-library.md) (.csproj). Dessa C#-projekt, som kan användas med Visual Studio eller Visual Studio Code, kompileras under testningen och när du publicerar till Azure. Om du istället vill skapa och arbeta med samma C#-skript (.csx) filer som skapades i version 1.x och i portalen, måste du inkludera den `--csx` parameter när du skapar och distribuerar funktioner.

## <a name="register-extensions"></a>Registrera tillägg

I version 2.x av Azure Functions-runtime som du behöver registrera de tillägg av bindning (bindningstyper) som du använder i din funktionsapp.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Mer information finns i [Azure Functions-utlösare och bindningar begrepp](./functions-bindings-expressions-patterns.md).

## <a name="local-settings-file"></a>Fil för lokala inställningar

Filen local.settings.json lagrar appinställningar, anslutningssträngar och inställningar för Azure Functions Core Tools. Inställningarna i filen local.settings.json används endast av Functions tools när du kör lokalt. Som standard dessa inställningar migreras inte automatiskt när projektet har publicerats till Azure. Använd den `--publish-local-settings` växla [när du publicerar](#publish) att kontrollera att dessa inställningar har lagts till funktionsappen i Azure. Observera att värdena i **ConnectionStrings** aldrig har publicerats. Filen har följande struktur:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

| Inställning      | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | När värdet `true`, alla värden som krypteras med hjälp av en lokal dator-nyckel. Används med `func settings` kommandon. Standardvärdet är `false`. |
| **`Values`** | Samling av programinställningar och anslutningssträngar som används när du kör lokalt. Dessa värden motsvarar appinställningar i din funktionsapp i Azure, till exempel [ `AzureWebJobsStorage` ]. Många utlösare och bindningar har en egenskap som refererar till en appinställning för anslutningssträngen, till exempel `Connection` för den [Blob storage-utlösare](functions-bindings-storage-blob.md#trigger---configuration). För egenskaper, behöver du en programinställning som definierats i den `Values` matris. <br/>[`AzureWebJobsStorage`] är en obligatorisk app inställning för utlösare än HTTP. <br/>Version 2.x av funktionskörningen kräver den [ `FUNCTIONS_WORKER_RUNTIME` ] som genereras för ditt projekt med Core Tools. <br/> När du har den [Azure storage-emulatorn](../storage/common/storage-use-emulator.md) installerat lokalt kan du ange [ `AzureWebJobsStorage` ] till `UseDevelopmentStorage=true` och Core Tools använder emulatorn. Detta är användbart under utvecklingen, men du bör testa med en faktisk lagringsanslutning före distributionen. |
| **`Host`** | Inställningarna i det här avsnittet Anpassa värdprocessen funktioner när du kör lokalt. |
| **`LocalHttpPort`** | Anger standardporten som används när du kör den lokala Functions-värden (`func host start` och `func run`). Den `--port` kommandoradsalternativet har företräde framför det här värdet. |
| **`CORS`** | Definierar de ursprung som får för [cross-origin resource sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Ursprung tillhandahålls som en kommaavgränsad lista med utan blanksteg. Jokertecknet (\*) stöds, vilket gör att begäranden från valfri origin. |
| **`ConnectionStrings`** | Använd inte den här samlingen för anslutningssträngar som används av din funktionsbindning. Den här samlingen används endast av ramverk som kommer vanligtvis anslutningssträngar från den `ConnectionStrings` avsnitt i en konfiguration av fil, som [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Anslutningssträngar i det här objektet läggs till i miljön med providertyp av [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Objekt i den här samlingen inte har publicerats till Azure med andra appinställningar. Du måste uttryckligen lägga till dessa värden till den `Connection strings` insamling av din funktionsappinställningarna. Om du skapar en [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) i Funktionskoden, bör du lagra Anslutningssträngens värde i **programinställningar** i portalen med dina andra anslutningar. |

Funktionen appen inställningsvärden kan också läsa i koden som miljövariabler. Mer information finns i avsnittet miljö variabler i dessa språkspecifika referensämnen:

* [C#-förkompilerad version](functions-dotnet-class-library.md#environment-variables)
* [C#-skript (.csx)](functions-reference-csharp.md#environment-variables)
* [F#skript (.fsx)](functions-reference-fsharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

När ingen giltig lagringsanslutningssträng har angetts för [ `AzureWebJobsStorage` ] och emulatorn inte används, visas följande felmeddelande visas:

> Saknas värde för AzureWebJobsStorage i local.settings.json. Detta krävs för alla utlösare än HTTP. Du kan köra ”func azure functionapp fetch-app-settings \<functionAppName\>' eller ange en anslutningssträng i local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Hämta ditt storage-anslutningssträngar

Även om du använder storage-emulatorn för utveckling, kan du vill testa med en verkligt lagringsutrymme-anslutning. Om vi antar att du redan har [skapat ett lagringskonto](../storage/common/storage-create-storage-account.md), du kan hämta en giltig lagringsanslutningssträng i något av följande sätt:

+ Från den [Azure Portal]. Gå till ditt lagringskonto väljer **åtkomstnycklar** i **inställningar**, kopiera en av de **anslutningssträngen** värden.

  ![Kopiera anslutningssträngen från Azure-portalen](./media/functions-run-local/copy-storage-connection-portal.png)

+ Använd [Azure Storage Explorer](https://storageexplorer.com/) att ansluta till ditt Azure-konto. I den **Explorer**, expandera din prenumeration, Välj ditt lagringskonto och kopiera primär eller sekundär anslutningssträng.

  ![Kopiera anslutningssträngen från Storage Explorer](./media/functions-run-local/storage-explorer.png)

+ Använd Core Tools anslutningssträngen från Azure med en av följande kommandon:

  + Hämta alla inställningar från en befintlig funktionsapp:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Hämta anslutningssträngen för ett specifikt lagringskonto:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    När du redan inte är inloggad till Azure, uppmanas du att göra detta.

## <a name="create-func"></a>Skapa en funktion

Skapa en funktion genom att köra följande kommando:

```bash
func new
```

I version 2.x, när du kör `func new` du uppmanas att välja en mall i standardspråket för funktionsappen och sedan också uppmanas du att välja ett namn för din funktion. I version 1.x, också uppmanas du att välja önskat språk.

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

Funktionskoden skapas i en undermapp med tillhandahållna funktionsnamnet, som du ser i följande utdata för kö-utlösare:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Du kan också ange alternativen i kommandot med följande argument:

| Argument     | Beskrivning                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Version 2.x) Genererar samma C#-skript (.csx) mallarna som används i version 1.x och i portalen. |
| **`--language -l`**| Mallen programmeringsspråk, till exempel C#, F#, eller JavaScript. Det här alternativet krävs i version 1.x. I version 2.x kan inte använda det här alternativet eller välj ett språk som matchar worker-runtime. |
| **`--name -n`** | Funktionsnamnet. |
| **`--template -t`** | Använd den `func templates list` kommando för att se den fullständiga listan över tillgängliga mallar för varje språk som stöds.   |

Till exempel för att skapa en JavaScript-HTTP-utlösare i ett enda kommando, kör du:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Om du vill skapa en funktion som utlöses av kön med ett enda kommando, kör du:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Köra funktioner lokalt

För att köra Functions-projekt, kör du Functions-värden. Värden kan utlösare för alla funktioner i projektet:

```bash
func host start
```

Den `host` kommando krävs endast i version 1.x.

`func host start` stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Gör inga aktuella build-projektet innan du kör. För dotnet-projekt. Standard är inställd på false. Version 2.x endast. |
| **`--cert`** | Sökvägen till en .pfx-fil som innehåller en privat nyckel. Bara används med `--useHttps`. Version 2.x endast. |
| **`--cors-credentials`** | Tillåt resursdelning för korsande ursprung autentiserade begäranden (t.ex. cookies och rubriken autentisering) Version 2.x endast. |
| **`--cors`** | En kommaavgränsad lista över CORS-ursprung, utan blanksteg. |
| **`--language-worker`** | Argument konfigurera språk worker. Version 2.x endast. |
| **`--nodeDebugPort -n`** | Porten för nod-felsökare att använda. Standard: Ett värde från launch.json eller 5858. Version 1.x endast. |
| **`--password`** | Lösenordet eller en fil som innehåller lösenordet för en .pfx-fil. Bara används med `--cert`. Version 2.x endast. |
| **`--port -p`** | Lokal port att lyssna på. Standardvärde: 7071. |
| **`--pause-on-error`** | Pausa för ytterligare indata innan du avslutar processen. Används endast när den startas Core Tools från en integrerad utvecklingsmiljö (IDE).|
| **`--script-root --prefix`** | Används för att ange sökvägen till roten för funktionsappen som ska köras eller distribueras. Det här används för kompilerade projekt som genererar av projektfiler till en undermapp. Till exempel när du skapar en C#-klassbibliotek projekt, host.json, local.settings.json och function.json filer skapas i en *rot* undermapp med en sökväg som `MyProject/bin/Debug/netstandard2.0`. I så fall, Ange prefixet som `--script-root MyProject/bin/Debug/netstandard2.0`. Det här är roten av funktionsappen vid körning i Azure. |
| **`--timeout -t`** | Tidsgränsen för Functions värden startas, i sekunder. Standard: 20 sekunder.|
| **`--useHttps`** | Binda till `https://localhost:{port}` snarare än till `http://localhost:{port}`. Det här alternativet skapar som standard ett betrott certifikat på datorn.|

För en C# klassbiblioteksprojektet (.csproj), måste du inkludera den `--build` alternativet för att generera DLL-filen för biblioteket.

När funktioner värden startas, returnerar de URL: en för HTTP-utlösta funktionerna:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>När du kör lokalt är inte autentisering aktiv för HTTP-slutpunkter. Det innebär att alla lokala HTTP-förfrågningar hanteras som `authLevel = "anonymous"`. Mer information finns i den [HTTP-bindning artikeln](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Skicka test-data till en funktion

Testa dina funktioner lokalt, du [startar Functions värden](#start) och anropa slutpunkter på den lokala servern med hjälp av HTTP-begäranden. Den slutpunkt som du anropar beror på vilken typ av funktionen.

>[!NOTE]
> Exemplen i det här avsnittet använda verktyget cURL för att skicka HTTP-begäranden från terminalen eller en kommandotolk. Du kan använda ett verktyg för att skicka HTTP-begäranden till den lokala servern. CURL-verktyget är tillgängligt som standard på Linux-baserade system. På Windows, måste du först hämta och installera den [cURL verktyget](https://curl.haxx.se/).

Mer allmän information om att testa functions finns i [strategier för att testa din kod i Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>HTTP- och webhook-utlösta funktionerna

Du anropar följande slutpunkt för att köras lokalt HTTP och webhook-utlösta funktionerna:

    http://localhost:{port}/api/{function_name}

Se till att använda samma servernamn och port som Functions-värden lyssnar på. Du ser du i utdata genereras när du startar funktionen värden. Du kan anropa den här URL: en med hjälp av valfri HTTP-metod som stöds av utlösaren.

Följande cURL-kommando utlösare den `MyHttpTrigger` Snabbstart funktion från en GET-begäran med den _namn_ -parametern som angavs i frågesträngen.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

I följande exempel är samma funktion som anropas från en POST-begäran som passerar _namn_ i begärandetexten:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Du kan hämta begäranden från en webbläsare som överföring av data i frågesträngen. Du måste använda cURL, Fiddler, Postman eller ett liknande HTTP-testverktyg för alla HTTP-metoder.

#### <a name="non-http-triggered-functions"></a>Icke-HTTP-utlösta funktionerna

För alla typer av funktioner än HTTP-utlösare och webhooks kan testa du dina funktioner lokalt genom att anropa en slutpunkt för administration. Anropa den här slutpunkten med en HTTP POST-begäran på den lokala servern utlöser funktionen. Du kan välja att skicka testdata att körningen i brödtexten i POST-begäran. Den här funktionen liknar den **Test** fliken i Azure-portalen.

Du kan anropa följande administratör slutpunkt för att utlösa icke-HTTP-funktioner:

    http://localhost:{port}/admin/functions/{function_name}

Du måste ange data i brödtexten i en POST-förfrågan för om du vill skicka testdata till administratör slutpunkten för en funktion. Meddelandetexten måste ha följande JSON-format:

```JSON
{
    "input": "<trigger_input>"
}
```

Den `<trigger_input>` värdet innehåller data i ett format som förväntades av funktionen. Följande cURL-exempel finns ett INLÄGG till en `QueueTriggerJS` funktion. I det här fallet är indata en sträng som motsvarar meddelandet som förväntas ska finnas i kön.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Med hjälp av den `func run` i version 1.x

>[!IMPORTANT]
> Den `func run` kommandot stöds inte i version 2.x av verktygen. Mer information finns i avsnittet [hur du Azure Functions runtime versioner](set-runtime-version.md).

Du kan också anropa en funktion som direkt med hjälp av `func run <FunctionName>` och ange indata för funktionen. Det här kommandot liknar en funktion med hjälp av den **Test** fliken i Azure-portalen.

`func run` stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Infogad innehåll. |
| **`--debug -d`** | Koppla en felsökare till värdprocessen innan du kör funktionen.|
| **`--timeout -t`** | Tid (i sekunder) tills den lokala funktioner värden är klar.|
| **`--file -f`** | Filnamnet för att använda innehåll.|
| **`--no-interactive`** | Frågar inte om indata. Användbart för automatiseringsscenarier.|

Till exempel för att anropa en HTTP-utlöst funktion och skicka brödtexten för innehållet, kör du följande kommando:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publicera till Azure

Core Tools stöder två typer av distribution, distribuera funktionen projektfilerna direkt till din funktionsapp och distribuera en anpassad Linux-behållare, vilket stöds bara i version 2.x. Du måste redan ha [skapat en funktionsapp i Azure-prenumerationen](functions-cli-samples.md#create).

I version 2.x, måste du ha [registrerad dina tillägg](#register-extensions) i projektet innan du publicerar. Projekt som kräver kompilering ska byggas så att binärfilerna som kan distribueras.

### <a name="project-file-deployment"></a>Projektet filen distribution

Den vanligaste distributionsmetoden innebär att med hjälp av Core Tools att paketera ditt funktionsappsprojekt, binärfiler och beroenden och distribuera dem till din funktionsapp. Du kan eventuellt [köra dina funktioner direkt från distributionspaketet](run-functions-from-deployment-package.md).

Om du vill publicera en Functions-projekt till en funktionsapp i Azure, använda den `publish` kommando:

```bash
func azure functionapp publish <FunctionAppName>
```

Det här kommandot publicerar till en befintlig funktionsapp i Azure. Ett fel uppstår när den `<FunctionAppName>` finns inte i din prenumeration. Läs hur du skapar en funktionsapp från Kommandotolken eller med hjälp av Azure CLI-terminalfönstret i [skapa en Funktionsapp för serverlös körning](./scripts/functions-cli-create-serverless.md).

Den `publish` kommando laddar upp innehållet i projektkatalogen funktioner. Om du tar bort filer lokalt, den `publish` kommandot tar inte bort dem från Azure. Du kan ta bort filer i Azure med hjälp av den [Kudu-verktyget](functions-how-to-use-azure-function-app-settings.md#kudu) i den [Azure Portal].

>[!IMPORTANT]
> När du skapar en funktionsapp i Azure-portalen används version 2.x av funktionskörningen som standard. Att göra funktionen app Använd version 1.x av körning, följer du anvisningarna i [kör version 1.x](functions-versions.md#creating-1x-apps).
> Du kan inte ändra runtime-versionen för en funktionsapp som har befintliga funktioner.

Följande projektet publiceringsalternativ tillkommer för versioner, 1.x och 2.x:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publiceringsinställningar i local.settings.json till Azure, där du uppmanas att skriva över om inställningen finns redan. Om du använder lagringsemulatorn kan du ändra appinställningen en [faktiska lagringsanslutning](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Ignorera skrivs appinställningar när `--publish-local-settings -i` används.|

Följande projektet publiceringsalternativ stöds bara i version 2.x:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Endast Publiceringsinställningar och hoppa över innehållet. Standardvärdet är fråga. |
|**`--list-ignored-files`** | Visar en lista över filer som ignoreras under publicering, som baseras på .funcignore-filen. |
| **`--list-included-files`** | Visar en lista över filer som har publicerats som baseras på .funcignore-filen. |
| **`--nozip`** | Aktiverar standard `Run-From-Zip` läge av. |
| **`--build-native-deps`** | Hoppar över skapandet .wheels mapp när du publicerar python funktionsappar. |
| **`--additional-packages`** | Lista över paket som ska installeras när du skapar inbyggda beroenden. Till exempel: `python3-dev libevent-dev`. |
| **`--force`** | Ignorera före publicering verifiering i vissa scenarier. |
| **`--csx`** | Publicera en C#-skript (.csx) projekt. |
| **`--no-build`** | Hoppa över att skapa dotnet-funktioner. |
| **`--dotnet-cli-params`** | När publicering kompileras C# (.csproj)-funktioner, de viktigaste verktygen anropar ”dotnet build--utdata bin/publicera'. Alla parametrar som skickas till detta kommer att läggas till kommandoraden. |

### <a name="custom-container-deployment"></a>Anpassade behållardistribution

Functions kan du distribuera function-projekt i en anpassad Linux-behållare. Mer information finns i [skapa en funktion i Linux med en anpassad avbildning](functions-create-function-linux-custom-image.md). Version 2.x av Core Tools har stöd för en anpassad behållare. Anpassad behållare måste ha en Dockerfile. Använd alternativet--dockerfile på `func init`.

```bash
func deploy
```

Följande distributionsalternativ för anpassad behållare är tillgängliga:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Namnet på ett Docker-register den aktuella användaren loggat in. |
| **`--platform`** | Värdplattform för funktionsappen. Giltiga alternativ är `kubernetes` |
| **`--name`** | Funktionsappens namn. |
| **`--max`**  | Du kan också anger det maximala antalet funktionen app-instanserna ska distribueras till. |
| **`--min`**  | Du kan också anger det minsta antalet funktionen app-instanserna ska distribueras till. |
| **`--config`** | Anger en valfri distributionskonfigurationsfilen. |

## <a name="next-steps"></a>Nästa steg

Azure Functions Core Tools är [öppen källkod och finns på GitHub](https://github.com/azure/azure-functions-cli).  
Till filen en bugg eller funktionen begäran [öppna ett GitHub-ärende](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
