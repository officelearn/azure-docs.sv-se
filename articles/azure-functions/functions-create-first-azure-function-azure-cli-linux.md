---
title: Skapa din första Linux-funktion i Azure
description: Learn how to create your first function hosted on Linux in Azure using the command line tools, Azure Functions Core Tools and the Azure CLI.
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 19abfee69db53c560dfa2696d85f8c1c3d770c09
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230816"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>Quickstart: Create your first function hosted on Linux using command line tools

Med Azure Functions kan du köra kod i en [serverfri](https://azure.com/serverless) Linux-miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. Linux-hosting requires [the Functions 2.x runtime](functions-versions.md). Serverless functions run in the [Consumption plan](functions-scale.md#consumption-plan).

Den här snabbstartsartikeln går igenom hur du använder Azure CLI till att skapa din första funktionsapp som körs på Linux. Funktionskoden skapas lokalt och distribueras sedan till Azure med hjälp av [Azure Functions Core Tools](functions-run-local.md).

Följande steg kan användas på en Mac-, Windows- eller Linux-dator. Den här artikeln visar hur du skapar funktioner i JavaScript eller C#. To learn how to create Python functions, see [Create your first Python function using Core Tools and the Azure CLI](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Krav

Innan du kör exemplet måste du ha följande:

+ Install [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 or above.

+ Installera [Azure CLI]( /cli/azure/install-azure-cli). Den här artikeln kräver Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Du kan också använda [Azure Cloud Shell](https://shell.azure.com/bash).

+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Enable extension bundles

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Skapa en Linux-funktionsapp i Azure

Du måste ha en funktionsapp som värd för körning av dina funktioner i Linux. Funktionsappen ger en serverlös miljö för körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp som körs på Linux med hjälp av kommandot [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

I följande kommando använder du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` är även DNS-standarddomänen för funktionsappen. Det här namnet måste vara unikt inom alla appar i Azure. You should also set the `<language>` runtime for your function app, from `dotnet` (C#), `node` (JavaScript/TypeScript), or `python`.

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