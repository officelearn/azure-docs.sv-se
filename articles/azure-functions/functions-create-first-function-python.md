---
title: Skapa en HTTP-utlöst python-funktion i Azure
description: Skapa och distribuera en server lös python-kod till molnet med hjälp av Azure Functions.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 452c5aeab5d2a1092cb7d338d37e26a82d92396e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845506"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Snabb start: skapa en HTTP-utlöst python-funktion i Azure

I den här artikeln använder du kommando rads verktyg för att skapa en python-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions. Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [Visual Studio Code-baserad version](/azure/python/tutorial-vs-code-serverless-python-01) av den här artikeln.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core tools](./functions-run-local.md#v2) version 2.7.1846 eller senare.
- [Azure CLI](/cli/azure/install-azure-cli) -versionen 2.0.76 eller senare. 
- [Python 3.7.4 – 64 bitar](https://www.python.org/downloads/release/python-374/). (Python-3.7.4 verifieras med Azure Functions; Python 3,8 och senare versioner stöds inte ännu.)

### <a name="prerequisite-check"></a>Krav kontroll

1. I ett terminalfönster eller kommando fönster kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 2.7.1846 eller senare.
1. Kör `az --version` för att kontrol lera att Azure CLI-versionen är 2.0.76 eller senare.
1. Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.
1. Kör `python --version` (Linux/MacOS) eller `py --version` (Windows) för att kontrol lera dina python-versions rapporter 3.7. x.

## <a name="create-and-activate-a-virtual-environment"></a>Skapa och aktivera en virtuell miljö

I en lämplig mapp kör du följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv`. Se till att använda python 3,7, som stöds av Azure Functions.


# <a name="bashtabbash"></a>[bash](#tab/bash)

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Kommandot](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Du kör alla efterföljande kommandon i den här aktiverade virtuella miljön. (Du avslutar den virtuella miljön genom att köra `deactivate`.)

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktions projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss utlösare. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda funktion.

1. I den virtuella miljön kör du kommandot `func init` för att skapa ett Functions-projekt i en mapp med namnet *LocalFunctionProj* med den angivna körnings miljön:

    ```
    func init LocalFunctionProj --python
    ```
    
    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler som heter [Local. Settings. JSON](functions-run-local.md#local-settings-file) och [Host. JSON](functions-host-json.md). Eftersom *Local. Settings. JSON* kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i *. gitignore* -filen.

    > [!TIP]
    > Eftersom ett funktions projekt är knutet till en viss körnings miljö måste alla funktioner i projektet skrivas med samma språk.

1. Navigera till projektmappen:

    ```
    cd LocalFunctionProj
    ```
    
1. Lägg till en funktion i projektet med hjälp av följande kommando, där argumentet `--name` är det unika namnet på din funktion och argumentet `--template` anger funktionens utlösare. `func new` skapa en undermapp som matchar funktions namnet som innehåller en kod fil som är lämplig för projektets valda språk och en konfigurations fil med namnet *Function. JSON*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>Valfritt Granska fil innehållet

Om du vill kan du hoppa över att [köra funktionen lokalt](#run-the-function-locally) och undersöka fil innehållet senare.

### <a name="__init__py"></a>\_\_init\_\_. py

*\_\_init\_\_. py* innehåller en `main()` python-funktion som utlöses enligt konfigurationen i *Function. JSON*.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

För HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` som definieras i *Function. JSON*. `req` är en instans av [klassen Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Returvärdet, som definieras som `$return` i *Function. JSON*, är en instans av [klassen Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Läs mer i [Azure Functions HTTP-utlösare och bindningar](functions-bindings-http-webhook.md).

### <a name="functionjson"></a>function.json

*Function. JSON* är en konfigurations fil som definierar indata-och utdata `bindings` för funktionen, inklusive utlösnings typen. Du kan ändra `scriptFile` att anropa en annan python-fil om du vill.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en inkommande bindning av typen [`httpTrigger`](functions-bindings-http-webhook.md#trigger) och utgående bindning av typen [`http`](functions-bindings-http-webhook.md#output).


## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Starta funktionen genom att starta den lokala Azure Functions körnings värden i mappen *LocalFunctionProj* :

```
func start
```

Följande utdata bör visas. (Om HttpExample inte visas som visas nedan, startade du förmodligen värden från mappen *HttpExample* . I så fall använder du **Ctrl**+**C** för att stoppa värden, navigerar till den överordnade *LocalFunctionProj* -mappen och kör `func start` igen.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Kopiera URL: en för din `HttpExample`-funktion från utdata till en webbläsare och Lägg till frågesträngen `?name=<your-name>`, vilket gör den fullständiga URL: en som `http://localhost:7071/api/HttpExample?name=Functions`. Webbläsaren ska visa ett meddelande som `Hello Functions`:

![Resultatet av funktionen körs lokalt i webbläsaren](./media/functions-create-first-function-python/function-test-local-browser.png)

Terminalen där du körde `func start` visar också logg data när du gör förfrågningar.

När du är klar kan du **Ctrl**+**C** för att stoppa funktions värden.

## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Om du vill distribuera funktions koden till Azure måste du skapa tre resurser:

- En resurs grupp, som är en logisk behållare för relaterade resurser.
- Ett Azure Storage konto som upprätthåller tillstånd och annan information om dina projekt.
- En Azure Functions-app som tillhandahåller miljön för att köra funktions koden. En Function-app mappar till ditt lokala Function-projekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Du kan använda Azure CLI-kommandon för att skapa dessa objekt. Varje kommando innehåller JSON-utdata när den har slutförts.

1. Om du inte redan har gjort det loggar du in på Azure med kommandot [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp med namnet `AzureFunctionsQuickstart-rg` i `westeurope` region. (Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region från kommandot `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `AzureFunctionsQuickstart-rg` med en Windows Function-app eller en webbapp måste du använda en annan resurs grupp.
    
1. Skapa ett allmänt lagrings konto i din resurs grupp och region med hjälp av kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) . I följande exempel ersätter du `<storage_name>` med ett globalt unikt namn som passar dig. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett typiskt allmänt syftes konto.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Lagrings kontot ådrar sig bara några USD cent för den här snabb starten.
    
1. Skapa Functions-appen med kommandot [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . I följande exempel ersätter du `<storage_name>` med namnet på det konto som du använde i föregående steg och ersätter `<app_name>` med ett globalt unikt namn som passar dig. `<app_name>` är även DNS-standarddomänen för funktionsappen.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    Det här kommandot skapar en Function-app som kör den angivna språk körningen under [Azure Functions förbruknings plan](functions-scale.md#consumption-plan), vilket är kostnads fritt för den mängd användning som du ådrar dig här. Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.
    
## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktions projektet till Azure

Med nödvändiga resurser på plats är du nu redo att distribuera ditt lokala Functions-projekt till Function-appen i Azure med hjälp av kommandot [FUNC Azure functionapp Publish](functions-run-local.md#project-file-deployment) . I följande exempel ersätter du `<app_name>` med namnet på din app.

```
func azure functionapp publish <app_name>
```

Om du ser felet "det går inte att hitta appen med namnet..." väntar du några sekunder och försöker igen, eftersom Azure kanske inte har fullständigt initierat appen efter föregående `az functionapp create`-kommando.

Kommandot Publicera visar resultat som liknar följande utdata (trunkerade för enkelhetens skull):

```output
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
```

## <a name="invoke-the-function-on-azure"></a>Anropa funktionen på Azure

Eftersom din funktion använder en HTTP-utlösare anropar du den genom att göra en HTTP-begäran till dess URL i webbläsaren eller med ett verktyg som vändning. I båda fallen är `code` URL-parametern din unika funktions nyckel som godkänner anropet till funktions slut punkten.

# <a name="browsertabbrowser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **anrops-URL: en** som visas i utdata från kommandot Publicera till ett webb adress fält för webbläsare, och lägga till Frågeparametern `&name=Azure`. Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

![Resultatet av funktionen körs på Azure i en webbläsare](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[klammerparentes](#tab/curl)

Kör en [sväng](https://curl.haxx.se/) med **anrops-URL: en**och Lägg till parametern `&name=Azure`. Kommandots utdata ska vara texten "Hello Azure".

![Resultatet av funktionen körs på Azure med hjälp av sväng](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> Om du vill visa nära real tids loggar för en publicerad python-app använder du [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa steg lägger du [till en Azure Storage utgående bindning för kö](functions-add-output-binding-storage-queue-python.md), behåller alla resurser på samma sätt som du skapar på det du redan har gjort.

Annars kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till en Azure Storage utgående bindning för kö](functions-add-output-binding-storage-queue-python.md)
