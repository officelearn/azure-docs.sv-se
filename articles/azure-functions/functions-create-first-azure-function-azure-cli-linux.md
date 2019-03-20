---
title: Skapa din första Linux-funktion i Azure
description: Lär dig hur du skapar din första Linux-funktion i Azure med hjälp av Azure Functions Core Tools och Azure CLI.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 03/12/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc, fasttrack-edit
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: 78c2f599ba7d22e6de070f5867398e111a396d45
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849745"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Skapa din första Linux-funktion med hjälp av Core Tools och Azure CLI (förhandsversion)

Med Azure Functions kan du köra kod i en [serverfri](https://azure.com/serverless) Linux-miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. Linux-värd kräver [Functions 2.0 runtime](functions-versions.md). Stöd för att köra en funktionsapp i Linux i den serverfria [förbrukningsplan](functions-scale.md#consumption-plan) förhandsvisas just nu. Mer information finns i [den här förhandsversionen överväganden artikeln](https://aka.ms/funclinux).

Den här snabbstartsartikeln går igenom hur du använder Azure CLI till att skapa din första funktionsapp som körs på Linux. Funktionskoden skapas lokalt och distribueras sedan till Azure med hjälp av [Azure Functions Core Tools](functions-run-local.md).

Följande steg kan användas på en Mac-, Windows- eller Linux-dator. Den här artikeln visar hur du skapar funktioner i JavaScript eller C#. Information om hur du lär dig att skapa Python-funktioner finns i [Skapa din första Python-funktion med hjälp av Core Tools och Azure CLI (förhandsversion)](functions-create-first-function-python.md).

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

Använd piltangenterna för att välja en Worker-körtid från följande språkval:

+ `dotnet`: skapar ett .NET-klassbiblioteksprojekt (.csproj).
+ `node`: skapar ett JavaScript- eller TypeScript-projekt. Välj `JavaScript`.
+ `python`: skapar ett Python-projekt. Information om Python-funktioner finns i [Python-snabbstarten](functions-create-first-function-python.md).

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

## <a name="create-a-linux-function-app-in-azure"></a>Skapa en Linux-funktionsapp i Azure

Du måste ha en funktionsapp som värd för körning av dina funktioner i Linux. Funktionsappen ger en serverlös miljö för körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp som körs på Linux med hjälp av kommandot [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

I följande kommando använder du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` är även DNS-standarddomänen för funktionsappen. Det här namnet måste vara unikt inom alla appar i Azure. Du bör också ange den `<language>` runtime för din funktionsapp från `dotnet` (C#), `node` (JavaScript/TypeScript) eller `python`.

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