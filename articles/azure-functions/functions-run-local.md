---
title: Arbeta med Azure Functions core-verktyg
description: Lär dig hur du kodar och testar Azure-funktioner från kommandotolken eller terminalen på den lokala datorn innan du kör dem på Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 19691a654162ee3855cb257fd42e29d2e1fc0157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276665"
---
# <a name="work-with-azure-functions-core-tools"></a>Arbeta med Azure Functions core-verktyg

Med Azure Functions Core Tools kan du utveckla och testa dina funktioner på din lokala dator från kommandotolken eller terminalen. Dina lokala funktioner kan ansluta till live Azure-tjänster och du kan felsöka dina funktioner på din lokala dator med hjälp av den fullständiga funktionerna. Du kan även distribuera en funktionsapp till din Azure-prenumeration.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Utveckla funktioner på din lokala dator och publicera dem i Azure med hjälp av Core Tools följer dessa grundläggande steg:

> [!div class="checklist"]
> * [Installera kärnverktyg och beroenden.](#v2)
> * [Skapa ett funktionsappprojekt från en språkspecifik mall.](#create-a-local-functions-project)
> * [Registrera utlösar- och bindningstillägg.](#register-extensions)
> * [Definiera lagring och andra anslutningar.](#local-settings-file)
> * [Skapa en funktion från en utlösare och språkspecifik mall.](#create-func)
> * [Kör funktionen lokalt.](#start)
> * [Publicera projektet i Azure.](#publish)

## <a name="core-tools-versions"></a>Core Tools-versioner

Det finns tre versioner av Azure Functions Core Tools. Vilken version du använder beror på din lokala utvecklingsmiljö, [språkval](supported-languages.md)och supportnivå som krävs:

+ **Version 1.x**: Stöder version 1.x av Azure Functions runtime. Den här versionen av verktygen stöds bara på Windows-datorer och installeras från ett [npm-paket](https://www.npmjs.com/package/azure-functions-core-tools).

+ [**Version 2.x/3.x**](#v2): Stöder [version 2.x eller 3.x av Azure Functions runtime](functions-versions.md). Dessa versioner stöder [Windows,](/azure/azure-functions/functions-run-local?tabs=windows#v2) [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)och [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) och använder plattformsspecifika pakethanterare eller npm för installation.

Om inget annat anges är exemplen i den här artikeln för version 3.x.

## <a name="install-the-azure-functions-core-tools"></a>Installera Azure Functions Core Tools

[Azure Functions Core Tools] innehåller en version av samma körning som driver Azure Functions-körning som du kan köra på din lokala utvecklingsdator. Den innehåller också kommandon för att skapa funktioner, ansluta till Azure och distribuera funktionsprojekt.

>[!IMPORTANT]
>Du måste ha [Azure CLI](/cli/azure/install-azure-cli) installerat lokalt för att kunna publicera till Azure från Azure Functions Core Tools.  

### <a name="version-2x-and-3x"></a><a name="v2"></a>Version 2.x och 3.x

Version 2.x/3.x av verktygen använder azure functions-körningen som bygger på .NET Core. Den här versionen stöds på alla plattformar .NET Core stöder, inklusive [Windows,](/azure/azure-functions/functions-run-local?tabs=windows#v2) [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)och [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2). 

> [!IMPORTANT]
> Du kan kringgå kravet för att installera .NET Core SDK med hjälp av [tilläggspaket].

# <a name="windows"></a>[Windows](#tab/windows)

Följande steg använder npm för att installera Core Tools i Windows. Du kan också använda [Chocolatey](https://chocolatey.org/). Mer information finns i [läsminnet](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)för kärnverktyg .

1. Installera [Node.js], som innehåller npm.
    - För version 2.x av verktygen stöds endast Node.js 8.5 och senare versioner.
    - För version 3.x av verktygen stöds endast Node.js 10 och senare versioner.

1. Installera paketet Core Tools:

    ##### <a name="v2x"></a>v2.x

    ```cmd
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```cmd
    npm install -g azure-functions-core-tools@3
    ```

   Det kan ta några minuter innan npm hämtar och installerar Core Tools-paketet.

1. Om du inte tänker använda [tilläggspaket]installerar du [.NET Core 2.x SDK för Windows](https://www.microsoft.com/net/download/windows).

# <a name="macos"></a>[Macos](#tab/macos)

Följande steg använder Homebrew för att installera Core Tools på macOS.

1. Installera [Homebrew](https://brew.sh/), om det inte redan är installerat.

1. Installera paketet Core Tools:

    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

Följande steg använder [APT](https://wiki.debian.org/Apt) för att installera Core Tools på din Ubuntu/Debian Linux-distribution. För andra Linux-distributioner, se [Core Tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installera GPG-nyckeln för Microsoft-paketarkivet för att verifiera paketets integritet:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Konfigurera .NET-utvecklingskällalistan innan du gör en APT-uppdatering.

   Om du vill ställa in APT-källlistan för Ubuntu kör du det här kommandot:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Om du vill ställa in APT-källlistan för Debian kör du det här kommandot:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Kontrollera `/etc/apt/sources.list.d/dotnetdev.list` filen för en av lämpliga Linux-version strängar som anges nedan:

    | Linux-distribution | Version |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux Mynta 18    | `xenial`  |

1. Starta uppdateringen av APT-källa:

    ```bash
    sudo apt-get update
    ```

1. Installera paketet Core Tools:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Om du inte planerar att använda [tilläggspaket]installerar du [.NET Core 2.x SDK för Linux](https://www.microsoft.com/net/download/linux).

---

## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

En funktionsprojektkatalog innehåller filerna [host.json](functions-host-json.md) och [local.settings.json](#local-settings-file), tillsammans med undermappar som innehåller koden för enskilda funktioner. Den här katalogen motsvarar en funktionsapp i Azure. Mer information om mappstrukturen Funktioner finns i [Azure Functions-utvecklarguiden](functions-reference.md#folder-structure).

Version 2.x kräver att du väljer ett standardspråk för projektet när det initieras. I version 2.x använder alla tillagda funktioner standardspråkmallar. I version 1.x anger du språket varje gång du skapar en funktion.

I terminalfönstret eller från en kommandotolk kör du följande kommando för att skapa projektet och den lokala Git-databasen:

```
func init MyFunctionProj
```

När du anger ett projektnamn skapas och initieras en ny mapp med det namnet. Annars initieras den aktuella mappen.  
I version 2.x måste du välja en körning för projektet när du kör kommandot. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Använd upp-/nedpiltangenterna för att välja ett språk och tryck sedan på Retur. Om du planerar att utveckla JavaScript- eller TypeScript-funktioner väljer du **nod**och väljer sedan språket. TypeScript har [några ytterligare krav](functions-reference-node.md#typescript). 

Utdata ser ut som följande exempel för ett JavaScript-projekt:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init`stöder följande alternativ, som endast är version 2.x, om inget annat anges:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Initierar ett [C#-klassbibliotek (.cs) projekt](functions-dotnet-class-library.md). |
| **`--csx`** | Initierar ett [C#-skript (.csx) projekt](functions-reference-csharp.md). Du måste `--csx` ange i efterföljande kommandon. |
| **`--docker`** | Skapa en Dockerfile för en behållare med hjälp `--worker-runtime`av en basavbildning som baseras på den valda . Använd det här alternativet när du planerar att publicera till en anpassad Linux-behållare. |
| **`--docker-only`** |  Lägger till en Dockerfile i ett befintligt projekt. Frågar efter arbetarkörningen om den inte anges eller anges i local.settings.json. Använd det här alternativet när du planerar att publicera ett befintligt projekt i en anpassad Linux-behållare. |
| **`--force`** | Initiera projektet även när det finns befintliga filer i projektet. Den här inställningen skriver över befintliga filer med samma namn. Andra filer i projektmappen påverkas inte. |
| **`--java`**  | Initierar ett [Java-projekt](functions-reference-java.md). |
| **`--javascript`**<br/>**`--node`**  | Initierar ett [JavaScript-projekt](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Förhindrar att en Git-databas skapas i version 1.x som standard. I version 2.x skapas inte git-databasen som standard. |
| **`--powershell`**  | Initierar ett [PowerShell-projekt](functions-reference-powershell.md). |
| **`--python`**  | Initierar ett [Python-projekt](functions-reference-python.md). |
| **`--source-control`** | Styr om en git-databas skapas. Som standard skapas ingen databas. När `true`skapas en databas. |
| **`--typescript`**  | Initierar ett [TypeScript-projekt](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Anger språkkörning för projektet. Värden som `csharp`stöds `dotnet` `java`är:`node` , , `powershell` `python`, `javascript` `typescript`,(JavaScript), och . När du inte har angett uppmanas du att välja din körning under initieringen. |

> [!IMPORTANT]
> Som standard skapar version 2.x av Kärnverktygen funktionsappprojekt för .NET-körningen som [C#-klassprojekt](functions-dotnet-class-library.md) (.csproj). Dessa C#-projekt, som kan användas med Visual Studio eller Visual Studio Code, kompileras under testning och när du publicerar till Azure. Om du i stället vill skapa och arbeta med samma C#script (.csx) filer som skapats `--csx` i version 1.x och i portalen, måste du inkludera parametern när du skapar och distribuerar funktioner.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Som standard migreras inte dessa inställningar automatiskt när projektet publiceras i Azure. Använd `--publish-local-settings` växeln [när du publicerar](#publish) för att se till att dessa inställningar läggs till i funktionsappen i Azure. Observera att värden i **ConnectionStrings aldrig publiceras.**

Värdena för funktionsappinställningar kan också läsas i koden som miljövariabler. Mer information finns i avsnittet Miljövariabler i de här språkspecifika referensavsnitten:

* [C# förkompilerad](functions-dotnet-class-library.md#environment-variables)
* [C#-skript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

När ingen giltig lagringsanslutningssträng är inställd för [`AzureWebJobsStorage`] och emulatorn inte används visas följande felmeddelande:

> Värde saknas för AzureWebJobsStorage i local.settings.json. Detta krävs för alla utlösare förutom HTTP. Du kan köra 'func azure functionapp \<fetch-app-settings functionAppName\>' eller ange en anslutningssträng i local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Hämta dina lagringsanslutningssträngar

Även när du använder Microsoft Azure Storage Emulator för utveckling, kanske du vill testa med en faktisk lagringsanslutning. Om du antar att du redan har [skapat ett lagringskonto](../storage/common/storage-create-storage-account.md)kan du få en giltig lagringsanslutningssträng på något av följande sätt:

- Sök efter och välj **Lagringskonton**i [Azure-portalen]. 
  ![Välj Lagringskonton från Azure-portalen](./media/functions-run-local/select-storage-accounts.png)
  
  Välj ditt lagringskonto, välj **Access-nycklar** i **Inställningar**och kopiera sedan ett av **anslutningssträngvärdena.**
  ![Kopiera anslutningssträng från Azure Portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Använd [Azure Storage Explorer](https://storageexplorer.com/) för att ansluta till ditt Azure-konto. Expandera prenumerationen i **Utforskaren,** expandera **Lagringskonton,** välja ditt lagringskonto och kopiera den primära eller sekundära anslutningssträngen.

  ![Kopiera anslutningssträng från Lagringsutforskaren](./media/functions-run-local/storage-explorer.png)

+ Använd Core Tools för att hämta anslutningssträngen från Azure med något av följande kommandon:

  + Ladda ned alla inställningar från en befintlig funktionsapp:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Hämta anslutningssträngen för ett visst lagringskonto:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    När du inte redan är inloggad på Azure uppmanas du att göra det.

## <a name="create-a-function"></a><a name="create-func"></a>Skapa en funktion

Skapa en funktion genom att köra följande kommando:

```
func new
```

I version 2.x uppmanas `func new` du att välja en mall på standardspråket för funktionsappen när du kör. I version 1.x uppmanas du också att välja språk.

<pre>
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
</pre>

Funktionskod genereras i en undermapp med det angivna funktionsnamnet, som du kan se i följande köutlösningsutdata:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Du kan också ange dessa alternativ i kommandot med hjälp av följande argument:

| Argument     | Beskrivning                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Version 2.x) Genererar samma C#script (.csx) mallar som används i version 1.x och i portalen. |
| **`--language`**, **`-l`**| Mallens programmeringsspråk, till exempel C#, F#eller JavaScript. Det här alternativet krävs i version 1.x. Använd inte det här alternativet i version 2.x eller välj ett språk som matchar arbetarkörningen. |
| **`--name`**, **`-n`** | Funktionsnamnet. |
| **`--template`**, **`-t`** | Använd `func templates list` kommandot för att se en fullständig lista över tillgängliga mallar för varje språk som stöds.   |

Om du till exempel vill skapa en JavaScript HTTP-utlösare i ett enda kommando kör du:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Om du vill skapa en köutlöst funktion i ett enda kommando kör du:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Kör funktioner lokalt

Om du vill köra ett Functions-projekt kör du programvärden Funktioner. Värden aktiverar utlösare för alla funktioner i projektet. Startkommandot varierar beroende på projektspråket.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```
# <a name="javascript"></a>[Javascript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Det här kommandot måste [köras i en virtuell miljö](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Version 1.x av funktionskörningen `host` kräver kommandot, som i följande exempel:
>
> ```
> func host start
> ```

`func start`stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Gör inget byggströmsprojekt innan du kör. Endast för dotnet-projekt. Standard är inställt på false. Stöds inte för version 1.x. |
| **`--cert`** | Sökvägen till en PFX-fil som innehåller en privat nyckel. Används endast `--useHttps`med . Stöds inte för version 1.x. |
| **`--cors-credentials`** | Tillåt autentiserade begäranden med kors ursprung (dvs. cookies och autentiseringshuvudet) Stöds inte för version 1.x. |
| **`--cors`** | En kommaavgränsad lista över CORS-ursprung, utan mellanslag. |
| **`--language-worker`** | Argument för att konfigurera språkarbetaren. Du kan till exempel aktivera felsökning för språkarbetare genom att tillhandahålla [felsökningsport och andra nödvändiga argument](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Stöds inte för version 1.x. |
| **`--nodeDebugPort`**, **`-n`** | Porten för nod.js-felsökningen att använda. Standard: Ett värde från launch.json eller 5858. Endast version 1.x. |
| **`--password`** | Antingen lösenordet eller en fil som innehåller lösenordet för en PFX-fil. Används endast `--cert`med . Stöds inte för version 1.x. |
| **`--port`**, **`-p`** | Den lokala hamnen att lyssna på. Standardvärde: 7071. |
| **`--pause-on-error`** | Pausa för ytterligare indata innan du avslutar processen. Används endast när du startar Core Tools från en integrerad utvecklingsmiljö (IDE).|
| **`--script-root`**, **`--prefix`** | Används för att ange sökvägen till roten för funktionsappen som ska köras eller distribueras. Detta används för kompilerade projekt som genererar projektfiler till en undermapp. När du till exempel skapar ett C#-klassbiblioteksprojekt genereras värden.json-, local.settings.json- och function.json-filerna `MyProject/bin/Debug/netstandard2.0`i en *rotundermapp* med en bana som . I det här fallet anger `--script-root MyProject/bin/Debug/netstandard2.0`du prefixet som . Detta är roten till funktionsappen när du kör i Azure. |
| **`--timeout`**, **`-t`** | Tidsgränsen för den funktioner som är värd för att starta, på några sekunder. Standard: 20 sekunder.|
| **`--useHttps`** | Bind `https://localhost:{port}` till i `http://localhost:{port}`stället för till . Som standard skapar det här alternativet ett betrott certifikat på datorn.|

När functions-värden startar matar den ut URL:en för HTTP-utlösta funktioner:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>När du kör lokalt tillämpas inte auktorisering för HTTP-slutpunkter. Det innebär att alla lokala `authLevel = "anonymous"`HTTP-begäranden hanteras som . Mer information finns i [HTTP-bindningsartikeln](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Att skicka testdata till en funktion

Om du vill testa dina funktioner lokalt startar du [programvärden för funktioner](#start) och anropar slutpunkter på den lokala servern med HJÄLP av HTTP-begäranden. Vilken slutpunkt du anropar beror på vilken typ av funktion det är.

>[!NOTE]
> Exempel i det här avsnittet använder cURL-verktyget för att skicka HTTP-begäranden från terminalen eller en kommandotolk. Du kan använda ett verktyg som du väljer för att skicka HTTP-begäranden till den lokala servern. CURL-verktyget är tillgängligt som standard på Linux-baserade system och Windows 10 build 17063 och senare. På äldre Windows måste du först hämta och installera [cURL-verktyget](https://curl.haxx.se/).

Mer allmän information om testfunktioner finns i [Strategier för testning av din kod i Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>HTTP- och webhook-utlösta funktioner

Du anropar följande slutpunkt för att köra HTTP- och webhook-utlösta funktioner lokalt:

    http://localhost:{port}/api/{function_name}

Se till att använda samma servernamn och port som programledaren för Funktioner lyssnar på. Detta visas i utdata som genereras när funktionsvärden startas. Du kan anropa den här URL:en med valfri HTTP-metod som stöds av utlösaren.

Följande cURL-kommando utlöser `MyHttpTrigger` snabbstartsfunktionen från en GET-begäran med _namnparametern_ som skickas i frågesträngen.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Följande exempel är samma funktion som anropas från ett POST-begäran som skickar _namn_ i begäranden:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Du kan göra GET-begäranden från en webbläsare som skickar data i frågesträngen. För alla andra HTTP-metoder måste du använda cURL, Fiddler, Postman eller ett liknande HTTP-testverktyg.

#### <a name="non-http-triggered-functions"></a>Icke-HTTP-utlösta funktioner

För alla typer av funktioner än HTTP-utlösare och webhooks och Event Grid-utlösare kan du testa dina funktioner lokalt genom att anropa en administrationsslutpunkt. Om du anropar den här slutpunkten med en HTTP POST-begäran på den lokala servern utlöses funktionen. 

Information om hur du testar händelserutlösande funktioner lokalt finns i [Lokal testning med visningswebbapp](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Du kan eventuellt skicka testdata till körningen i brödtexten för POST-begäran. Den här funktionen liknar fliken **Testa** i Azure-portalen.

Du anropar följande administratörsslutpunkt för att utlösa icke-HTTP-funktioner:

    http://localhost:{port}/admin/functions/{function_name}

Om du vill skicka testdata till administratörsslutpunkten för en funktion måste du ange data i brödtexten i ett post-begärandemeddelande. Meddelandetexten måste ha följande JSON-format:

```JSON
{
    "input": "<trigger_input>"
}
```

Värdet `<trigger_input>` innehåller data i ett format som förväntas av funktionen. Följande cURL-exempel är ett `QueueTriggerJS` POST till en funktion. I det här fallet är indata en sträng som motsvarar det meddelande som förväntas hittas i kön.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Använda `func run` kommandot (endast version 1.x)

>[!IMPORTANT]
> Kommandot `func run` stöds endast i version 1.x av verktygen. Mer information finns i avsnittet [Så här riktar du in dig på Azure Functions runtime-versioner](set-runtime-version.md).

I version 1.x kan du också anropa `func run <FunctionName>` en funktion direkt genom att använda och tillhandahålla indata för funktionen. Det här kommandot liknar att köra en funktion med hjälp av fliken **Testa** i Azure-portalen.

`func run`stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Infogat innehåll. |
| **`--debug`**, **`-d`** | Bifoga en felsökningssökning till värdprocessen innan du kör funktionen.|
| **`--timeout`**, **`-t`** | Dags att vänta (i sekunder) tills den lokala functionsvärden är klar.|
| **`--file`**, **`-f`** | Filnamnet som ska användas som innehåll.|
| **`--no-interactive`** | Frågar inte efter indata. Användbart för automatiseringsscenarier.|

Om du till exempel vill anropa en HTTP-utlöst funktion och skicka innehållstext kör du följande kommando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Publicera till Azure

Azure Functions Core Tools stöder två typer av distribution: distribuera funktionsprojektfiler direkt till din funktionsapp via [Zip Deploy](functions-deployment-technologies.md#zip-deploy) och [distribuera en anpassad Docker-behållare](functions-deployment-technologies.md#docker-container). Du måste redan ha [skapat en funktionsapp i din Azure-prenumeration](functions-cli-samples.md#create), som du ska distribuera din kod till. Projekt som kräver kompilering bör skapas så att binärfilerna kan distribueras.

>[!IMPORTANT]
>Du måste ha [Azure CLI](/cli/azure/install-azure-cli) installerat lokalt för att kunna publicera till Azure från Core Tools.  

En projektmapp kan innehålla språkspecifika filer och kataloger som inte bör publiceras. Uteslutna objekt visas i en .funcignore-fil i rotprojektmappen.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Distribuera projektfiler

Om du vill publicera din lokala kod `publish` i en funktionsapp i Azure använder du kommandot:

```
func azure functionapp publish <FunctionAppName>
```

Det här kommandot publiceras till en befintlig funktionsapp i Azure. Du får ett felmeddelande om du försöker `<FunctionAppName>` publicera till en som inte finns i din prenumeration. Mer information om hur du skapar en funktionsapp från kommandotolken eller terminalfönstret med Hjälp av Azure CLI finns i [Skapa en funktionsapp för serverlös körning](./scripts/functions-cli-create-serverless.md). Som standard använder det här kommandot [fjärrbygge](functions-deployment-technologies.md#remote-build) och distribuerar din app för att [köras från distributionspaketet](run-functions-from-deployment-package.md). Om du vill inaktivera det `--nozip` här rekommenderade distributionsläget använder du alternativet.

>[!IMPORTANT]
> När du skapar en funktionsapp i Azure-portalen används version 2.x av funktionskörningen som standard. Om du vill att funktionsappen ska använda version 1.x av körningen följer du instruktionerna i [Kör på version 1.x](functions-versions.md#creating-1x-apps).
> Du kan inte ändra körningsversionen för en funktionsapp som har befintliga funktioner.

Följande publiceringsalternativ gäller för båda versionerna, 1.x och 2.x:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publicera inställningar i local.settings.json till Azure, vilket uppmanas att skriva över om inställningen redan finns. Om du använder Microsoft Azure Storage Emulator ändrar du först appinställningen till en [faktisk lagringsanslutning](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Undertryck prompten om du `--publish-local-settings -i` vill skriva över appinställningarna när den används.|

Följande publiceringsalternativ stöds bara i version 2.x:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Publicera bara inställningar och hoppa över innehållet. Standard är fråga. |
|**`--list-ignored-files`** | Visar en lista över filer som ignoreras under publiceringen, som baseras på .funcignore-filen. |
| **`--list-included-files`** | Visar en lista över filer som publiceras, som baseras på .funcignore-filen. |
| **`--nozip`** | Stänger av `Run-From-Package` standardläget. |
| **`--build-native-deps`** | Hoppar över att generera .wheels-mappen när Python-funktionsappar publiceras. |
| **`--build`**, **`-b`** | Utför byggåtgärd när du distribuerar till en Linux-funktionsapp. Accepterar: `remote` `local`och . |
| **`--additional-packages`** | Lista över paket som ska installeras när du skapar inbyggda beroenden. Till exempel: `python3-dev libevent-dev`. |
| **`--force`** | Ignorera verifiering före publicering i vissa scenarier. |
| **`--csx`** | Publicera ett C#-skript (.csx)-projekt. |
| **`--no-build`** | Bygg inte .NET-klassbiblioteksfunktioner. |
| **`--dotnet-cli-params`** | Vid publicering kompilerade C# (.csproj) funktioner, de centrala verktygen kallar dotnet bygga --utdata bin / publicera.When publishing compiled C# (.csproj) functions, the core tools calls 'dotnet build --output bin/publish'. Alla parametrar som skickas till detta läggs till på kommandoraden. |

### <a name="deploy-custom-container"></a>Distribuera anpassad behållare

Med Azure Functions kan du distribuera ditt funktionsprojekt i en [anpassad Docker-behållare](functions-deployment-technologies.md#docker-container). Mer information finns i [Skapa en funktion på Linux med hjälp av en anpassad avbildning](functions-create-function-linux-custom-image.md). Anpassade behållare måste ha en Dockerfile. Om du vill skapa en app med en Dockerfile `func init`använder du alternativet --dockerfile på .

```
func deploy
```

Följande anpassade distributionsalternativ för behållare är tillgängliga:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Namnet på ett Docker-register som den aktuella användaren har loggat in på. |
| **`--platform`** | Värdplattform för funktionsappen. Giltiga alternativ är`kubernetes` |
| **`--name`** | Funktionsappens namn. |
| **`--max`**  | Du kan också ange det maximala antalet funktionsappinstanser som ska distribueras till. |
| **`--min`**  | Du kan också ange det minsta antalet funktionsappinstanser som ska distribueras till. |
| **`--config`** | Anger en valfri distributionskonfigurationsfil. |

## <a name="monitoring-functions"></a>Övervakningsfunktioner

Det rekommenderade sättet att övervaka körningen av dina funktioner är genom att integrera med Azure Application Insights. Du kan också strömma körningsloggar till din lokala dator. Mer information finns i [Övervaka Azure-funktioner](functions-monitoring.md).

### <a name="application-insights-integration"></a>Integrering av Application Insights

Application Insights-integrering bör aktiveras när du skapar din funktionsapp i Azure. Om din funktionsapp av någon anledning inte är ansluten till en Application Insights-instans är det enkelt att göra den här integreringen i Azure-portalen. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Aktivera strömmande loggar

Du kan visa en ström av loggfiler som genereras av dina funktioner i en kommandoradssession på den lokala datorn. 

#### <a name="native-streaming-logs"></a>Inbyggda strömmande loggar

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Den här typen av direktuppspelningsloggar kräver att Application Insights-integrering aktiveras för din funktionsapp.   


## <a name="next-steps"></a>Nästa steg

Lär dig hur du utvecklar, testar och publicerar Azure-funktioner med hjälp av Azure Functions Core Tools [Microsoft learn module](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools är öppen källkod och finns på [GitHub](https://github.com/azure/azure-functions-cli).  
Om du vill skicka en fel- eller funktionsbegäran [öppnar du ett GitHub-problem](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Grundläggande verktyg för Azure-funktioner]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
["AzureWebJobsStorage"]: functions-app-settings.md#azurewebjobsstorage
[förlängningspaket]: functions-bindings-register.md#extension-bundles
