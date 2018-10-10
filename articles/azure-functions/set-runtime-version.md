---
title: Hur du Azure Functions runtime versioner
description: Azure Functions har stöd för flera versioner av körningen. Lär dig hur du anger runtime-versionen av en funktionsapp i Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: glenga
ms.openlocfilehash: 6d89746c0a2d4642e5025789d352803195c0a3b9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886815"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Hur du Azure Functions runtime versioner

En funktionsapp som körs på en specifik version av Azure Functions-körningen. Det finns två huvudversioner: [1.x och 2.x](functions-versions.md). Som standard fungerar appar som skapats version 2.x av körningsmiljön. Den här artikeln förklarar hur du konfigurerar en funktionsapp i Azure för att köra på den version som du väljer. Information om hur du konfigurerar en lokal utvecklingsmiljö för en specifik version finns i [kod och testa Azure Functions lokalt](functions-run-local.md).

> [!NOTE]
> Du kan inte ändra runtime-versionen för en funktionsapp som har en eller flera funktioner. Du bör använda Azure-portalen för att ändra runtime-versionen.

## <a name="automatic-and-manual-version-updates"></a>Automatisk och manuell versionuppdateringar

Functions kan du anpassa en specifik version av körningen med hjälp av den `FUNCTIONS_EXTENSION_VERSION` inställningen i en funktionsapp. Funktionsappen sparas på den angivna huvudversionen tills du uttryckligen väljer att flytta till en ny version.

Om du anger endast huvudversionen (”~ 2” för 2.x eller ”~ 1” för 1.x) uppdateras appen automatiskt till nya mindre versioner av körningen när de blir tillgängliga. Ny delversioner inför inte ändringar. Om du anger en lägre version (till exempel ”2.0.12345”), fästs funktionsappen till den specifika versionen tills du uttryckligen ändrar den.

När en ny version är allmänt tillgängliga, kan en uppmaning i portalen du att byta till den här versionen. När du har flyttat till en ny version, kan du alltid använda den `FUNCTIONS_EXTENSION_VERSION` inställningen att flytta tillbaka till en tidigare version.

En ändring av körningsversion orsakar en funktionsapp att starta om.

De värden som du kan ange i den `FUNCTIONS_EXTENSION_VERSION` appinställningen för att aktivera Automatiska uppdateringar är för närvarande ”~ 1” för 1.x-körningen och ”~ 2” för 2.x.

## <a name="view-and-update-the-current-runtime-version"></a>Visa och uppdatera den aktuella runtime-versionen

Använd följande procedur om du vill visa runtime-versionen som för närvarande används av en funktionsapp.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till din funktionsapp.

1. Under **konfigurerats funktioner**, Välj **fungera appinställningar**.

    ![Välj funktionsappinställningar](./media/set-runtime-version/add-update-app-setting.png)

1. I den **fungera appinställningar** fliken, leta upp den **körningsversion**. Observera den särskilda körningsversion och den begärda huvudversionen. I exemplet nedan anges versionen till `~2`.

   ![Välj funktionsappinställningar](./media/set-runtime-version/function-app-view-version.png)

1. Om du vill fästa funktionsappen till version 1.x-körningen, Välj **~ 1** under **körningsversion**. Den här växeln är inaktiverad när du har funktioner i din app.

1. Om du har ändrat körningsversion gå tillbaka till den **översikt** och välj **starta om** starta om appen.  Funktionsappen startar om som körs på version 1.x-körningen och version 1.x-mallar används när du skapar funktioner.

## <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Visa och uppdatera runtime-versionen med hjälp av Azure CLI

Du kan också visa och ange den `FUNCTIONS_EXTENSION_VERSION` från Azure CLI.

>[!NOTE]
>Eftersom andra inställningar kan påverkas av runtime-versionen, bör du ändra versionen i portalen. Portalen gör automatiskt de andra nödvändiga uppdateringarna, till exempel Node.js-versionen och runtime stack, när du ändrar körningsversioner.  

Med hjälp av Azure CLI, visa den aktuella runtime-versionen med den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) kommando.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

I den här koden, Ersätt `<function_app>` med namnet på din funktionsapp. Byt även ut `<my_resource_group>` med namnet på resursgruppen för din funktionsapp. 

Du ser den `FUNCTIONS_EXTENSION_VERSION` i följande utdata som har trunkerats för tydlighetens skull:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

Du kan uppdatera den `FUNCTIONS_EXTENSION_VERSION` i funktionsappen med den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) kommando.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Ersätt `<function_app>` med namnet på din funktionsapp. Byt även ut `<my_resource_group>` med namnet på resursgruppen för din funktionsapp. Dessutom måste du ersätta `<version>` med en giltig version av 1.x-körningen eller `~2` för version 2.x.

Du kan köra det här kommandot från den [Azure Cloud Shell](../cloud-shell/overview.md) genom att välja **prova** i ovanstående kodexempel. Du kan också använda den [Azure CLI lokalt](/cli/azure/install-azure-cli) kunna köra kommandot efter körning [az-inloggning](/cli/azure/reference-index#az-login) att logga in.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Körningsmiljön 2.0 i din lokala utvecklingsmiljö](functions-run-local.md)

> [!div class="nextstepaction"]
> [Se viktig information för körningsversioner](https://github.com/Azure/azure-webjobs-sdk-script/releases)
