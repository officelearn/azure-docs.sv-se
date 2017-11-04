---
title: "Utveckla och kör lokalt Azure functions | Microsoft Docs"
description: "Lär dig hur du code och testa Azure functions lokalt på datorn innan du kör dem på Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 35fd47025ca0dba1edbe1d7dd3ee0172fc45d6f5
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="code-and-test-azure-functions-locally"></a>Platskod och testa Azure Functions lokalt

När den [Azure-portalen] ger en fullständig uppsättning av verktyg för att utveckla och testa Azure Functions, många utvecklare föredrar en lokal utveckling upplevelse. Azure Functions gör det enkelt att använda din favorit redigerare och lokala utvecklingsverktyg att utveckla och testa dina funktioner på den lokala datorn. Dina funktioner kan utlösa händelser i Azure och du kan felsöka ditt C# och JavaScript-funktioner på den lokala datorn. 

Om du är en Visual Studio C# utvecklare Azure Functions även [kan integreras med Visual Studio 2017](functions-develop-vs.md).

## <a name="install-the-azure-functions-core-tools"></a>Installera Azure Functions Core-verktyg

[Azure Functions grundläggande verktyg] är en lokal version av Azure Functions-runtime som du kan köra på utvecklingsdatorn lokala. Det är inte en emulator eller simulatorn. Det är samma körningsmiljön som stänger fungerar i Azure. Det finns två versioner av Azure Functions Core verktyg, en för version 1.x av runtime och en för version 2.x. Båda versionerna tillhandahålls som ett [npm paketet](https://docs.npmjs.com/getting-started/what-is-npm).

>[!NOTE]  
> Innan du installerar någon version, måste du [installera NodeJS](https://docs.npmjs.com/getting-started/installing-node), som innehåller npm. För version 2.x verktyg, endast Node.js 8.5 och senare versioner stöds. 

### <a name="version-1x-runtime"></a>Version 1.x runtime

Den ursprungliga versionen av verktygen använder funktioner 1.x runtime. Den här versionen använder .NET Framework och stöds endast på Windows-datorer. Använd följande kommando för att installera version 1.x-verktyg:

```bash
npm install -g azure-functions-core-tools
```

### <a name="version-2x-runtime"></a>Version 2.x runtime

Version 2.x av verktygen använder Azure Functions-runtime 2.x som bygger på .NET Core. Den här versionen stöds för alla plattformar som .NET Core 2.x stöder. Den här versionen används för utveckling av plattformar och när Functions-runtime 2.x krävs. 

>[!IMPORTANT]   
> Innan du installerar Azure Functions grundläggande verktyg [installera .NET Core 2.0](https://www.microsoft.com/net/core).  
>
> Azure Functions-runtime 2.0 är en förhandsversion och stöds för närvarande inte alla funktioner i Azure Functions. Mer information finns i [Azure Functions-runtime 2.0 kända problem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues) 

 Använd följande kommando för att installera verktygen för version 2.0:

```bash
npm install -g azure-functions-core-tools@core
```

När du installerar på Ubuntu Använd `sudo`enligt följande:

```bash
sudo npm install -g azure-functions-core-tools@core
```

När du installerar i macOS och Linux, du kan behöva ta de `unsafe-perm` flaggan på följande sätt:

```bash
sudo npm install -g azure-functions-core-tools@core --unsafe-perm true
```

## <a name="run-azure-functions-core-tools"></a>Köra verktyg för Azure Functions kärnor
 
Azure Functions grundläggande verktyg lägger du till följande kommando alias:
* **funktionen**
* **azfun**
* **azurefunctions**

Något av dessa alias kan användas där `func` illustreras i exemplen.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Skapa ett projekt med lokala funktioner

När du kör lokalt funktioner projektet är en katalog som innehåller filerna [host.json](functions-host-json.md) och [local.settings.json](#local-settings). Den här katalogen är motsvarigheten till en funktionsapp i Azure. Läs mer om Azure Functions mappstrukturen i den [Azure Functions utvecklarguide för](functions-reference.md#folder-structure).

Kör följande kommando för att skapa projektet och lokal Git-lagringsplats i fönstret terminal eller från en kommandotolk:

```
func init MyFunctionProj
```

Resultatet ser ut som i följande exempel:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Använd för att skapa projektet utan en lokal Git-lagringsplats i `--no-source-control [-n]` alternativet.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>Lokala inställningsfilen

Filen local.settings.json lagrar app-inställningar, anslutningssträngar och inställningar för Azure Functions Core verktyg. Det har följande struktur:

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
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
| **Värden** | Samling av programinställningar som används när du kör lokalt. **AzureWebJobsStorage** och **AzureWebJobsDashboard** är exempel, en fullständig lista, se [app inställningsreferens](functions-app-settings.md).  |
| **Värden** | Inställningarna i det här avsnittet Anpassa värdprocess funktioner när du kör lokalt. | 
| **LocalHttpPort** | Anger den standardport som används när du kör den lokala värden funktioner (`func host start` och `func run`). Den `--port` kommandoradsalternativet har högre prioritet än detta värde. |
| **CORS** | Definierar det ursprung som tillåts för [resursdelning för korsande ursprung (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Ursprung tillhandahålls som en kommaavgränsad lista med inga blanksteg. Jokertecknet (**\***) stöds, vilket gör att begäranden från alla ursprung. |
| **ConnectionStrings** | Innehåller databasanslutningssträngar för dina funktioner. Anslutningssträngar i det här objektet har lagts till i miljön med providern **System.Data.SqlClient**.  | 

De flesta utlösare och bindningar har en **anslutning** egenskap som mappas till namnet på en miljöinställning för variabeln eller app. För varje anslutning-egenskap måste det finnas appinställningen som definierats i local.settings.json-filen. 

De här inställningarna kan också läsa i koden som miljövariabler. I C#, använda [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) eller [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx). I JavaScript, använda `process.env`. Inställningarna som en systemmiljövariabler åsidosätter värden i filen local.settings.json. 

Inställningarna i filen local.settings.json används endast av funktioner verktyg när du kör lokalt. Som standard dessa inställningar migreras inte automatiskt när projektet har publicerats till Azure. Använd den `--publish-local-settings` växla [när du publicerar](#publish) att se till att dessa inställningar har lagts till i funktionsapp i Azure.

Om ingen giltig lagringsanslutningssträng anges för **AzureWebJobsStorage**, visas följande felmeddelande:  

>Värde saknas för AzureWebJobsStorage i local.settings.json. Detta krävs för alla utlösare än HTTP. Du kan köra ' func azure functionapp fetch-app-inställningar <functionAppName>' eller ange en anslutningssträng i local.settings.json.
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Konfigurera appinställningar

För att ange ett värde för anslutningssträngar, kan du göra något av följande alternativ:
* Ange anslutningssträng från [Azure Lagringsutforskaren](http://storageexplorer.com/).
* Använd någon av följande kommandon:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    Båda kommandon måste du först logga in till Azure.

<a name="create-func"></a>
## <a name="create-a-function"></a>Skapa en funktion

Om du vill skapa en funktion, kör du följande kommando:

```
func new
``` 
`func new`stöder följande valfria argument:

| Argumentet     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Den mall som programmeringsspråket, till exempel C#, F # eller JavaScript. |
| **`--template -t`** | Mallnamnet. |
| **`--name -n`** | Namnet på funktionen. |

Till exempel för att skapa en JavaScript-HTTP-utlösare, kör du:

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Om du vill skapa en funktion som utlöses av kön, kör du:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>Kör funktioner lokalt

För att köra ett funktioner projekt, kör du funktioner värden. Värden kan utlösare för alla funktioner i projektet:

```
func host start
```

`func host start`stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Den lokala porten lyssna på. Standardvärde: 7071. |
| **`--debug <type>`** | Alternativen är `VSCode` och `VS`. |
| **`--cors`** | En kommaavgränsad lista över CORS ursprung, utan blanksteg. |
| **`--nodeDebugPort -n`** | Porten för felsökaren nod att använda. Standard: Ett värde från launch.json eller 5858. |
| **`--debugLevel -d`** | Spårningsnivån konsolen (av, verbose, info, warning eller error). Standard: Info.|
| **`--timeout -t`** | Tidsgräns för funktioner värden startas, i sekunder. Standard: 20 sekunder.|
| **`--useHttps`** | Binda till https://localhost: {port} i stället för till http://localhost: {port}. Som standard skapas ett betrott certifikat på datorn.|
| **`--pause-on-error`** | Pausa för ytterligare information innan du avslutar processen. Användbar när den startas Azure Functions grundläggande verktyg från en integrerad utvecklingsmiljö (IDE).|

När värden funktioner startar anger URL: en för HTTP-utlösta funktioner:

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Felsöka i VS-kod eller Visual Studio

Om du vill koppla en felsökare skickar den `--debug` argumentet. Använd Visual Studio-koden för att felsöka JavaScript-funktioner. Använda Visual Studio för C#.

Om du vill felsöka C#-funktioner, Använd `--debug vs`. Du kan också använda [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Om du vill starta värden och Ställ in JavaScript-felsökning, kör du:

```
func host start --debug vscode
```

I Visual Studio-koden i den **felsöka** väljer **ansluta till Azure Functions**. Du kan koppla brytpunkter inspektera variabler och stega igenom koden.

![JavaScript-felsökning med Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

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

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
Följande exempel är samma funktion som anropas från en POST-begäran skickas _namn_ i frågans brödtext:

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Observera att du HÄMTAR förfrågningar från en webbläsare överföring av data i frågesträngen. För alla andra HTTP-metoder, måste du använda cURL, Fiddler, Postman eller ett liknande tester HTTP-verktyg.  

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

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Med den `func run` i version 1.x

>[!IMPORTANT]  
> Den `func run` kommandot stöds inte i version 2.x verktyg. Mer information finns i avsnittet [så avsedda för Azure Functions-runtime versioner](functions-versions.md).

Du kan även anropa en funktion direkt med hjälp av `func run <FunctionName>` och ange indata för funktionen. Det här kommandot liknar kör en funktion med hjälp av den **Test** fliken i Azure-portalen. 

`func run`stöder följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Infogade innehållet. |
| **`--debug -d`** | Koppla en felsökare till värdprocessen innan du kör funktionen.|
| **`--timeout -t`** | Tid för att vänta (i sekunder) till den lokala värden funktioner är klar.|
| **`--file -f`** | Filnamnet för att använda innehåll.|
| **`--no-interactive`** | Begär inte indata. Bra automation.|

Till exempel för att anropa en funktion som utlöses av HTTP och skicka innehållet, kör du följande kommando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publicera till Azure

Om du vill publicera ett projekt för funktioner till en funktionsapp i Azure, använda den `publish` kommando:

```
func azure functionapp publish <FunctionAppName>
```

Du kan använda följande alternativ:

| Alternativ     | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publiceringsinställningar i local.settings.json till Azure, där du uppmanas att skriva över om inställningen finns redan.|
| **`--overwrite-settings -y`** | Måste användas med `-i`. Skriver över AppSettings i Azure med lokalt värde om olika. Standardvärdet är fråga.|

Det här kommandot publicerar till en befintlig funktionsapp i Azure. Ett fel uppstår när den `<FunctionAppName>` finns inte i din prenumeration. Information om hur du skapar en funktionsapp från Kommandotolken eller terminalfönster med hjälp av Azure CLI finns [skapa en Funktionsapp för serverlösa körning](./scripts/functions-cli-create-serverless.md).

Den `publish` kommandot Överför innehållet i projektkatalogen funktioner. Om du tar bort filer lokalt på `publish` kommandot tar inte bort dem från Azure. Du kan ta bort filer i Azure med hjälp av den [Kudu verktyget](functions-how-to-use-azure-function-app-settings.md#kudu) i den [Azure-portalen].  

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
[Azure-portalen]: https://portal.azure.com 
