---
title: Skapa din första funktion med Azure CLI
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning med Azure CLI och Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 07a079e00963f1f5aff96369649e2e4fb248aae0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986006"
---
# <a name="create-your-first-function-from-the-command-line"></a>Skapa din första funktion från kommandoraden

I den här snabbstarten får du hjälp med att skapa din första funktion från kommandoraden eller terminalen. Du kan använda Azure CLI till att skapa en funktionsapp, som är den [serverfria](https://azure.microsoft.com/solutions/serverless/) infrastruktur som är värd för funktionen. Funktionskodprojektet genereras från en mall med hjälp av [Azure Functions Core Tools](functions-run-local.md) som också används för att distribuera funktionsapprojektet till Azure.

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kör exemplet måste du ha följande:

+ Installera [Azure Core Tools version 2.x](functions-run-local.md#v2).

+ Installera [Azure CLI]( /cli/azure/install-azure-cli). Den här artikeln kräver Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Du kan också använda [Azure Cloud Shell](https://shell.azure.com/bash).

+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Skapa det lokala funktionsapprojektet

Kör följande kommando från kommandoraden för att skapa ett funktionsapprojekt i mappen `MyFunctionProj` i den aktuella lokala katalogen. En GitHub-lagringsplats skapas också i `MyFunctionProj`.

```bash
func init MyFunctionProj
```

När du uppmanas väljer du en worker-körtid från följande språkval:

+ `dotnet`: skapar ett .NET-klassbiblioteksprojekt (.csproj).
+ `node`: skapar ett JavaScript-projekt.

När kommandot körs visas något i stil med följande utdata:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

Använd följande kommando för att navigera till den nya `MyFunctionProj`-projektmappen.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Funktionsappen är en miljö för serverfri körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp med kommandot [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

I följande kommando infogar du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Parametern _deployment-source-url_ är en exempellagringsplats i GitHub som innehåller en HTTP-utlöst ”Hello World”-funktion.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

Genom att konfigurera parametern _consumption-plan-location_ anger du att funktionsappen ska använda en förbrukningsplan som värd. I det här serverlösa prenumerationsavtalet läggs resurser till dynamiskt när funktionerna har behov av det och du betalar endast när funktionerna används. Mer information finns i [Välja rätt värdplan](functions-scale.md).

När funktionsappen har skapats visas information som liknar följande exempel i Azure CLI:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>Konfigurera funktionsappen

Core Tools version 2.x skapar projekt med hjälp av mallar för Azure Functions 2.x-körtiden. Därför måste du se till att version 2.x-körtiden används i Azure. Om du anger programinställningen för `FUNCTIONS_WORKER_RUNTIME` som `~2` så fästs funktionsappen på den senaste 2.x-versionen. Ange programinställningar med kommandot [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

I följande Azure CLI-kommando är <app_name> namnet på funktionsappen.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
