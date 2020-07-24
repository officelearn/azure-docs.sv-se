---
title: Så här riktar du Azure Functions runtime-versioner
description: Azure Functions stöder flera versioner av körnings miljön. Lär dig hur du anger körnings versionen av en Function-app som finns i Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 3d4e40af1ba1e28bc9e9a433872e1315ffbe7747
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079663"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Så här riktar du Azure Functions runtime-versioner

En Function-App körs på en viss version av Azure Functions Runtime. Det finns tre huvud versioner: [1. x, 2. x och 3. x](functions-versions.md). Som standard skapas Function apps i version 3. x i körnings miljön. Den här artikeln förklarar hur du konfigurerar en Function-app i Azure så att den körs på den version du väljer. Information om hur du konfigurerar en lokal utvecklings miljö för en angiven version finns i [kod-och test Azure Functions lokalt](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Automatiska och manuella versions uppdateringar

Med Azure Functions kan du rikta en specifik version av körningen genom att använda `FUNCTIONS_EXTENSION_VERSION` program inställningen i en Function-app. Function-appen sparas på den angivna huvud versionen tills du uttryckligen väljer att flytta till en ny version.

Om du bara anger huvud versionen uppdateras Function-appen automatiskt till nya lägre versioner av körnings miljön när de blir tillgängliga. Nya del versioner bör inte införa ändringar. Om du anger en lägre version (till exempel "2.0.12345") fästs Function-appen till den aktuella versionen tills du ändrar den explicit.

> [!NOTE]
> Om du fäster till en annan version av Azure Functions och sedan försöker publicera till Azure med hjälp av Visual Studio öppnas ett dialog fönster där du uppmanas att uppdatera till den senaste versionen eller avbryta publiceringen. Undvik detta genom att lägga till `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` egenskapen i `.csproj` filen.

När en ny version är offentligt tillgänglig ger en prompt i portalen dig chansen att flytta upp till den versionen. När du har flyttat till en ny version kan du alltid använda `FUNCTIONS_EXTENSION_VERSION` program inställningen för att gå tillbaka till en tidigare version.

I följande tabell visas `FUNCTIONS_EXTENSION_VERSION` värdena för varje huvud version för att aktivera automatiska uppdateringar:

| Högre version | `FUNCTIONS_EXTENSION_VERSION`värde |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2x  | `~2` |
| 1.x  | `~1` |

En ändring i körnings versionen leder till att en Function-app startar om.

## <a name="view-and-update-the-current-runtime-version"></a>Visa och uppdatera den aktuella körnings versionen

Du kan ändra den körnings version som används av din Function-app. På grund av möjligheten att bryta ändringar kan du bara ändra kör tids versionen innan du har skapat några funktioner i din Function-app. 

> [!IMPORTANT]
> Även om körnings versionen bestäms av `FUNCTIONS_EXTENSION_VERSION` inställningen bör du göra den här ändringen i Azure Portal och inte genom att ändra inställningen direkt. Detta beror på att portalen validerar dina ändringar och gör andra relaterade ändringar efter behov.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Med hjälp av Azure Portal kan du inte ändra körnings versionen för en Function-app som redan innehåller funktioner.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Från Azure CLI

Du kan också visa och ställa in `FUNCTIONS_EXTENSION_VERSION` från Azure CLI.

>[!NOTE]
>Eftersom andra inställningar kan påverkas av körnings versionen bör du ändra versionen i portalen. Portalen gör de andra nödvändiga uppdateringarna automatiskt, till exempel Node.js version och körnings stacken, när du ändrar körnings versioner.  

Använd Azure CLI för att visa den aktuella körnings versionen med kommandot [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

I den här koden ersätter du `<function_app>` med namnet på din Function-app. Ersätt också `<my_resource_group>` med namnet på resurs gruppen för din Function-app. 

Du ser `FUNCTIONS_EXTENSION_VERSION` i följande utdata, som har trunkerats för tydlighetens skull:

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

Du kan uppdatera `FUNCTIONS_EXTENSION_VERSION` inställningen i Function-appen med kommandot [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Ersätt `<function_app>` med namnet på din Function-app. Ersätt också `<my_resource_group>` med namnet på resurs gruppen för din Function-app. Ersätt också `<version>` med en giltig version av 1. x-körningsmiljön eller `~2` för version 2. x.

Du kan köra det här kommandot från [Azure Cloud Shell](../cloud-shell/overview.md) genom att välja **prova** i föregående kod exempel. Du kan också använda [Azure CLI lokalt](/cli/azure/install-azure-cli) för att köra det här kommandot när du har kört [AZ-inloggning](/cli/azure/reference-index#az-login) för att logga in.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Rikta in dig på 2,0-körningsmiljön i din lokala utvecklings miljö](functions-run-local.md)

> [!div class="nextstepaction"]
> [Se viktig information för körnings versioner](https://github.com/Azure/azure-webjobs-sdk-script/releases)
