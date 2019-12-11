---
title: Skapa din första Linux-funktion i Azure
description: Lär dig hur du skapar din första funktion på Linux i Azure med hjälp av kommando rads verktygen Azure Functions Core Tools och Azure CLI.
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 57c1b4a0ef5e8f030360bca6d69f66e8752f3da1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964148"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>Snabb start: skapa din första funktion på Linux med hjälp av kommando rads verktyg

Med Azure Functions kan du köra kod i en [serverfri](https://azure.com/serverless) Linux-miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. Linux-värd kräver [funktioner 2. x och senare körning](functions-versions.md). Funktioner utan Server körs i [förbruknings planen](functions-scale.md#consumption-plan).

Den här snabbstartsartikeln går igenom hur du använder Azure CLI till att skapa din första funktionsapp som körs på Linux. Funktionskoden skapas lokalt och distribueras sedan till Azure med hjälp av [Azure Functions Core Tools](functions-run-local.md).

Följande steg kan användas på en Mac-, Windows- eller Linux-dator. Den här artikeln visar hur du skapar funktioner i JavaScript eller C#. Information om hur du skapar python-funktioner finns i [skapa din första python-funktion med hjälp av kärn verktyg och Azure CLI](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Krav

Innan du kör exemplet måste du ha följande:

+ Installera [Azure Functions Core tools](./functions-run-local.md#v2) version 2.6.666 eller senare.

+ Installera [Azure CLI]( /cli/azure/install-azure-cli). Den här artikeln kräver Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Du kan också använda [Azure Cloud Shell](https://shell.azure.com/bash).

+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Aktivera tilläggs paket

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Skapa en Linux-funktionsapp i Azure

Du måste ha en funktionsapp som värd för körning av dina funktioner i Linux. Funktionsappen ger en serverlös miljö för körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp som körs på Linux med hjälp av kommandot [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

I följande kommando använder du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` är även DNS-standarddomänen för funktionsappen. Det här namnet måste vara unikt inom alla appar i Azure. Du bör också ange `<language>` runtime för din Function-app, från `dotnet` (C#), `node` (JavaScript/typescript) eller `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

När funktionsappen har skapats visas följande meddelande:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Nu kan du publicera ditt projekt i den nya funktionsappen i Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]