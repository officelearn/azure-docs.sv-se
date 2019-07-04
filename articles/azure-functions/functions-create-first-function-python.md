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
ms.openlocfilehash: 5ef30fbf647492f79c64508d8306868aa1f6b278
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444579"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Skapa en HTTP-utlöst funktion i Azure

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Den här artikeln visar hur du använder kommandoradsverktyg för att skapa en Python-projekt som körs i Azure Functions. Funktionen som du skapar utlöses av HTTP-begäranden. Slutligen kan du publicera projektet att köras som en [funktion](functions-scale.md#consumption-plan) i Azure.

Den här artikeln är först av två snabbstarter för Azure Functions. När du har slutfört den här artikeln får du [lägga till ett Azure Storage köutdatabindningen](functions-add-output-binding-storage-queue-python.md) till din funktion.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha följande:

+ Installera [Python 3.6](https://www.python.org/downloads/).

+ Installera [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.1071 eller en senare version.

+ Installera den [Azure CLI](/cli/azure/install-azure-cli) version 2.x eller en senare version.

+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Skapa och aktivera en virtuell miljö

För att lokalt utveckla och testa Python-funktioner, måste du arbeta i en miljö med Python 3.6. Kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.env`.

### <a name="bash"></a>Bash:

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell eller en Windows-kommandotolk:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

De resterande kommandona körs i den virtuella miljön.

## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

Functions-projekt är motsvarigheten till en funktionsapp i Azure. Den kan ha flera funktioner som alla har samma lokala och värdbaserade konfigurationer.

I den virtuella miljön genom att köra följande kommando, välja **python** som worker-runtime.

```console
func init MyFunctionProj
```

En mapp med namnet _MyFunctionProj_ skapas, som innehåller följande tre filer:

* `local.settings.json` används för att lagra appinställningar och anslutningssträngar när du kör lokalt. Den här filen publiceras inte till Azure.
* `requirements.txt` innehåller listan över paket som ska installeras på publicering till Azure.
* `host.json` innehåller globala konfigurationsalternativ som påverkar alla funktioner i en funktionsapp. Den här filen publiceras till Azure.

Navigera till mappen MyFunctionProj:

```console
cd MyFunctionProj
```

Därefter uppdaterar du filen host.json om du vill aktivera tillägget paket.  

## <a name="create-a-function"></a>Skapa en funktion

Lägg till en funktion i projektet genom att köra följande kommando:

```console
func new
```

Välj den **HTTP-utlösare** mall, skriver du in `HttpTrigger` som namn för funktionen, och tryck sedan på RETUR.

En undermapp med namnet _HttpTrigger_ skapas, som innehåller följande filer:

* **Function.JSON**: konfigurationsfil som definierar funktionen, utlösare och bindningar för andra. Granska den här filen och ser till att värdet för `scriptFile` pekar på filen som innehåller funktionen, medan anrop utlösare och bindningar har definierats i den `bindings` matris.

  Varje bindning kräver en riktning, typ och ett unikt namn. HTTP-utlösaren har en indatabindning av typen [ `httpTrigger` ](functions-bindings-http-webhook.md#trigger) och en utgående bindning av typen [ `http` ](functions-bindings-http-webhook.md#output).

* **\_\_init\_\_.py**: funktion som utlöses av skriptfil som är HTTP. Granska det här skriptet och se att den innehåller en standard `main()`. HTTP-data från utlösaren skickas till den här funktionen med hjälp av den `req` med namnet bindningsparametern. Definierats i function.json, `req` är en instans av den [azure.functions.HttpRequest klass](/python/api/azure-functions/azure.functions.httprequest). 

    Det returnera objektet, definierad som `$return` i function.json, är en instans av [azure.functions.HttpResponse klass](/python/api/azure-functions/azure.functions.httpresponse). Mer information finns i [Azure Functions HTTP-utlösare och bindningar](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Följande kommando startar funktionsapp, som körs lokalt med hjälp av samma Azure Functions-körning som finns i Azure.

```bash
func host start
```

När funktioner värden startas, skrivs ungefär som i följande utdata har trunkerats för läsbarhet:

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

## <a name="create-a-function-app-in-azure"></a>Skapa en funktionsapp i Azure

En funktionsapp är en miljö för körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

Kör följande kommando med ett unikt funktionsappnamn i stället för platshållaren `<APP_NAME>` och lagringskontonamnet i stället för `<STORAGE_NAME>`. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. Det här namnet måste vara unikt inom alla appar i Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

> [!NOTE]
> Azure Functions, förbrukningsplan för Linux är för närvarande i förhandsversion och endast tillgängligt i följande regioner: Västra USA, östra USA, Västeuropa, östra Asien. Linux och Windows-appar kan dessutom finnas i samma resursgrupp. Om du har en befintlig resursgrupp med namnet `myResourceGroup` med en Windows-funktionsapp eller webbapp, måste du använda en annan resursgrupp.

Du är nu redo att publicera projektet lokala funktioner i funktionsappen i Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en Python-funktionsprojekt med en HTTP-utlöst funktion, köra den på den lokala datorn och distribuerat den till Azure. Nu kan utöka din funktion av...

> [!div class="nextstepaction"]
> [Att lägga till ett Azure Storage köutdatabindningen](functions-add-output-binding-storage-queue-python.md)
