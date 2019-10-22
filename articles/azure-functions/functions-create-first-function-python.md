---
title: Skapa en HTTP-utlöst funktion i Azure
description: Lär dig hur du skapar din första Python-funktion i Azure med hjälp av Azure Functions Core Tools och Azure CLI.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: f2602e5a13f83090291656e7062c74c245bc6568
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693355"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Skapa en HTTP-utlöst funktion i Azure

Den här artikeln visar hur du använder kommando rads verktyg för att skapa ett python-projekt som körs i Azure Functions. Du skapar också en funktion som utlöses av en HTTP-begäran. Slutligen publicerar du ditt projekt så att det körs som en [Server lös funktion](functions-scale.md#consumption-plan) i Azure.

Den här artikeln är den första av två python-snabb starter för Azure Functions. När du har slutfört den här snabb starten kan du [lägga till en Azure Storage utgående bindning för kön](functions-add-output-binding-storage-queue-python.md) till din funktion.

## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

+ Installera [python-3.6.8](https://www.python.org/downloads/). Den här versionen av python verifieras med Functions. 3,7 och senare versioner stöds inte ännu.

+ Installera [Azure Functions Core tools](./functions-run-local.md#v2) version 2.7.1575 eller en senare version.

+ Installera [Azure CLI](/cli/azure/install-azure-cli) version 2. x eller en senare version.

+ Ha en aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Skapa och aktivera en virtuell miljö (valfritt)

Du bör använda en python 3.6. x-miljö för att utveckla python-funktioner lokalt. Kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv`.

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

Nu när du har aktiverat den virtuella miljön kör du de återstående kommandona i den. Kör `deactivate` för att ta bort den virtuella miljön.

## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

Ett Functions-projekt motsvarar en Function-app i Azure. Det kan ha flera funktioner som alla delar samma lokala och värdbaserade konfigurationer.

1. Kör följande kommando i den virtuella miljön:

    ```console
    func init MyFunctionProj
    ```

1. Välj **python** som arbetar-Runtime.

    Kommandot skapar en _MyFunctionProj_ -mapp. Den innehåller följande tre filer:

    * *Local. Settings. JSON*: används för att lagra appinställningar och anslutnings strängar när de körs lokalt. Den här filen publiceras inte i Azure.
    * *Requirements. txt*: innehåller en lista över de paket som systemet ska installera vid publicering till Azure.
    * *Host. JSON*: innehåller globala konfigurations alternativ som påverkar alla funktioner i en Function-app. Den här filen publiceras i Azure.

1. Gå till den nya mappen *MyFunctionProj* :

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Skapa en funktion

Lägg till en funktion i det nya projektet.

1. Om du vill lägga till en funktion i projektet kör du följande kommando:

    ```console
    func new
    ```

1. Använd nedåtpilen för att välja mallen för **http-utlösare** .

1. När du uppmanas att ange ett funktions namn, anger du *HttpTrigger* och trycker sedan på RETUR.

De här kommandona skapar en undermapp med namnet _HttpTrigger_. Den innehåller följande filer:

* *Function. JSON*: konfigurations fil som definierar funktionen, utlösaren och andra bindningar. Observera att värdet för `scriptFile` pekar på filen som innehåller funktionen i den här filen och att `bindings` matrisen definierar anrops utlösaren och bindningarna.

    Varje bindning kräver en riktning, skriv och ett unikt namn. HTTP-utlösaren har en inkommande bindning av typen [`httpTrigger`](functions-bindings-http-webhook.md#trigger) och utgående bindning av typen [`http`](functions-bindings-http-webhook.md#output).

* *\_ \_init \_ \_. py*: skript fil som är din http-utlöst funktion. Observera att det här skriptet har en standard `main()`. HTTP-data från utlösaren skickas till funktionen med hjälp av `req` med namnet `binding parameter`. @No__t_0, som definieras i function. JSON, är en instans av [klassen Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Returvärdet, som definieras som `$return` i *Function. JSON*, är en instans av [klassen Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Läs mer i [Azure Functions HTTP-utlösare och bindningar](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Funktionen körs lokalt med hjälp av Azure Functions Runtime.

1. Det här kommandot startar Function-appen:

    ```console
    func host start
    ```

    När Azure Functions värden startar skriver den något som följande utdata. Den trunkeras här så att du kan läsa den bättre:

    ```output
    
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %
    
    ...
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Kopiera URL:en till din funktion `HttpTrigger` från körtidutdatan och klistra in den i webbläsarens adressfält.

1. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran. Följande skärm bild visar svaret på GET-begäran som den lokala funktionen returnerar till webbläsaren:

    ![Verifiera lokalt i webbläsaren](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Välj CTRL + C om du vill stänga av din Function-app.

Nu när du har kört funktionen lokalt kan du skapa funktionsappen och andra nödvändiga resurser i Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Skapa en Function-app i Azure

En Function-app tillhandahåller en miljö för att köra funktions koden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

Kör följande kommando. Ersätt `<APP_NAME>` med ett unikt namn för Function-appen. Ersätt `<STORAGE_NAME>` med ett lagrings konto namn. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. Det här namnet måste vara unikt inom alla appar i Azure.

> [!NOTE]
> Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `myResourceGroup` med en Windows Function-app eller en webbapp måste du använda en annan resurs grupp.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Föregående kommando etablerar även en associerad Azure Application insikts-instans i samma resurs grupp. Du kan använda den här instansen för att övervaka din Function-app och Visa loggar.

Nu är du redo att publicera ditt lokala Functions-projekt till Function-appen i Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Distribuera funktionsapprojektet till Azure

När du har skapat Function-appen i Azure kan du använda kommandot [FUNC Azure-functionapp publicera](functions-run-local.md#project-file-deployment) Core-verktyg för att distribuera projekt koden till Azure. I det här exemplet ersätter du `<APP_NAME>` med namnet på din app.

```console
func azure functionapp publish <APP_NAME> --build remote
```

Med alternativet `--build remote` skapas ett python-projekt via fjärr anslutning i Azure från filerna i distributions paketet. 

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
