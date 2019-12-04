---
title: Arbeta med Azure Functions Core Tools
description: Lär dig hur du kodar och testar Azure Functions från kommando tolken eller terminalen på den lokala datorn innan du kör dem på Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 24bee8ffe23d524553143b2097560979a39329d7
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74784722"
---
# <a name="work-with-azure-functions-core-tools"></a>Arbeta med Azure Functions Core Tools

Med Azure Functions Core Tools kan du utveckla och testa dina funktioner på den lokala datorn från kommando tolken eller terminalen. Dina lokala funktioner kan ansluta till Live Azure-tjänster, och du kan felsöka funktionerna på din lokala dator med hjälp av körnings funktionen för fullständiga funktioner. Du kan även distribuera en Function-app till din Azure-prenumeration.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Genom att utveckla funktioner på den lokala datorn och publicera dem på Azure med hjälp av kärn verktygen följer du dessa grundläggande steg:

> [!div class="checklist"]
> * [Installera kärn verktygen och beroenden.](#v2)
> * [Skapa ett Function-appaket från en språkspecifik mall.](#create-a-local-functions-project)
> * [Registrera utlösare och bindnings tillägg.](#register-extensions)
> * [Definiera lagring och andra anslutningar.](#local-settings-file)
> * [Skapa en funktion från en utlösare och en språkspecifik mall.](#create-func)
> * [Kör funktionen lokalt](#start)
> * [Publicera projektet till Azure](#publish)

## <a name="core-tools-versions"></a>Core tools-versioner

Det finns två versioner av Azure Functions Core Tools. Vilken version du använder beror på din lokala utvecklings miljö, [Val av språk](supported-languages.md)och support nivå som krävs:

+ Version 1. x: stöder version 1. x av körnings miljön. Den här versionen av verktygen stöds endast på Windows-datorer och installeras från ett [NPM-paket](https://docs.npmjs.com/getting-started/what-is-npm). Med den här versionen kan du skapa funktioner i experiment språk som inte stöds av officiellt. Mer information finns [i språk som stöds i Azure Functions](supported-languages.md)

+ [Version 2. x](#v2): stöder [version 2. x av körnings miljön](functions-versions.md). Den här versionen stöder [Windows](#windows-npm), [MacOS](#brew)och [Linux](#linux). Använder plattformsspecifika paket hanterare eller NPM för installation.

Om inget annat anges är exemplen i den här artikeln för version 2. x.

## <a name="install-the-azure-functions-core-tools"></a>Installera Azure Functions Core Tools

[Azure Functions Core tools] innehåller en version av samma körnings miljö som har behörighet Azure Functions runtime som du kan köra på din lokala utvecklings dator. Den innehåller också kommandon för att skapa funktioner, ansluta till Azure och distribuera funktions projekt.

### <a name="v2"></a>Version 2. x

Version 2. x av verktygen använder Azure Functions runtime 2. x som bygger på .NET Core. Den här versionen stöds på alla plattformar .NET Core 2. x stöder, inklusive [Windows](#windows-npm), [MacOS](#brew)och [Linux](#linux). 

> [!IMPORTANT]
> Du kan kringgå kravet för att installera .NET Core 2. x SDK med hjälp av [tilläggs paket].

#### <a name="windows-npm"></a>Aktivitets

I följande steg används NPM för att installera kärn verktyg i Windows. Du kan också använda [choklad](https://chocolatey.org/). Mer information finns i Readme- [verktyg för viktiga verktyg](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Installera [Node.js], som innehåller NPM. För version 2. x av verktygen stöds endast Node. js 8,5 och senare versioner.

1. Installera paketet core tools:

    ```bash
    npm install -g azure-functions-core-tools
    ```

   Det kan ta några minuter för NPM att hämta och installera Core Tools-paketet.

1. Om du inte planerar att använda [tilläggs paket]installerar du [.net Core 2. x SDK för Windows](https://www.microsoft.com/net/download/windows).

#### <a name="brew"></a>MacOS med homebrew

I följande steg används homebrew för att installera kärn verktygen på macOS.

1. Installera [homebrew](https://brew.sh/)om den inte redan är installerad.

1. Installera paketet core tools:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

1. Om du inte planerar att använda [tilläggs paket]installerar du [.net Core 2. x SDK för MacOS](https://www.microsoft.com/net/download/macos).


#### <a name="linux"></a>Linux (Ubuntu/Debian) med APT

I följande steg används [apt](https://wiki.debian.org/Apt) för att installera kärn verktyg på din Ubuntu/Debian Linux-distribution. Andra Linux-distributioner finns i [README-verktyg](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installera GPG-nyckeln för Microsoft Package-lagringsplatsen för att verifiera paket integritet:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Konfigurera listan med .NET-utvecklings källor innan du gör en APT uppdatering.

   Kör följande kommando för att ställa in APT-källistan för Ubuntu:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Kör följande kommando för att ställa in APT-källistan för Debian:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Kontrol lera `/etc/apt/sources.list.d/dotnetdev.list`-filen för någon av de lämpligaste Linux-versions strängarna i listan nedan:

    | Linux-distribution | Version |
    | --------------- | ----------- |
    | Debian 10 | `buster` |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux mintgrön 18    | `xenial`  |

1. Starta APT-käll uppdateringen:

    ```bash
    sudo apt-get update
    ```

1. Installera paketet core tools:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Om du inte planerar att använda [tilläggs paket]installerar du [.net Core 2. x SDK för Linux](https://www.microsoft.com/net/download/linux).

## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

Projekt katalogen Functions innehåller filerna [Host. JSON](functions-host-json.md) och [Local. Settings. JSON](#local-settings-file), tillsammans med undermappar som innehåller koden för enskilda funktioner. Den här katalogen är motsvarigheten till en Function-app i Azure. Mer information om mappstrukturen för functions finns i [guiden för Azure Functions utvecklare](functions-reference.md#folder-structure).

Version 2. x kräver att du väljer ett standard språk för projektet när det initieras, och alla funktioner som har lagts till använder standard språk mal linor. I version 1. x anger du språket varje gången du skapar en funktion.

I terminalfönstret eller från en kommando tolk kör du följande kommando för att skapa projektet och den lokala git-lagringsplatsen:

```bash
func init MyFunctionProj
```

När du anger ett projekt namn skapas och initieras en ny mapp med det namnet. Annars initieras den aktuella mappen.  
När du kör kommandot i version 2. x måste du välja en körning för projektet. 

```output
Select a worker runtime:
dotnet
node
python 
powershell
```

Använd piltangenterna för att välja ett språk och tryck sedan på RETUR. Om du planerar att utveckla Java Script-eller TypeScript-funktioner väljer du **Node**och väljer sedan språket. TypeScript har [vissa ytterligare krav](functions-reference-node.md#typescript). 

Utdata ser ut som i följande exempel för ett JavaScript-projekt:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` stöder följande alternativ, som endast är version 2. x, om inget annat anges:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Initierar ett [ C# klass biblioteks projekt (. CS)](functions-dotnet-class-library.md). |
| **`--csx`** | Initierar ett [ C# skript-(. CSX)-projekt](functions-reference-csharp.md). Du måste ange `--csx` i efterföljande kommandon. |
| **`--docker`** | Skapa en Dockerfile för en behållare med hjälp av en bas avbildning som baseras på vald `--worker-runtime`. Använd det här alternativet när du planerar att publicera till en anpassad Linux-behållare. |
| **`--docker-only`** |  Lägger till en Dockerfile i ett befintligt projekt. Prompter för Worker-körning om detta inte anges eller anges i Local. Settings. JSON. Använd det här alternativet när du planerar att publicera ett befintligt projekt i en anpassad Linux-behållare. |
| **`--force`** | Initiera projektet även när det finns befintliga filer i projektet. Den här inställningen skriver över befintliga filer med samma namn. Andra filer i projektmappen påverkas inte. |
| **`--java`**  | Initierar ett [Java-projekt](functions-reference-java.md). |
| **`--javascript`**<br/>**`--node`**  | Initierar ett [JavaScript-projekt](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Förhindrar att en git-lagringsplats skapas som standard i version 1. x. Git-lagringsplatsen skapas inte som standard i version 2. x. |
| **`--powershell`**  | Initierar ett [PowerShell-projekt](functions-reference-powershell.md). |
| **`--python`**  | Initierar ett [python-projekt](functions-reference-python.md). |
| **`--source-control`** | Anger om en git-lagringsplats skapas. Som standard skapas inte en lagrings plats. När `true`skapas en lagrings plats. |
| **`--typescript`**  | Initierar ett [typescript-projekt](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Anger språk körning för projektet. De värden som stöds är: `csharp`, `dotnet`, `java`, `javascript`,`node` (Java Script), `powershell`, `python`och `typescript`. När du inte har angett uppmanas du att välja din körning under initieringen. |

> [!IMPORTANT]
> Som standard skapar version 2. x av kärn verktygen Function app-projekt för .net-körningen som [ C# klass projekt](functions-dotnet-class-library.md) (. CSPROJ). Dessa C# projekt, som kan användas med Visual Studio eller Visual Studio Code, kompileras under testning och vid publicering till Azure. Om du i stället vill skapa och arbeta med samma C# skript-filer (. CSX) som skapats i version 1. x och i portalen måste du inkludera parametern `--csx` när du skapar och distribuerar funktioner.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Som standard migreras de här inställningarna inte automatiskt när projektet publiceras till Azure. Använd `--publish-local-settings`s växeln [när du publicerar](#publish) för att se till att dessa inställningar läggs till i Function-appen i Azure. Observera att värden i **ConnectionString** aldrig publiceras.

Värdena för funktionen app-inställningar kan också läsas i koden som miljövariabler. Mer information finns i avsnittet miljövariabler i de här språkspecifika referens avsnitten:

* [C#förkompilerade](functions-dotnet-class-library.md#environment-variables)
* [C#skript (. CSX)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

När ingen giltig lagrings anslutnings sträng har angetts för [`AzureWebJobsStorage`] och emulatorn inte används visas följande fel meddelande:

> Värde saknas för AzureWebJobsStorage i Local. Settings. JSON. Detta krävs för alla andra utlösare än HTTP. Du kan köra "FUNC Azure functionapp Fetch-App-Settings \<functionAppName\>" eller ange en anslutnings sträng i Local. Settings. JSON.

### <a name="get-your-storage-connection-strings"></a>Hämta anslutnings strängar för lagring

Även om du använder Storage-emulatorn för utveckling kanske du vill testa med en faktisk lagrings anslutning. Förutsatt att du redan har [skapat ett lagrings konto](../storage/common/storage-create-storage-account.md)kan du hämta en giltig lagrings anslutnings sträng på något av följande sätt:

- Sök efter och välj **lagrings konton**från [Azure-portalen]. 
  ![väljer du lagrings konton från Azure Portal](./media/functions-run-local/select-storage-accounts.png)
  
  Välj ditt lagrings konto, Välj **åtkomst nycklar** i **Inställningar**och kopiera sedan ett av värdena för **anslutnings strängen** .
  ![kopiera anslutnings strängen från Azure Portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Använd [Azure Storage Explorer](https://storageexplorer.com/) för att ansluta till ditt Azure-konto. I **Utforskaren**expanderar du din prenumeration, väljer ditt lagrings konto och kopierar den primära eller sekundära anslutnings strängen.

  ![Kopiera anslutnings strängen från Storage Explorer](./media/functions-run-local/storage-explorer.png)

+ Använd kärn verktyg för att hämta anslutnings strängen från Azure med något av följande kommandon:

  + Hämta alla inställningar från en befintlig Function-app:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Hämta anslutnings strängen för ett angivet lagrings konto:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    När du inte redan har loggat in på Azure uppmanas du att göra det.

## <a name="create-func"></a>Skapa en funktion

Skapa en funktion genom att köra följande kommando:

```bash
func new
```

När du kör `func new` i version 2. x, uppmanas du att välja en mall på standard språket för din Function-app. därefter uppmanas du också att välja ett namn för din funktion. I version 1. x uppmanas du också att välja språk.

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

Funktions koden skapas i en undermapp med det tillhandahållna funktions namnet, som du kan se i följande utlösare för kön:

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
| **`--csx`** | (Version 2. x) Genererar samma C# skript-mallar (. CSX) som används i version 1. x och i portalen. |
| **`--language -l`**| Programmeringsspråket för mallar, till exempel C#, F#eller Java Script. Det här alternativet krävs i version 1. x. Använd inte det här alternativet i version 2. x eller Välj ett språk som matchar arbets körningen. |
| **`--name -n`** | Funktions namnet. |
| **`--template -t`** | Använd kommandot `func templates list` för att se en fullständig lista över tillgängliga mallar för varje språk som stöds.   |

Om du till exempel vill skapa en JavaScript-HTTP-utlösare i ett enda kommando kör du:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

För att skapa en kö-utlöst funktion i ett enda kommando, kör:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Köra funktioner lokalt

Kör Functions-värden om du vill köra ett Functions-projekt. Värden aktiverar utlösare för alla funktioner i projektet. 

### <a name="version-2x"></a>Version 2. x

I version 2. x av körnings miljön varierar Start kommandot beroende på ditt projekt språk.

#### <a name="c"></a>C\#

```command
func start --build
```

#### <a name="javascript"></a>JavaScript

```command
func start
```

#### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

### <a name="version-1x"></a>Version 1. x

Version 1. x av Functions-körningen kräver kommandot `host`, som i följande exempel:

```command
func host start
```

`func start` stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Skapa inte aktuellt projekt innan det körs. Endast för dotNET-projekt. Standardvärdet är inställt på falskt. Endast version 2. x. |
| **`--cert`** | Sökvägen till en. pfx-fil som innehåller en privat nyckel. Används endast med `--useHttps`. Endast version 2. x. |
| **`--cors-credentials`** | Tillåt kors ursprung autentiserade begär Anden (dvs. cookies och Authentication-huvudet) version 2. x. |
| **`--cors`** | En kommaavgränsad lista med CORS-ursprung, utan blank steg. |
| **`--language-worker`** | Argument för att konfigurera språk arbets tagaren. Endast version 2. x. |
| **`--nodeDebugPort -n`** | Porten för noden som fel sökare ska använda. Standard: ett värde från Launch. JSON eller 5858. Endast version 1. x. |
| **`--password`** | Antingen lösen ordet eller en fil som innehåller lösen ordet för en PFX-fil. Används endast med `--cert`. Endast version 2. x. |
| **`--port -p`** | Den lokala porten att lyssna på. Standardvärde: 7071. |
| **`--pause-on-error`** | Pausa för ytterligare indatatyper innan du avslutar processen. Används endast när du startar kärn verktyg från en Integrated Development Environment (IDE).|
| **`--script-root --prefix`** | Används för att ange sökvägen till roten för Function-appen som ska köras eller distribueras. Detta används för kompilerade projekt som genererar projektfiler i en undermapp. När du till exempel skapar ett C# klass biblioteks projekt skapas värden. JSON, Local. Settings. JSON och function. JSON-filerna i en *rotmapp* med en sökväg som `MyProject/bin/Debug/netstandard2.0`. I det här fallet ställer du in prefixet som `--script-root MyProject/bin/Debug/netstandard2.0`. Detta är roten i Function-appen när du kör i Azure. |
| **`--timeout -t`** | Tids gränsen för funktionens värd att starta, i sekunder. Standard: 20 sekunder.|
| **`--useHttps`** | Bind till `https://localhost:{port}` i stället för att `http://localhost:{port}`. Som standard skapar det här alternativet ett betrott certifikat på din dator.|

När Functions-värden startar matar den in URL: en för HTTP-utlösta funktioner:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>När det körs lokalt tillämpas inte autentisering för HTTP-slutpunkter. Det innebär att alla lokala HTTP-begäranden hanteras som `authLevel = "anonymous"`. Mer information finns i artikeln om [http-bindning](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Skicka test data till en funktion

Om du vill testa dina funktioner lokalt [startar du Functions-värden](#start) och anropar slut punkter på den lokala servern med HTTP-begäranden. Slut punkten som du anropar beror på typen av funktion.

>[!NOTE]
> Exemplen i det här avsnittet använder verktyget för att skicka HTTP-förfrågningar från terminalen eller en kommando tolk. Du kan använda ett verktyg som du väljer för att skicka HTTP-begäranden till den lokala servern. Verktyget vänd är tillgängligt som standard på Linux-baserade system och Windows 10 version 17063 och senare. I äldre fönster måste du först hämta och installera [verktyget sväng](https://curl.haxx.se/).

Mer allmän information om testning av funktioner finns [i strategier för att testa koden i Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funktioner som utlöses av HTTP och webhook

Du anropar följande slut punkt för att köra HTTP-och webhook-utlösta funktioner lokalt:

    http://localhost:{port}/api/{function_name}

Se till att använda samma server namn och port som värden lyssnar på. Du ser detta i de utdata som genereras när du startar funktions värden. Du kan anropa den här URL: en med hjälp av en HTTP-metod som stöds av utlösaren.

Följande spiral kommando utlöser `MyHttpTrigger` snabb starts funktion från en GET-begäran med den _namn_ parameter som angavs i frågesträngen.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Följande exempel är samma funktion som anropas från ett POST-begärans överförings _namn_ i begär ande texten:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Du kan göra GET-begäranden från en webbläsare som skickar data i frågesträngen. För alla andra HTTP-metoder måste du använda sväng, Fiddler, Postman eller ett liknande verktyg för HTTP-testning.

#### <a name="non-http-triggered-functions"></a>Funktioner som inte är HTTP-utlösta

För alla typer av funktioner än HTTP-utlösare och Webhooks kan du testa dina funktioner lokalt genom att anropa en administrations slut punkt. Anropet till den här slut punkten med en HTTP POST-begäran på den lokala servern utlöser funktionen. Du kan också skicka test data till körningen i bröd texten i POST-begäran. Den här funktionen liknar fliken **test** i Azure Portal.

Du anropar följande administratörs slut punkt för att utlösa icke-HTTP-funktioner:

    http://localhost:{port}/admin/functions/{function_name}

Om du vill skicka test data till administratörs slut punkten för en funktion måste du ange data i bröd texten i ett meddelande om POST-begäran. Meddelande texten måste ha följande JSON-format:

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>`-värdet innehåller data i ett format som förväntas av funktionen. Följande spiral exempel är ett inlägg i en `QueueTriggerJS`-funktion. I det här fallet är indatamängden en sträng som motsvarar det meddelande som förväntas finnas i kön.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Använda kommandot `func run` i version 1. x

>[!IMPORTANT]
> Kommandot `func run` stöds inte i version 2. x av verktygen. Mer information finns i avsnittet [How to target Azure Functions runtime-versioner](set-runtime-version.md).

Du kan också anropa en funktion direkt genom att använda `func run <FunctionName>` och ange indata för funktionen. Det här kommandot liknar att köra en funktion med hjälp av fliken **test** i Azure Portal.

`func run` stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Infogat innehåll. |
| **`--debug -d`** | Koppla en fel sökare till värd processen innan du kör funktionen.|
| **`--timeout -t`** | Vänte tiden (i sekunder) tills den lokala funktionens värd är klar.|
| **`--file -f`** | Fil namnet som ska användas som innehåll.|
| **`--no-interactive`** | Kräver inte någon indatamängd. Användbart för automatiserings scenarier.|

Om du till exempel vill anropa en HTTP-utlöst funktion och skicka innehålls texten kör du följande kommando:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publicera till Azure

Azure Functions Core Tools stöder två typer av distribution: Distribuera Function Project-filer direkt till din Function-app via [zip Deploy](functions-deployment-technologies.md#zip-deploy) och [distribuera en anpassad Docker-behållare](functions-deployment-technologies.md#docker-container). Du måste redan ha [skapat en Function-app i din Azure-prenumeration](functions-cli-samples.md#create), som du ska distribuera din kod till. Projekt som kräver kompilering bör skapas så att binärfilerna kan distribueras.

En projektmapp kan innehålla språkspecifika filer och kataloger som inte ska publiceras. Undantagna objekt visas i en. funcignore-fil i rotmappen.     

### <a name="project-file-deployment"></a>Distribution (projektfiler)

Om du vill publicera din lokala kod i en Function-app i Azure använder du kommandot `publish`:

```bash
func azure functionapp publish <FunctionAppName>
```

Det här kommandot publicerar till en befintlig Function-app i Azure. Du får ett fel meddelande om du försöker publicera till en `<FunctionAppName>` som inte finns i din prenumeration. Information om hur du skapar en Function-app från kommando tolken eller terminalfönstret med hjälp av Azure CLI finns i [skapa en Funktionsapp för Server lös körning](./scripts/functions-cli-create-serverless.md). Som standard använder detta kommando [fjärrversion](functions-deployment-technologies.md#remote-build) och distribuerar din app för [körning från distributions paketet](run-functions-from-deployment-package.md). Om du vill inaktivera det rekommenderade distributions läget använder du alternativet `--nozip`.

>[!IMPORTANT]
> När du skapar en Function-app i Azure Portal, använder den version 2. x av funktions körning som standard. Om du vill att Function-appen ska använda version 1. x av körnings miljön följer du anvisningarna i [köra på version 1. x](functions-versions.md#creating-1x-apps).
> Du kan inte ändra körnings versionen för en Function-app som har befintliga funktioner.

Följande publicerings alternativ gäller för båda versionerna, 1. x och 2. x:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publicera inställningar i Local. Settings. JSON till Azure och du ombeds skriva över om inställningen redan finns. Om du använder Storage-emulatorn måste du först ändra appens inställning till en [faktisk lagrings anslutning](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Utelämna uppmaningen att skriva över appinställningar när `--publish-local-settings -i` används.|

Följande publicerings alternativ stöds bara i version 2. x:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Publicera bara inställningar och hoppa över innehållet. Standardvärdet är prompt. |
|**`--list-ignored-files`** | Visar en lista över filer som ignoreras under publicering, som baseras på. funcignore-filen. |
| **`--list-included-files`** | Visar en lista över publicerade filer, som baseras på. funcignore-filen. |
| **`--nozip`** | Stänger av standard `Run-From-Package`s läget. |
| **`--build-native-deps`** | Hoppar över genereringen av. Wheels-mappen när du publicerar python Function-appar. |
| **`--build`**<br/>**`-b`** | Utför Bygg åtgärd när du distribuerar till en Linux Function-app. Accepterar: `remote` och `local`. |
| **`--additional-packages`** | Lista över paket som ska installeras när du skapar interna beroenden. Till exempel: `python3-dev libevent-dev`. |
| **`--force`** | Ignorera för publicerings verifiering i vissa scenarier. |
| **`--csx`** | Publicera ett C# skript (. CSX)-projekt. |
| **`--no-build`** | Bygg inte funktioner i .NET-klass bibliotek. |
| **`--dotnet-cli-params`** | När du publicerar C# kompilerade (. CSPROJ)-funktioner anropar kärn verktygen "dotNet build--output bin/Publish". Alla parametrar som skickas till detta läggs till i kommando raden. |

### <a name="deployment-custom-container"></a>Distribution (anpassad behållare)

Med Azure Functions kan du distribuera ett funktions projekt i en [anpassad Docker-behållare](functions-deployment-technologies.md#docker-container). Mer information finns i [skapa en funktion i Linux med en anpassad avbildning](functions-create-function-linux-custom-image.md). Anpassade behållare måste ha en Dockerfile. Om du vill skapa en app med en Dockerfile använder du alternativet--Dockerfile på `func init`.

```bash
func deploy
```

Följande alternativ för distribution av anpassade behållare är tillgängliga:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Namnet på ett Docker-register som den aktuella användaren har loggat in på. |
| **`--platform`** | Värd plattform för Function-appen. Giltiga alternativ är `kubernetes` |
| **`--name`** | Funktionens namn på appen. |
| **`--max`**  | Om du vill kan du ange det maximala antalet funktions-App-instanser som ska distribueras till. |
| **`--min`**  | Om du vill kan du ange det minsta antalet funktions-App-instanser som ska distribueras till. |
| **`--config`** | Anger en valfri distributions konfigurations fil. |

## <a name="monitoring-functions"></a>Övervaknings funktioner

Det rekommenderade sättet att övervaka körningen av dina funktioner är genom att integrera med Azure Application insikter. Du kan också strömma körnings loggar till den lokala datorn. Mer information finns i [övervaka Azure Functions](functions-monitoring.md).

### <a name="enable-application-insights-integration"></a>Aktivera Application Insights-integrering

När du skapar en Function-app i Azure Portal görs Application Insights-integration som standard. Men när du skapar en Function-app med hjälp av Azure CLI, är integrationen i din Function-app i Azure inte färdig.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Aktivera strömnings loggar

Du kan visa en ström med loggfiler som genereras av dina funktioner i en kommando rad session på den lokala datorn. 

#### <a name="native-streaming-logs"></a>Interna strömmande loggar

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Den här typen av strömnings loggar kräver att du [aktiverar Application Insights integrering](#enable-application-insights-integration) för din Function-app.   


## <a name="next-steps"></a>Nästa steg

Lär dig hur du utvecklar, testar och publicerar Azure Functions med Azure Functions Core Tools [Microsoft lära](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools är [öppen källkod och finns på GitHub](https://github.com/azure/azure-functions-cli).  
Om du vill skicka en fel-eller funktions förfrågan [öppnar du ett GitHub-problem](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portalen]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[tilläggs paket]: functions-bindings-register.md#extension-bundles
