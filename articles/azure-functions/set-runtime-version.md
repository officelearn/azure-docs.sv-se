---
title: Hur du Azure Functions runtime versioner
description: Azure Functions har stöd för flera versioner av körningen. Lär dig hur du anger runtime-versionen av en funktionsapp i Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 889a5a40409238462ee81d3bbd51ac6b77d28173
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947503"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Hur du Azure Functions runtime versioner

En funktionsapp som körs på en specifik version av Azure Functions-körningen. Det finns två huvudversioner: [1.x och 2.x](functions-versions.md). Den här artikeln förklarar hur du konfigurerar en funktionsapp i Azure för att köra på den version som du väljer. Information om hur du konfigurerar en lokal utvecklingsmiljö för en specifik version finns i [kod och testa Azure Functions lokalt](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Automatisk och manuell versionuppdateringar

Functions kan du anpassa en specifik version av körningen med hjälp av den `FUNCTIONS_EXTENSION_VERSION` inställningen i en funktionsapp. Funktionsappen sparas på den angivna huvudversionen tills du uttryckligen väljer att flytta till en ny version.

Om du anger endast huvudversionen (”~ 2” för 2.x eller ”~ 1” för 1.x) uppdateras appen automatiskt till nya mindre versioner av körningen när de blir tillgängliga. Ny delversioner inför inte ändringar. Om du anger en lägre version (till exempel ”2.0.12345”), sparas funktionsappen på den här versionen tills du uttryckligen ändrar den. 

När en ny version är allmänt tillgängliga, kan en uppmaning i portalen du att byta till den här versionen. När du har flyttat till en ny version, kan du alltid använda den `FUNCTIONS_EXTENSION_VERSION` inställningen att flytta tillbaka till en tidigare version.

En ändring av körningsversion orsakar en funktionsapp att starta om.

De värden som du kan ange i den `FUNCTIONS_EXTENSION_VERSION` appinställningen för att aktivera Automatiska uppdateringar är för närvarande ”~ 1” för 1.x-körningen och ”~ 2” för 2.x.

## <a name="view-the-current-runtime-version"></a>Visa den aktuella runtime-versionen

Använd följande procedur om du vill visa runtime-versionen som för närvarande används av en funktionsapp. 

1. I den [Azure-portalen](https://portal.azure.com), navigera till funktionsappen och under **konfigurerats funktioner**, Välj **fungera appinställningar**. 

    ![Välj funktionsappinställningar](./media/functions-versions/add-update-app-setting.png)

2. I den **fungera appinställningar** fliken, leta upp den **körningsversion**. Observera den särskilda körningsversion och den begärda huvudversionen. I exemplet nedan, FUNKTIONERNA\_tillägget\_VERSION app är inställt på `~1`.
 
   ![Välj funktionsappinställningar](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Ha ett versionsmål med hjälp av portalen

När du behöver mot en annan version än den aktuella huvudversionen eller version 2.0 kan ställa in den `FUNCTIONS_EXTENSION_VERSION` programinställningen.

1. I den [Azure-portalen](https://portal.azure.com), navigera till din funktionsapp och under **konfigurerats funktioner**, Välj **programinställningar**.

    ![Välj funktionsappinställningar](./media/functions-versions/add-update-app-setting1a.png)

2. I den **programinställningar** fliken, hitta den `FUNCTIONS_EXTENSION_VERSION` ange och ändra värdet till en giltig version av 1.x-körningen eller `~2` för version 2.0. Tecknet tilde med huvudversion innebär att du använder den senaste versionen av den huvudsakliga versionen (till exempel ”~ 1”). 

    ![Ställa in inställningen funktionen app](./media/functions-versions/add-update-app-setting2.png)

3. Klicka på **spara** att spara inställningen programuppdateringen. 

## <a name="target-a-version-using-azure-cli"></a>Ha ett versionsmål med Azure CLI

 Du kan också ange den `FUNCTIONS_EXTENSION_VERSION` från Azure CLI. Med hjälp av Azure CLI, uppdatera inställningen i funktionsappen med den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) kommando.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
I den här koden, Ersätt `<function_app>` med namnet på din funktionsapp. Byt även ut `<my_resource_group>` med namnet på resursgruppen för din funktionsapp. Ersätt `<version>` med en giltig version av 1.x-körningen eller `~2` för version 2.x. 

Du kan köra det här kommandot från den [Azure Cloud Shell](../cloud-shell/overview.md) genom att välja **prova** i ovanstående kodexempel. Du kan också använda den [Azure CLI lokalt](/cli/azure/install-azure-cli) kunna köra kommandot efter körning [az-inloggning](/cli/azure/reference-index#az-login) att logga in.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Körningsmiljön 2.0 i din lokala utvecklingsmiljö](functions-run-local.md)

> [!div class="nextstepaction"]
> [Se viktig information för körningsversioner](https://github.com/Azure/azure-webjobs-sdk-script/releases)
