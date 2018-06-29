---
title: Arbeta med Azure Functions grundläggande verktyg | Microsoft Docs
description: Lär dig mer om att koda och testa Azure functions från Kommandotolken eller terminal på din lokala dator innan du kör dem på Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/26/2018
ms.author: glenga
ms.openlocfilehash: 5c582b080ec6f2cff801758fc4bff4f7d07fd7df
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083077"
---
# <a name="work-with-azure-functions-core-tools"></a>Arbeta med Azure Functions grundläggande verktyg

Azure Functions Core-verktyg kan du utveckla och testa dina funktioner på den lokala datorn från Kommandotolken eller terminal. Din lokala funktioner kan ansluta till live Azure-tjänster och du kan felsöka dina funktioner på den lokala datorn med fullständig Functions-runtime. Du kan även distribuera en funktionsapp till din Azure-prenumeration.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Core verktyg versioner

Det finns två versioner av Azure Functions grundläggande verktyg. Den version som du använder beror på din lokala utvecklingsmiljö, val av språk och supportnivå som krävs:

+ [Version 1.x](#v1): har stöd för version 1.x av körningsmiljön, vilket är normalt tillgängliga (GA). Den här versionen av verktygen stöds bara på Windows-datorer och installeras från en [npm paketet](https://docs.npmjs.com/getting-started/what-is-npm). Med den här versionen kan du skapa funktioner i experiment språk som inte stöds officiellt. Mer information finns i [språk som stöds i Azure Functions](supported-languages.md)

+ [Version 2.x](#v2): har stöd för version 2.x av körningsmiljön. Den här versionen stöder [Windows](#windows-npm), [macOS](#brew), och [Linux](#linux). Använder plattformsspecifika paketet chefer eller npm för installation. Som 2.x-runtime är den här versionen av grundläggande verktyg för närvarande i förhandsgranskningen.

Om inget annat anges i exemplen i den här artikeln gäller version 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Installera Azure Functions Core Tools

[Azure Functions grundläggande verktyg] innehåller en version av samma körningsmiljön som används av Azure Functions-runtime som du kan köra på utvecklingsdatorn lokala. Det ger också kommandon för att skapa funktioner, ansluta till Azure och distribuera funktionen projekt.

### <a name="v1"></a>Version 1.x

Den ursprungliga versionen av verktygen använder funktioner 1.x runtime. Den här versionen använder .NET Framework (4.7.1) och stöds endast på Windows-datorer. Innan du installerar verktygen version 1.x, måste du [installera NodeJS](https://docs.npmjs.com/getting-started/installing-node), som innehåller npm.

Använd följande kommando för att installera version 1.x-verktyg:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Version 2.x

>[!NOTE]
> Azure Functions-runtime 2.0 är en förhandsversion och stöds för närvarande inte alla funktioner i Azure Functions. Mer information finns i [Azure Functions-versioner](functions-versions.md) 

Version 2.x av verktygen använder Azure Functions-runtime 2.x som bygger på .NET Core. Den här versionen stöds för alla plattformar som .NET Core 2.x har stöd för, inklusive [Windows](#windows-npm), [macOS](#brew), och [Linux](#linux).

#### <a name="windows-npm"></a>Windows

Följande steg kan du använda npm för att installera grundläggande verktyg i Windows. Du kan också använda [Chocolatey](https://chocolatey.org/). Mer information finns i [grundläggande verktyg viktigt](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Installera [.NET Core 2.0 för Windows](https://www.microsoft.com/net/download/windows).

2. Installera [Node.js], som innehåller npm. För version 2.x verktyg, endast Node.js 8.5 och senare versioner stöds.

3. Installera paketet Verktyg för kärnor:

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>MacOS med Homebrew

Följande steg använda Homebrew för att installera hanteringsverktygen kärnor på macOS.

1. Installera [.NET Core 2.0 för macOS](https://www.microsoft.com/net/download/macos).

2. Installera [Homebrew](https://brew.sh/), om den inte redan är installerad.

3. Installera paketet Verktyg för kärnor:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Debian/Ubuntu) med LGH

Följande steg används [LGH](https://wiki.debian.org/Apt) installera grundläggande verktyg på Ubuntu/Debian Linux-distribution. Andra Linux-distributioner finns i [grundläggande verktyg viktigt](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installera [.NET Core 2.0 för Linux](https://www.microsoft.com/net/download/linux).

2. Registrera Microsoft-produktnyckeln som tillförlitliga:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Kontrollera Ubuntu server kör en lämplig version i tabellen nedan. Om du vill lägga till lgh källan, kör du:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Linux-distribution | Version |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu nr 17.04 från    | `zesty`     |
    | Ubuntu 16.04/Linux myntverket 18    | `xenial`  |

4. Installera paketet Verktyg för kärnor:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Skapa ett projekt med lokala funktioner

En funktion projektkatalogen innehåller filer [host.json](functions-host-json.md) och [local.settings.json](#local-settings-file), längs undermappar som innehåller koden för enskilda funktioner. Den här katalogen är motsvarigheten till en funktionsapp i Azure. Mer information om mappstrukturen funktioner finns i [Azure Functions utvecklarguide för](functions-reference.md#folder-structure).

Version 2.x måste du välja ett standardspråk för ditt projekt när den har initierats och alla funktioner läggs till standard språkmallar. I version 1.x, du ange språk när du skapar en funktion.

Kör följande kommando för att skapa projektet och lokal Git-lagringsplats i fönstret terminal eller från en kommandotolk:

```bash
func init MyFunctionProj
```

I version 2.x, när du kör kommandot du måste välja en runtime för projektet. Om du planerar att utveckla JavaScript-funktioner, välja **noden**:

```output
Select a worker runtime:
dotnet
node
```

Använd upp/ned piltangenterna för att välja språk och tryck sedan på RETUR. Resultatet ser ut som följande exempel för ett JavaScript-projekt:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

Använd för att skapa projektet utan en lokal Git-lagringsplats i `--no-source-control [-n]` alternativet.

## <a name="register-extensions"></a>Registrera tillägg

I version 2.x av Azure Functions-runtime måste du registrera bindning-tillägg (bindningstyper) som du använder i appen funktion.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Mer information finns i [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Lokala inställningsfilen

Filen local.settings.json lagrar app-inställningar, anslutningssträngar och inställningar för Azure Functions Core verktyg. Det har följande struktur:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

| Inställning      | Beskrivning                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Om värdet är **SANT**, alla värden som är krypterade med en lokal dator-nyckel. Används med `func settings` kommandon. Standardvärdet är **FALSKT**. |
| **Värden** | Samling programinställningar och anslutningssträngar som används när du kör lokalt. Dessa värden motsvarar app-inställningar i appen funktionen i Azure, som **AzureWebJobsStorage** och **AzureWebJobsDashboard**. Många utlösare och bindningar har en egenskap som refererar till en appinställningen för anslutningssträngen, exempelvis **anslutning** för den [Blob storage utlösaren](functions-bindings-storage-blob.md#trigger---configuration). För sådana egenskaper, behöver du en tillämpningsinställning som definierats i den **värden** matris. <br/>**AzureWebJobsStorage** är en obligatorisk appinställning för utlösare än HTTP. När du har den [Azure storage-emulatorn](../storage/common/storage-use-emulator.md) installeras lokalt, du kan ställa in **AzureWebJobsStorage** till `UseDevelopmentStorage=true` och grundläggande verktygen använder emulatorn. Detta är användbart vid utveckling, men du bör testa med en anslutning för faktiska lagringsplatsen före distributionen. |
| **Värd** | Inställningarna i det här avsnittet Anpassa värdprocess funktioner när du kör lokalt. |
| **LocalHttpPort** | Anger den standardport som används när du kör den lokala värden funktioner (`func host start` och `func run`). Den `--port` kommandoradsalternativet har högre prioritet än detta värde. |
| **CORS** | Definierar det ursprung som tillåts för [resursdelning för korsande ursprung (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Ursprung tillhandahålls som en kommaavgränsad lista med inga blanksteg. Jokertecknet (\*) stöds, vilket gör att begäranden från alla ursprung. |
| **ConnectionStrings** | Använd inte den här samlingen för anslutningssträngar som används av din funktionsbindning. Den här samlingen används endast av ramverk som måste få anslutningssträngar från den **ConnectionStrings** avsnitt i en konfiguration av fil, som [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Anslutningssträngar i det här objektet har lagts till i miljön med providern [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Objekt i samlingen inte har publicerats till Azure med andra appinställningar. Du måste uttryckligen lägga till dessa värden till den **anslutningssträngar** avsnitt i den **programinställningar** för din funktionsapp. |

Funktionen app inställningsvärden kan också läsa i koden som miljövariabler. Mer information finns i avsnittet miljö variabler i referensavsnitten språkspecifika:

+ [C#-förkompilerat](functions-dotnet-class-library.md#environment-variables)
+ [C#-skript (.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Inställningarna i filen local.settings.json används endast av funktioner verktyg när du kör lokalt. Som standard dessa inställningar migreras inte automatiskt när projektet har publicerats till Azure. Använd den `--publish-local-settings` växla [när du publicerar](#publish) att se till att dessa inställningar har lagts till i funktionsapp i Azure. Värdena i **ConnectionStrings** aldrig har publicerats.

Om ingen giltig lagringsanslutningssträng anges för **AzureWebJobsStorage** och emulatorn inte används, visas följande felmeddelande:  

>Värde saknas för AzureWebJobsStorage i local.settings.json. Detta krävs för alla utlösare än HTTP. Du kan köra ' func azure functionapp fetch-app-inställningar <functionAppName>' eller ange en anslutningssträng i local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Hämta storage-anslutningssträngar

Även när du använder storage-emulatorn för utveckling, kanske du vill testa med en anslutning för faktiska lagringsplatsen. Anta att du har redan [skapat ett lagringskonto](../storage/common/storage-create-storage-account.md), du kan hämta en giltig lagringsanslutningssträng i något av följande sätt:

+ Från den [Azure Portal]. Navigera till ditt lagringskonto, Välj **åtkomstnycklar** i **inställningar**, kopiera en av de **anslutningssträngen** värden.

  ![Kopiera anslutningssträngen från Azure-portalen](./media/functions-run-local/copy-storage-connection-portal.png)

+ Använd [Azure Lagringsutforskaren](http://storageexplorer.com/) att ansluta till ditt Azure-konto. I den **Explorer**, expandera din prenumeration, Välj ditt lagringskonto och kopiera den primära eller sekundära anslutningssträngen. 

  ![Kopiera anslutningssträngen från Lagringsutforskaren](./media/functions-run-local/storage-explorer.png)

+ Använda grundläggande verktyg för att hämta anslutningssträngen från Azure med något av följande kommandon:

    + Hämta alla inställningar från en befintlig funktionsapp:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + Hämta anslutningssträngen för ett visst lagringskonto:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    
    När du redan inte är inloggad till Azure, uppmanas du att göra detta.

## <a name="create-func"></a>Skapa en funktion

Om du vill skapa en funktion, kör du följande kommando:

```bash
func new
```

I version 2.x, när du kör `func new` du uppmanas att välja en mall på standardspråket för funktionen appen och du uppmanas också att ange ett namn för din funktion. I version 1.x, du uppmanas också att välja önskat språk.

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

Funktionskoden genereras i en undermapp med det angivna funktionsnamnet som du ser i följande kö utlösaren utdata:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Du kan också ange alternativen i kommandot med följande argument:

| Argumentet     | Beskrivning                            |
| ------------------------------------------ | -------------------------------------- |
| **`--language -l`**| Den mall som programmeringsspråket, till exempel C#, F # eller JavaScript. Det här alternativet är obligatoriskt i version 1.x. I version 2.x kan inte använda det här alternativet eller Välj standardspråk för projektet. |
| **`--template -t`** | Det mallnamn som kan vara något av värdena:<br/><ul><li>`Blob trigger`</li><li>`Cosmos DB trigger`</li><li>`Event Grid trigger`</li><li>`HTTP trigger`</li><li>`Queue trigger`</li><li>`SendGrid`</li><li>`Service Bus Queue trigger`</li><li>`Service Bus Topic trigger`</li><li>`Timer trigger`</li></ul> |
| **`--name -n`** | Namnet på funktionen. |

Till exempel för att skapa en JavaScript-HTTP-utlösare i ett enda kommando, kör du:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Om du vill skapa en funktion som utlöses av kön i ett enda kommando kör du:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Kör funktioner lokalt

För att köra ett funktioner projekt, kör du funktioner värden. Värden kan utlösare för alla funktioner i projektet:

```bash
func host start
```

`func host start` stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Den lokala porten lyssna på. Standardvärde: 7071. |
| **`--debug <type>`** | Värden med debug-port öppnas så att du kan ansluta till den **func.exe** processer från [Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) eller [Visual Studio 2017](functions-dotnet-class-library.md). Den *\<typen\>* alternativ är `VSCode` och `VS`.  |
| **`--cors`** | En kommaavgränsad lista över CORS ursprung, utan blanksteg. |
| **`--nodeDebugPort -n`** | Porten för felsökaren nod att använda. Standard: Ett värde från launch.json eller 5858. |
| **`--debugLevel -d`** | Spårningsnivån konsolen (av, verbose, info, warning eller error). Standard: Info.|
| **`--timeout -t`** | Tidsgräns för funktioner värden startas, i sekunder. Standard: 20 sekunder.|
| **`--useHttps`** | Binda till `https://localhost:{port}` i stället för till `http://localhost:{port}`. Som standard skapas ett betrott certifikat på datorn.|
| **`--pause-on-error`** | Pausa för ytterligare information innan du avslutar processen. Används när den startas Core verktyg från Visual Studio eller VS-kod.|

När värden funktioner startar anger URL: en för HTTP-utlösta funktioner:

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="passing-test-data-to-a-function"></a>Skicka test-data till en funktion

Att testa dina funktioner lokalt kan du [startar funktioner värden](#start) och anropa slutpunkter på den lokala servern med hjälp av HTTP-begäranden. Den slutpunkt du anropa beror på vilken typ av funktionen.

>[!NOTE]  
> Exemplen i det här avsnittet använda verktyget cURL för att skicka HTTP-begäranden från terminalen eller Kommandotolken. Du kan använda ett verktyg du väljer för att skicka HTTP-begäranden till den lokala servern. CURL-verktyget finns som standard på Linux-baserade system. I Windows, måste du först hämta och installera den [cURL verktyget](https://curl.haxx.se/).

Mer allmän information om hur du testar funktioner finns [strategier för att testa din kod i Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>HTTP- och webhook utlöses funktioner

Du kan anropa följande slutpunkt som kör lokalt HTTP och webhook utlöses funktioner:

    http://localhost:{port}/api/{function_name}

Se till att använda samma servernamn och port som funktioner värden lyssnar på. Detta ser du i utdata som genereras när du startar funktionen värden. Du kan anropa denna URL med någon HTTP-metod som stöds av utlösaren. 

Följande cURL-kommando utlösare i `MyHttpTrigger` quickstart funktion från en GET-begäran med den _namn_ -parametern som angavs i frågesträngen. 

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
Följande exempel är samma funktion som anropas från en POST-begäran skickas _namn_ i frågans brödtext:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Du kan hämta begäranden från en webbläsare överföring av data i frågesträngen. För alla andra HTTP-metoder, måste du använda cURL, Fiddler, Postman eller ett liknande tester HTTP-verktyg.  

#### <a name="non-http-triggered-functions"></a>Icke-HTTP-utlösta funktioner

Du kan testa dina funktioner för alla typer av funktioner än http-utlösare och webhooks lokalt genom att anropa en slutpunkt för administration. Anropar den här slutpunkten med en HTTP POST-begäran på den lokala servern utlöser funktionen. Du kan eventuellt överföra testdata att körningen i brödtexten för POST-begäran. Den här funktionen liknar den **Test** fliken i Azure-portalen.  

Du kan anropa följande administratör slutpunkt för att utlösa icke-HTTP-funktioner:

    http://localhost:{port}/admin/functions/{function_name}

Du måste ange data i brödtexten i en POST-förfrågan om du vill skicka testdata till administratören slutpunkten för en funktion. Meddelandetexten måste ha följande JSON-format:

```JSON
{
    "input": "<trigger_input>"
}
````

Den `<trigger_input>` värdet innehåller data i ett format som förväntades av funktionen. Följande cURL-exempel är en POST till en `QueueTriggerJS` funktion. I det här fallet är indata en sträng som motsvarar det meddelande som förväntas finnas i kön.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Med den `func run` i version 1.x

>[!IMPORTANT]  
> Den `func run` kommandot stöds inte i version 2.x verktyg. Mer information finns i avsnittet [så avsedda för Azure Functions-runtime versioner](set-runtime-version.md).

Du kan även anropa en funktion direkt med hjälp av `func run <FunctionName>` och ange indata för funktionen. Det här kommandot liknar kör en funktion med hjälp av den **Test** fliken i Azure-portalen. 

`func run` stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Infogade innehållet. |
| **`--debug -d`** | Koppla en felsökare till värdprocessen innan du kör funktionen.|
| **`--timeout -t`** | Tid för att vänta (i sekunder) till den lokala värden funktioner är klar.|
| **`--file -f`** | Filnamnet för att använda innehåll.|
| **`--no-interactive`** | Begär inte indata. Bra automation.|

Till exempel för att anropa en funktion som utlöses av HTTP och skicka innehållet, kör du följande kommando:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Visa loggfiler lokalt

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Publicera till Azure

Om du vill publicera ett projekt för funktioner till en funktionsapp i Azure, använda den `publish` kommando:

```bash
func azure functionapp publish <FunctionAppName>
```

Du kan använda följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publiceringsinställningar i local.settings.json till Azure, där du uppmanas att skriva över om inställningen finns redan. Om du använder lagringsemulatorn kan du ändra appinställningen en [anslutning faktiska lagringsplatsen](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Måste användas med `-i`. Skriver över AppSettings i Azure med lokalt värde om olika. Standardvärdet är fråga.|

Det här kommandot publicerar till en befintlig funktionsapp i Azure. Ett fel uppstår när den `<FunctionAppName>` finns inte i din prenumeration. Information om hur du skapar en funktionsapp från Kommandotolken eller terminalfönster med hjälp av Azure CLI finns [skapa en Funktionsapp för serverlösa körning](./scripts/functions-cli-create-serverless.md).

Den `publish` kommandot Överför innehållet i projektkatalogen funktioner. Om du tar bort filer lokalt på `publish` kommandot tar inte bort dem från Azure. Du kan ta bort filer i Azure med hjälp av den [Kudu verktyget](functions-how-to-use-azure-function-app-settings.md#kudu) i den [Azure Portal].  

>[!IMPORTANT]  
> När du skapar en funktionsapp i Azure används version 1.x av funktionen körningsmiljön som standard. För att funktionen Använd programversion 2.x av körningsmiljön, Lägg till inställningen `FUNCTIONS_EXTENSION_VERSION=beta`.  
Använd följande kod i Azure CLI för att lägga till den här inställningen i appen funktionen:

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Nästa steg

Azure Functions grundläggande verktyg är [öppna datakällan och finns på GitHub](https://github.com/azure/azure-functions-cli).  
Till filen en bugg eller funktion begäran [öppna ett problem med GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions grundläggande verktyg]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
