---
title: Hur du Azure Functions runtime versioner
description: Azure Functions har stöd för flera versioner av körningen. Lär dig hur du anger runtime-versionen av en funktionsapp i Azure.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: glenga
ms.openlocfilehash: 6e8142e391dd02e78be42e1f16ae2626b74c41c3
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734478"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Hur du Azure Functions runtime versioner

En funktionsapp som körs på en specifik version av Azure Functions-körningen. Det finns två större versioner: [1.x och 2.x](functions-versions.md). Som standard fungerar appar som skapats version 2.x av körningsmiljön. Den här artikeln förklarar hur du konfigurerar en funktionsapp i Azure för att köra på den version som du väljer. Information om hur du konfigurerar en lokal utvecklingsmiljö för en specifik version finns i [kod och testa Azure Functions lokalt](functions-run-local.md).

> [!NOTE]
> Du kan inte ändra runtime-versionen för en funktionsapp som har en eller flera funktioner. Du bör använda Azure-portalen för att ändra runtime-versionen.

## <a name="automatic-and-manual-version-updates"></a>Automatisk och manuell versionuppdateringar

Functions kan du anpassa en specifik version av körningen med hjälp av den `FUNCTIONS_EXTENSION_VERSION` inställningen i en funktionsapp. Funktionsappen sparas på den angivna huvudversionen tills du uttryckligen väljer att flytta till en ny version.

Om du anger endast huvudversionen (”~ 2” för 2.x eller ”~ 1” för 1.x) uppdateras appen automatiskt till nya mindre versioner av körningen när de blir tillgängliga. Ny delversioner inför inte ändringar. Om du anger en lägre version (till exempel ”2.0.12345”), fästs funktionsappen till den specifika versionen tills du uttryckligen ändrar den.

När en ny version är allmänt tillgängliga, kan en uppmaning i portalen du att byta till den här versionen. När du har flyttat till en ny version, kan du alltid använda den `FUNCTIONS_EXTENSION_VERSION` inställningen att flytta tillbaka till en tidigare version.

En ändring av körningsversion orsakar en funktionsapp att starta om.

De värden som du kan ange i den `FUNCTIONS_EXTENSION_VERSION` appinställningen för att aktivera Automatiska uppdateringar är för närvarande ”~ 1” för 1.x-körningen och ”~ 2” för 2.x.

## <a name="view-and-update-the-current-runtime-version"></a>Visa och uppdatera den aktuella runtime-versionen

Du kan ändra runtime-versionen som används av funktionsappen. På grund av potentialen i större ändringar, bör du bara ändra runtime-versionen för att du har skapat några funktioner i din funktionsapp. Även om körningsversion bestäms av den `FUNCTIONS_EXTENSION_VERSION` inställningen, bör du se den här ändringen i Azure-portalen och inte genom att ändra inställningen direkt. Det beror på portalen verifierar dina ändringar och gör andra relaterade ändringar efter behov.

### <a name="from-the-azure-portal"></a>Från Azure portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Med Azure CLI

Du kan också visa och ange den `FUNCTIONS_EXTENSION_VERSION` från Azure CLI.

>[!NOTE]
>Eftersom andra inställningar kan påverkas av runtime-versionen, bör du ändra versionen i portalen. Portalen gör automatiskt de andra nödvändiga uppdateringarna, till exempel Node.js-versionen och runtime stack, när du ändrar körningsversioner.  

Med hjälp av Azure CLI, visa den aktuella runtime-versionen med den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) kommando.

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

Du kan uppdatera den `FUNCTIONS_EXTENSION_VERSION` i funktionsappen med den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) kommando.

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
