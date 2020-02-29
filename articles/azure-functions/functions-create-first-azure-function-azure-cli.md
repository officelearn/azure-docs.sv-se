---
title: Skapa en funktion i Azure som svarar på HTTP-begäranden
description: Lär dig hur du skapar en funktion från kommando raden och sedan publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c8648a0bdfaeeb8a89ea24b0f49610e5bf5c0491
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190892"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Snabb start: skapa en funktion i Azure som svarar på HTTP-begäranden

I den här artikeln använder du kommando rads verktyg för att skapa en funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions. Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [Visual Studio Code-baserad version](functions-create-first-function-vs-code.md) av den här artikeln.

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core tools](./functions-run-local.md#v2) version 2.7.1846 eller en senare 2. x-version.

+ [Azure CLI](/cli/azure/install-azure-cli) -versionen 2.0.76 eller senare. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), aktiva LTS och underhåll LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) eller [python 3,6](https://www.python.org/downloads/release/python-368/), som stöds av Azure Functions. Python 3,8 och senare versioner stöds inte ännu. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell-kärna](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.Net Core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Kontrol lera din miljö

+ I ett terminalfönster-eller kommando fönster kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 2.7.1846 eller en senare 2. x-version.

+ Kör `az --version` för att kontrol lera att Azure CLI-versionen är 2.0.76 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Kör `node --version` för att kontrol lera Node. js-versions rapporter 8. x eller 10. x.
::: zone-end
::: zone pivot="programming-language-python"
+ Kör `python --version` (Linux/MacOS) eller `py --version` (Windows) för att kontrol lera python-versions rapporterna 3.7. x eller 3.6. x.

## <a name="create-venv"></a>Skapa och aktivera en virtuell miljö

I en lämplig mapp kör du följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv`. Se till att använda python 3,7 eller 3,6, som stöds av Azure Functions.


# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Om python inte installerade venv-paketet på din Linux-distribution kör du följande kommando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Kommandot](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Du kör alla efterföljande kommandon i den här aktiverade virtuella miljön. (Du avslutar den virtuella miljön genom att köra `deactivate`.)

::: zone-end

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktions projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss utlösare. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda funktion.

1. I den virtuella miljön kör du kommandot `func init` för att skapa ett Functions-projekt i en mapp med namnet *LocalFunctionProj* med den angivna körnings miljön:

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


    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler som heter [Local. Settings. JSON](functions-run-local.md#local-settings-file) och [Host. JSON](functions-host-json.md). Eftersom *Local. Settings. JSON* kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i *. gitignore* -filen.

1. Navigera till projektmappen:

    ```
    cd LocalFunctionProj
    ```
    
1. Lägg till en funktion i projektet med hjälp av följande kommando, där argumentet `--name` är det unika namnet på din funktion och argumentet `--template` anger funktionens utlösare. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new` skapar en HttpExample.cs-kod fil.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new` skapar en undermapp som matchar funktions namnet som innehåller en kod fil som är lämplig för projektets valda språk och en konfigurations fil med namnet *Function. JSON*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>Valfritt Granska fil innehållet

Om du vill kan du hoppa över att [köra funktionen lokalt](#run-the-function-locally) och undersöka fil innehållet senare.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* innehåller en `Run` metod som tar emot begär ande data i `req`-variabeln är en [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) som är dekorerad med **HttpTriggerAttribute**, som definierar utlösnings beteendet. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Returvärdet är en [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) som returnerar ett svarsmeddelande som antingen en [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) eller en [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Läs mer i [Azure Functions HTTP-utlösare och bindningar](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_. py

*\_\_init\_\_. py* innehåller en `main()` python-funktion som utlöses enligt konfigurationen i *Function. JSON*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

För en HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` som definieras i *Function. JSON*. `req` är en instans av [klassen Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Returvärdet, som definieras som `$return` i *Function. JSON*, är en instans av [klassen Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Läs mer i [Azure Functions HTTP-utlösare och bindningar](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index. js

*index. js* exporterar en funktion som utlöses enligt konfigurationen i *Function. JSON*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

För en HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` som definieras i *Function. JSON*. Returvärdet, som definieras som `$return` i *Function. JSON*, är svaret. Läs mer i [Azure Functions HTTP-utlösare och bindningar](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index. TS

*index. TS* exporterar en funktion som utlöses enligt konfigurationen i *Function. JSON*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

För en HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` av typen **HttpRequest** enligt definitionen i *Function. JSON*. Returvärdet, som definieras som `$return` i *Function. JSON*, är svaret. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>Kör. ps1

*Run. ps1* definierar ett funktions skript som utlöses enligt konfigurationen i *Function. JSON*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

För en HTTP-utlösare tar funktionen emot begär ande data som skickas till den `$Request` param som definierats i *Function. JSON*. Returvärdet, som definieras som `Response` i *Function. JSON*, skickas till `Push-OutputBinding`-cmdleten som svar. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*Function. JSON* är en konfigurations fil som definierar indata-och utdata `bindings` för funktionen, inklusive utlösnings typen. 
::: zone-end

::: zone pivot="programming-language-python"
Du kan ändra `scriptFile` att anropa en annan python-fil om du vill.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en inkommande bindning av typen [`httpTrigger`](functions-bindings-http-webhook-trigger.md) och utgående bindning av typen [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Innan du kan distribuera funktions koden till Azure måste du skapa tre resurser:

- En resurs grupp, som är en logisk behållare för relaterade resurser.
- Ett lagrings konto som upprätthåller tillstånd och annan information om dina projekt.
- En Function-app som tillhandahåller miljön för att köra funktions koden. En Function-app mappar till ditt lokala Function-projekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Använd följande Azure CLI-kommandon för att skapa dessa objekt. Varje kommando innehåller JSON-utdata när den har slutförts.

1. Om du inte redan har gjort det loggar du in på Azure med kommandot [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp med namnet `AzureFunctionsQuickstart-rg` i `westeurope` region. (Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region från kommandot `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `AzureFunctionsQuickstart-rg` med en Windows Function-app eller en webbapp måste du använda en annan resurs grupp.
    ::: zone-end  
    
1. Skapa ett allmänt lagrings konto i din resurs grupp och region med hjälp av kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) . I följande exempel ersätter du `<STORAGE_NAME>` med ett globalt unikt namn som passar dig. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett allmänt-syfte-konto, som [stöds av Functions](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Lagrings kontot förväntar sig bara några cent (USD) för den här snabb starten.
    
1. Skapa Functions-appen med kommandot [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . I följande exempel ersätter du `<STORAGE_NAME>` med namnet på det konto som du använde i föregående steg och ersätter `<APP_NAME>` med ett globalt unikt namn som passar dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 

    ::: zone pivot="programming-language-python"  
    Om du använder python 3,6 ändrar du också `--runtime-version` till `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Om du använder Node. js 8 kan du också ändra `--runtime-version` till `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Det här kommandot skapar en Function-app som körs på den angivna språk körningen under [Azure Functions förbruknings plan](functions-scale.md#consumption-plan), vilket är kostnads fritt för den användnings mängd som du ådrar dig här. Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.
    
## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktions projektet till Azure

::: zone pivot="programming-language-typescript"  
Innan du använder kärn verktyg för att distribuera ditt projekt till Azure skapar du en produktions klar version av JavaScript-filer från källfilerna för TypeScript.

Följande kommando förbereder ditt TypeScript-projekt för distribution:

```
npm run build:production 
```
::: zone-end  

Med nödvändiga resurser på plats är du nu redo att distribuera ditt lokala Functions-projekt till Function-appen i Azure med hjälp av kommandot [FUNC Azure functionapp Publish](functions-run-local.md#project-file-deployment) . I följande exempel ersätter du `<APP_NAME>` med namnet på din app.

```
func azure functionapp publish <APP_NAME>
```

Om du ser felet "det går inte att hitta appen med namnet..." väntar du några sekunder och försöker igen, eftersom Azure kanske inte har fullständigt initierat appen efter föregående `az functionapp create`-kommando.

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

Eftersom din funktion använder en HTTP-utlösare anropar du den genom att göra en HTTP-begäran till dess URL i webbläsaren eller med ett verktyg som vändning. I båda fallen är `code` URL-parametern din unika [funktions nyckel](functions-bindings-http-webhook-trigger.md#authorization-keys) som godkänner anropet av funktions slut punkten.

# <a name="browser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **anrops-URL: en** som visas i utdata från kommandot Publicera till ett webb adress fält för webbläsare, och lägga till Frågeparametern `&name=Functions`. Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

![Resultatet av funktionen körs på Azure i en webbläsare](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[klammerparentes](#tab/curl)

Kör [`curl`](https://curl.haxx.se/) med **ANROPs-URL: en**och Lägg till parametern `&name=Functions`. Kommandots utdata ska vara texten, "Hello Functions".

![Resultatet av funktionen körs på Azure med hjälp av sväng](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Om du vill visa nära real tids loggar för en publicerad Function-app använder du [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa steg lägger du [till en Azure Storage utgående bindning för kö](functions-add-output-binding-storage-queue-python.md), behåller alla resurser på samma sätt som du skapar på det du redan har gjort.

Annars kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md)
