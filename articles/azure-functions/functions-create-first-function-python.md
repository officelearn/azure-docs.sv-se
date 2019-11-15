---
title: Skapa en HTTP-utlöst python-funktion i Azure
description: Lär dig hur du skapar din första Python-funktion i Azure med hjälp av Azure Functions Core Tools och Azure CLI.
author: ggailey777
ms.author: glenga
ms.date: 11/07/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 61465177c98a31a739946097ca615382175df3d4
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082759"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Snabb start: skapa en HTTP-utlöst python-funktion i Azure

Den här artikeln visar hur du använder kommando rads verktyg för att skapa ett python-projekt som körs i Azure Functions. Du skapar också en funktion som utlöses av en HTTP-begäran. När du har kört lokalt publicerar du ditt projekt så att det körs som en [Server lös funktion](functions-scale.md#consumption-plan) i Azure. 

Den här artikeln är den första av två python-snabb starter för Azure Functions. När du har slutfört den här snabb starten kan du [lägga till en Azure Storage utgående bindning för kön](functions-add-output-binding-storage-queue-python.md) till din funktion.

Det finns också en [Visual Studio Code-baserad version](/azure/python/tutorial-vs-code-serverless-python-01) av den här artikeln.

## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

+ Installera [python-3.7.4](https://www.python.org/downloads/). Den här versionen av python verifieras med Functions. Python 3,8 och senare versioner stöds inte ännu.

+ Installera [Azure Functions Core tools](./functions-run-local.md#v2) version 2.7.1846 eller en senare version.

+ Installera [Azure CLI](/cli/azure/install-azure-cli) -versionen 2.0.76 eller en senare version.

+ Ha en aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Skapa och aktivera en virtuell miljö

Du bör använda en python 3,7-miljö för att utveckla python-funktioner lokalt. Kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv`.

> [!NOTE]
> Om python inte har installerat venv på din Linux-distribution kan du installera det med hjälp av följande kommando:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell eller en kommando tolk i Windows:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Nu när du har aktiverat den virtuella miljön kör du de återstående kommandona i den. Kör `deactivate`för att ta bort den virtuella miljön.

## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

Ett Functions-projekt kan ha flera funktioner som alla delar samma lokala och värdbaserade konfigurationer.

Kör följande kommandon i den virtuella miljön:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

Kommandot `func init` skapar en _MyFunctionProj_ -mapp. Python-projektet i den här mappen har inte några funktioner än. Du lägger till dem härnäst.

## <a name="create-a-function"></a>Skapa en funktion

Om du vill lägga till en funktion i projektet kör du följande kommando:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

Med det här kommandot skapar du en undermapp med namnet _HttpTrigger_, som innehåller följande filer:

* *Function. JSON*: konfigurations fil som definierar funktionen, utlösaren och andra bindningar. Observera att värdet för `scriptFile` pekar på filen som innehåller funktionen i den här filen och att `bindings` matrisen definierar anrops utlösaren och bindningarna.

    Varje bindning kräver en riktning, skriv och ett unikt namn. HTTP-utlösaren har en inkommande bindning av typen [`httpTrigger`](functions-bindings-http-webhook.md#trigger) och utgående bindning av typen [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_. py*: skript fil som är din http-utlöst funktion. Observera att det här skriptet har en standard `main()`. HTTP-data från utlösaren skickas till funktionen med hjälp av `req` med namnet `binding parameter`. `req`, som definieras i function. JSON, är en instans av [klassen Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Returvärdet, som definieras som `$return` i *Function. JSON*, är en instans av [klassen Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Läs mer i [Azure Functions HTTP-utlösare och bindningar](functions-bindings-http-webhook.md).

Nu kan du köra den nya funktionen på den lokala datorn.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Med det här kommandot startar du Function-appen med hjälp av Azure Functions Runtime (Func. exe):

```console
func host start
```

Du bör se följande information som skrivs till utdata:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

Kopiera URL: en för din `HttpTrigger`-funktion från den här utdata och klistra in den i webbläsarens Adress fält. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran. Följande skärm bild visar svaret på GET-begäran som den lokala funktionen returnerar till webbläsaren:

![Verifiera lokalt i webbläsaren](./media/functions-create-first-function-python/function-test-local-browser.png)

Använd CTRL + C om du vill stänga av funktionen för att köra appen.

Nu när du har kört funktionen lokalt kan du distribuera funktions koden till Azure.  
Innan du kan distribuera din app måste du skapa några Azure-resurser.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Skapa en Function-app i Azure

En Function-app tillhandahåller en miljö för att köra funktions koden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. 

Kör följande kommando. Ersätt `<APP_NAME>` med ett unikt namn för Function-appen. Ersätt `<STORAGE_NAME>` med ett lagrings konto namn. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. Det här namnet måste vara unikt inom alla appar i Azure.

> [!NOTE]
> Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `myResourceGroup` med en Windows Function-app eller en webbapp måste du använda en annan resurs grupp.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Föregående kommando skapar en Function-app som kör python-3.7.4. Den etablerar även en associerad Azure Application insikts-instans i samma resurs grupp. Du kan använda den här instansen för att övervaka din Function-app och Visa loggar. 

Nu är du redo att publicera ditt lokala Functions-projekt till Function-appen i Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Distribuera funktionsapprojektet till Azure

När du har skapat Function-appen i Azure kan du använda kommandot [FUNC Azure-functionapp publicera](functions-run-local.md#project-file-deployment) Core-verktyg för att distribuera projekt koden till Azure. I det här exemplet ersätter du `<APP_NAME>` med namnet på din app.

```console
func azure functionapp publish <APP_NAME> --build remote
```

Med alternativet `--build remote` skapas ett python-projekt via fjärr anslutning i Azure från filerna i distributions paketet, vilket rekommenderas. 

Du ser utdata som liknar följande meddelande. Den trunkeras här så att du kan läsa den bättre:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Du kan kopiera `Invoke url`-värdet för din `HttpTrigger` och använda det för att verifiera din funktion i Azure. URL: en innehåller ett `code` frågesträngs värde som är din funktions nyckel, vilket gör det svårt för andra att anropa en slut punkt för HTTP-utlösare i Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Om du vill visa nära real tids loggar för en publicerad python-app använder du [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Nästa steg

Du har skapat ett python Functions-projekt med en HTTP-utlöst funktion, kört den på din lokala dator och distribuerat den till Azure. Nu kan du utöka din funktion efter...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage utgående bindning för kö](functions-add-output-binding-storage-queue-python.md)
