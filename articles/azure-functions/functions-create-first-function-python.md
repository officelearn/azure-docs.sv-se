---
title: Skapa din första Python-funktion i Azure
description: Lär dig hur du skapar din första Python-funktion i Azure med hjälp av Azure Functions Core Tools och Azure CLI.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: a5abc90f5a40911538b6516622203f595adfed5c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091823"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Skapa din första Python-funktion i Azure (förhandsversion)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Den här snabbstartsartikeln går igenom hur du använder Azure CLI till att skapa din första [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) Python-funktionsapp som körs på Linux. Funktionskoden skapas lokalt och distribueras sedan till Azure med hjälp av [Azure Functions Core Tools](functions-run-local.md). Mer information om förhandsversionsöverväganden för att köra funktionsappar i Linux finns i [den här artikeln om Functions i Linux](https://aka.ms/funclinux).

Följande steg kan användas på en Mac-, Windows- eller Linux-dator.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill skapa och testa lokalt måste du:

+ Installera [Python 3.6](https://www.python.org/downloads/)

+ Installera [Azure Functions Core Tools](functions-run-local.md#v2) version 2.2.70 eller senare

Om du vill publicera och köra i Azure:

+ Installera [Azure CLI]( /cli/azure/install-azure-cli) version 2.x eller senare.

+ Du behöver en aktiv Azure-prenumeration.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Skapa och aktivera en virtuell miljö

Om du vill skapa ett Functions-projekt måste du arbeta i en virtuell miljö för Python 3.6. Kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.env`.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

Nu kan du skapa ett lokalt Functions-projekt. Den här katalogen är motsvarigheten till en funktionsapp i Azure. Den kan innehålla flera funktioner som delar samma lokala och värdbaserade konfiguration.

I terminalfönstret eller från en kommandotolk kör du följande kommando:

```bash
func init MyFunctionProj
```

Välj **python** som önskad körning.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

Något som liknar följande utdata visas.

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

En ny mapp med namnet _MyFunctionProj_ skapas. Om du vill fortsätta ändrar du katalogen till den här mappen.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>Skapa en funktion

Skapa en funktion genom att köra följande kommando:

```bash
func new
```

Välj `HTTP Trigger` som mall och ange **funktionsnamnet** `HttpTrigger`.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

Något som liknar följande utdata visas.

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

En undermapp med namnet _HttpTrigger_ skapas. Den innehåller `__init__.py`, som är den primära skriptfilen, och `function.json`-filen, som beskriver utlösaren och bindningarna som används av funktionen. Mer information om programmeringsmodellen finns i [Azure Functions Python-utvecklarguiden](functions-reference-python.md).

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Använd följande kommando om du vill köra Functions-värden lokalt.

```bash
func host start
```

När Functions-värden startar anger den webbadressen till den HTTP-utlösta funktionen som utdata. (Observera att alla utdata har trunkerats för läsbarhetens skull.)

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
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

Kopiera webbadressen till funktionen från dessa utdata och klistra in den i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

Följande skärmbild visar svaret från funktionen, när den utlöses från webbläsaren:

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

Nu är du redo att skapa en funktionsapp och andra resurser som krävs för publicering till Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Skapa en Linux-funktionsapp i Azure

Funktionsappen är en miljö för körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en **Python-funktionsapp som körs på Linux** med hjälp av kommandot [az functionapp create](/cli/azure/functionapp#az_functionapp_create).

Kör följande kommando med ett unikt funktionsappnamn i stället för platshållaren `<app_name>` och lagringskontonamnet i stället för `<storage_name>`. `<app_name>` är även DNS-standarddomänen för funktionsappen. Det här namnet måste vara unikt inom alla appar i Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Om du har en befintlig resursgrupp med namnet `myResourceGroup` med någon icke-Linux App Service-app måste du använda en annan resursgrupp. Du kan inte ha både Windows- och Linux-appar i samma resursgrupp.  

När funktionsappen har skapats visas följande meddelande:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Nu är du redo att publicera det lokala Functions-projektet till funktionsappen i Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Distribuera funktionsapprojektet till Azure

Kör följande kommando med Azure Functions Core Tools. Ersätt `<app_name>` med namnet på appen från föregående steg.

```bash
func azure functionapp publish <app_name>
```

Något som liknar följande utdata visas, som har trunkerats för läsbarhetens skull.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du utvecklar Azure Functions med hjälp av Python.

> [!div class="nextstepaction"]
> [Azure Functions Python-utvecklarguide](functions-reference-python.md)
> [Azure Functions-utlösare och -bindningar](functions-triggers-bindings.md)
