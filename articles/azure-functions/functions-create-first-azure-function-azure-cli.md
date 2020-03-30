---
title: Skapa en funktion i Azure som svarar på HTTP-begäranden
description: Lär dig hur du skapar en funktion från kommandoraden och publicerar sedan det lokala projektet till serverlös värd i Azure Functions.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 89b6a9f31414cbaa9cc92c1a0d881a1354180990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282740"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Snabbstart: Skapa en funktion i Azure som svarar på HTTP-begäranden

I den här artikeln använder du kommandoradsverktyg för att skapa en funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverlösa miljön i Azure Functions. Om du slutför den här snabbstarten medför en liten kostnad på några USD-cent eller mindre i ditt Azure-konto.

Det finns också en [Visual Studio-kodbaserad version](functions-create-first-function-vs-code.md) av den här artikeln.

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.7.1846 eller en senare 2.x-version.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 och 3.7 kräver [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.7.1846 eller en senare 2.x-version. Python 3.8 kräver [version 3.x](./functions-run-local.md#v2) av core tools.
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.0.76 eller senare. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js,](https://nodejs.org/)Active LTS och Underhåll LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-382/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/), som stöds av Azure Functions (x64).
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Kontrollera din miljö

+ I ett terminal- eller `func --version` kommandofönster kör du för att kontrollera att Azure Functions Core Tools är version 2.7.1846 eller en senare 2.x-version.

+ Kör `az --version` för att kontrollera att Azure CLI-versionen är 2.0.76 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Kör `node --version` för att kontrollera node.js-versionen rapporterar 8.x eller 10.x.
::: zone-end
::: zone pivot="programming-language-python"
+ Kör `python --version` (Linux/MacOS) `py --version` eller (Windows) för att kontrollera dina Python-versionsrapporter 3.8.x, 3.7.x eller 3.6.x.

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Skapa och aktivera en virtuell miljö

I en lämplig mapp kör du följande kommandon för `.venv`att skapa och aktivera en virtuell miljö med namnet . Var noga med att använda Python 3.8, 3.7 eller 3.6, som stöds av Azure Functions.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Om Python inte installerade venv-paketet på din Linux-distribution kör du följande kommando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Du kör alla efterföljande kommandon i den här aktiverade virtuella miljön. (Kör `deactivate`.) Om du vill avsluta den virtuella miljön.

::: zone-end

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktionsprojekt

I Azure Functions är ett funktionsprojekt en behållare för en eller flera enskilda funktioner som var och en svarar på en viss utlösare. Alla funktioner i ett projekt har samma lokala konfigurationer och värdkonfigurationer. I det här avsnittet skapar du ett funktionsprojekt som innehåller en enda funktion.

1. I den virtuella miljön `func init` kör du kommandot för att skapa ett funktionsprojekt i en mapp med namnet *LocalFunctionProj* med den angivna körningen:

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionProj --python
    ```
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionProj --dotnet
    ```
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionProj --javascript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionProj --typescript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionProj --powershell
    ```
    ::: zone-end


    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler med namnet [local.settings.json](functions-run-local.md#local-settings-file) och [host.json](functions-host-json.md). Eftersom *local.settings.json* kan innehålla hemligheter som hämtats från Azure, är filen undantagen från källkontrollen som standard i *.gitignore-filen.*

1. Navigera till projektmappen:

    ```
    cd LocalFunctionProj
    ```
    
1. Lägg till en funktion i projektet med `--name` hjälp av följande kommando, där `--template` argumentet är det unika namnet på din funktion och argumentet anger funktionens utlösare. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new`skapar en HttpExample.cs kodfil.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new`skapar en undermapp som matchar funktionsnamnet som innehåller en kodfil som är lämplig för projektets valda språk och en konfigurationsfil med namnet *function.json*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>(Valfritt) Granska filinnehållet

Om du vill kan du hoppa till [Kör funktionen lokalt](#run-the-function-locally) och undersöka filinnehållet senare.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* innehåller `Run` en metod som tar emot `req` begärandedata i variabeln är en [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) som är dekorerad med **HttpTriggerAttribute**, som definierar utlösarbeteendet. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Returobjektet är ett [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) som returnerar ett svarsmeddelande som antingen ett [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) eller ett [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Mer information finns i [Azure Functions HTTP-utlösare och bindningar](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* innehåller `main()` en Python-funktion som utlöses enligt konfigurationen i *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

För en HTTP-utlösare tar funktionen `req` emot begärandedata i variabeln enligt definitionen i *function.json*. `req`är en instans av [klassen azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Returobjektet, definierat som `$return` i *function.json*, är en instans av klassen [azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Mer information finns i [Azure Functions HTTP-utlösare och bindningar](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js exporterar* en funktion som utlöses enligt konfigurationen i *function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

För en HTTP-utlösare tar funktionen `req` emot begärandedata i variabeln enligt definitionen i *function.json*. Returobjektet, definierat som `$return` i *function.json*, är svaret. Mer information finns i [Azure Functions HTTP-utlösare och bindningar](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts exporterar* en funktion som utlöses enligt konfigurationen i *function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

För en HTTP-utlösare tar funktionen `req` emot begärandedata i variabeln av typen **HttpRequest** enligt definitionen i *function.json*. Returobjektet, definierat som `$return` i *function.json*, är svaret. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definierar ett funktionsskript som utlöses enligt konfigurationen i *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

För en HTTP-utlösare tar funktionen `$Request` emot begärandedata som skickas till param som definierats i *function.json*. Returobjektet, definierat som `Response` i *function.json*, skickas till `Push-OutputBinding` cmdleten som svar. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* är en konfigurationsfil som `bindings` definierar indata och utdata för funktionen, inklusive utlösartypen. 
::: zone-end

::: zone pivot="programming-language-python"
Du kan `scriptFile` ändra till att anropa en annan Python-fil om så önskas.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en [`httpTrigger`](functions-bindings-http-webhook-trigger.md) indatabindning [`http`](functions-bindings-http-webhook-output.md)av typ och utdatabindning av typen .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Innan du kan distribuera funktionskoden till Azure måste du skapa tre resurser:

- En resursgrupp, som är en logisk behållare för relaterade resurser.
- Ett lagringskonto som upprätthåller tillstånd och annan information om dina projekt.
- En funktionsapp som tillhandahåller miljön för att köra din funktionskod. En funktionsapp mappas till ditt lokala funktionsprojekt och låter dig gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Använd följande Azure CLI-kommandon för att skapa dessa objekt. Varje kommando ger JSON-utdata när det är klart.

1. Om du inte redan har gjort det loggar du in på Azure med kommandot [az login:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas `AzureFunctionsQuickstart-rg` en `westeurope` resursgrupp med namnet i regionen. (Du skapar vanligtvis resursgruppen och resurserna i en region nära `az account list-locations` dig med hjälp av en tillgänglig region från kommandot.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Du kan inte vara värd för Linux- och Windows-appar i samma resursgrupp. Om du har en `AzureFunctionsQuickstart-rg` befintlig resursgrupp med namnet med en Windows-funktionsapp eller webbapp måste du använda en annan resursgrupp.
    ::: zone-end  
    
1. Skapa ett allmänt lagringskonto i resursgruppen och regionen med kommandot [skapa az-lagringskonto.](/cli/azure/storage/account#az-storage-account-create) I följande exempel `<STORAGE_NAME>` ersätter du med ett globalt unikt namn som passar dig. Namnen får innehålla endast tre till 24 teckens siffror och gemener. `Standard_LRS`anger ett konto för allmänt ändamål som [stöds av Functions](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Lagringskontot ådrar sig bara några cent (USD) för denna snabbstart.
    
1. Skapa funktionsappen med kommandot [az functionapp create.](/cli/azure/functionapp#az-functionapp-create) I följande exempel `<STORAGE_NAME>` ersätter du med namnet på kontot som `<APP_NAME>` du använde i föregående steg och ersätter med ett globalt unikt namn som passar dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 

    ::: zone pivot="programming-language-python"  
    Om du använder Python 3.8 `3.8` `--functions_version` ändrar du `--runtime-version` till och till `3`.
    
    Om du använder Python 3.6 ändrar du `--runtime-version` till `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Om du använder Node.js 8, ändra `--runtime-version` också till `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Det här kommandot skapar en funktionsapp som körs i din angivna språkkörning under [Azure Functions Consumption Plan](functions-scale.md#consumption-plan), som är gratis för den mängd användning du ådrar dig här. Kommandot etablerar också en associerad Azure Application Insights-instans i samma resursgrupp, med vilken du kan övervaka din funktionsapp och visa loggar. Mer information finns i [Övervaka Azure-funktioner](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.
    
## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktionsprojektet till Azure

::: zone pivot="programming-language-typescript"  
Innan du använder Core Tools för att distribuera projektet till Azure skapar du en produktionsklar version av JavaScript-filer från TypeScript-källfilerna.

Följande kommando förbereder Ditt TypeScript-projekt för distribution:

```
npm run build:production 
```
::: zone-end  

Med nödvändiga resurser på plats är du nu redo att distribuera ditt lokala funktionsprojekt till funktionsappen i Azure med hjälp av kommandot [func azure functionapp publish.](functions-run-local.md#project-file-deployment) Ersätt `<APP_NAME>` med namnet på appen i följande exempel.

```
func azure functionapp publish <APP_NAME>
```

Om du ser felet "Det går inte att hitta app med namn ...", vänta några sekunder och försök `az functionapp create` igen, eftersom Azure kanske inte har initierat appen helt efter det föregående kommandot.

Kommandot Publicera visar resultat som liknar följande utdata (trunkerade för enkelhetens skull):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
</pre>

## <a name="invoke-the-function-on-azure"></a>Anropa funktionen på Azure

Eftersom funktionen använder en HTTP-utlösare anropar du den genom att göra en HTTP-begäran till webbadressen i webbläsaren eller med ett verktyg som curl. I båda fallen `code` är URL-parametern din unika [funktionsnyckel](functions-bindings-http-webhook-trigger.md#authorization-keys) som tillåter anrop av funktionsslutpunkten.

# <a name="browser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **anropa-URL:en** som visas i utdata från publiceringskommandot till ett webbläsaradressfält, vilket lägger till frågeparametern `&name=Functions`. Webbläsaren ska visa liknande utdata som när du körde funktionen lokalt.

![Utdata för funktionen körs på Azure i en webbläsare](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Kör [`curl`](https://curl.haxx.se/) med **url:en anropa**och `&name=Functions`lägga till parametern . Utdata för kommandot ska vara texten "Hej funktioner".

![Utdata för funktionen körs på Azure med curl](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Om du vill visa loggar i nära realtid för en publicerad funktionsapp använder du [Live Metrics Stream för Application Insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa steg, [Lägg till en Azure Storage-köutdatabindning,](functions-add-output-binding-storage-queue-cli.md)behåll alla dina resurser på plats när du bygger vidare på vad du redan har gjort.

Annars använder du följande kommando för att ta bort resursgruppen och alla dess inneslutna resurser för att undvika att ådra sig ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md)
