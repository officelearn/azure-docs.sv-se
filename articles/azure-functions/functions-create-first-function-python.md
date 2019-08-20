---
title: Skapa en HTTP-utlöst funktion i Azure
description: Lär dig hur du skapar din första Python-funktion i Azure med hjälp av Azure Functions Core Tools och Azure CLI.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 5b90702f89af260a67b69bf96c2e079a45298723
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575440"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Skapa en HTTP-utlöst funktion i Azure

Den här artikeln visar hur du använder kommando rads verktyg för att skapa ett python-projekt som körs i Azure Functions. Funktionen som du skapar utlöses av HTTP-begäranden. Slutligen publicerar du ditt projekt så att det körs som en [Server lös funktion](functions-scale.md#consumption-plan) i Azure.

Den här artikeln är den första av två snabb starter för Azure Functions. När du har slutfört den här artikeln [lägger du till en Azure Storage utgående bindning för kön](functions-add-output-binding-storage-queue-python.md) till din funktion.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha följande:

+ Installera [Python 3.6](https://www.python.org/downloads/).

+ Installera [Azure Functions Core tools](./functions-run-local.md#v2) version 2.7.1575 eller en senare version.

+ Installera [Azure CLI](/cli/azure/install-azure-cli) version 2. x eller en senare version.

+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Skapa och aktivera en virtuell miljö (valfritt)

För att lokalt utveckla och testa python-funktioner rekommenderar vi att du använder en python 3,6-miljö. Kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv`.

### <a name="bash"></a>Bash

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell eller en kommando tolk i Windows:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

Återstående kommandon körs i den virtuella miljön.

## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

Ett Functions-projekt motsvarar en Function-app i Azure. Det kan ha flera funktioner som alla delar samma lokala och värdbaserade konfigurationer.

Kör följande kommando i den virtuella miljön och välj **python** som arbetar-Runtime.

```console
func init MyFunctionProj
```

En mapp med namnet _MyFunctionProj_ skapas, som innehåller följande tre filer:

* `local.settings.json`används för att lagra appinställningar och anslutnings strängar när de körs lokalt. Den här filen publiceras inte i Azure.
* `requirements.txt`innehåller listan över paket som ska installeras vid publicering till Azure.
* `host.json`innehåller globala konfigurations alternativ som påverkar alla funktioner i en Function-app. Den här filen publiceras i Azure.

Navigera till den nya mappen MyFunctionProj:

```console
cd MyFunctionProj
```

## <a name="create-a-function"></a>Skapa en funktion

Om du vill lägga till en funktion i projektet kör du följande kommando:

```console
func new
```

Välj mallen för **http-** utlösare, Skriv `HttpTrigger` som namn på funktionen och tryck på RETUR.

En undermapp med namnet _HttpTrigger_ skapas, som innehåller följande filer:

* **Function. JSON**: konfigurations fil som definierar funktionen, utlösaren och andra bindningar. Granska den här filen och se att värdet för `scriptFile` pekar på filen som innehåller funktionen, medan anrops utlösaren och bindningarna definieras `bindings` i matrisen.

  Varje bindning kräver en riktning, skriv och ett unikt namn. HTTP-utlösaren har en inkommande bindning [`httpTrigger`](functions-bindings-http-webhook.md#trigger) av typen och utgående bindningen av typen. [`http`](functions-bindings-http-webhook.md#output)

* **init.py\_: skript fil som är din http-utlöst funktion.\_ \_ \_** Granska det här skriptet och se att det innehåller ett `main()`standardvärde. HTTP-data från utlösaren skickas till den här funktionen `req` med hjälp av den namngivna bindnings parametern. Definierad i function. JSON, `req` är en instans av [klassen Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Returvärdet, som definieras som `$return` i function. JSON, är en instans av [klassen Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Läs mer i [Azure Functions HTTP-utlösare och bindningar](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Följande kommando startar Function-appen, som körs lokalt med samma Azure Functions-körning som finns i Azure.

```bash
func host start
```

När Functions-värden startar skriver den något som följande utdata, som har trunkerats för läsbarhet:

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

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Kopiera URL:en till din funktion `HttpTrigger` från körtidutdatan och klistra in den i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran. Nedan visas svaret på GET-begäran som returnerades av den lokala funktionen i webbläsaren:

![Testa lokalt i webbläsaren](./media/functions-create-first-function-python/function-test-local-browser.png)

Nu när du har kört funktionen lokalt kan du skapa funktionsappen och andra nödvändiga resurser i Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Skapa en Function-app i Azure

En Function-app tillhandahåller en miljö för att köra funktions koden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

Kör följande kommando med ett unikt funktionsappnamn i stället för platshållaren `<APP_NAME>` och lagringskontonamnet i stället för `<STORAGE_NAME>`. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. Det här namnet måste vara unikt inom alla appar i Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```
> [!NOTE]
> Linux-och Windows-appar kan inte finnas i samma resurs grupp. Om du har en befintlig resurs grupp med `myResourceGroup` namnet med en Windows Function-app eller webbapp måste du använda en annan resurs grupp.

Det här kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp som kan användas för att övervaka och Visa loggar.

Nu är du redo att publicera ditt lokala Functions-projekt till Function-appen i Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Om du vill visa nära real tids loggar för en publicerad python-app rekommenderar vi att du använder [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs)

## <a name="next-steps"></a>Nästa steg

Du har skapat ett python Functions-projekt med en HTTP-utlöst funktion, kört den på din lokala dator och distribuerat den till Azure. Nu kan du utöka din funktion efter...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage utgående bindning för kö](functions-add-output-binding-storage-queue-python.md)
