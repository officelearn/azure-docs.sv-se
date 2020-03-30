---
title: Så här inriktar du dig på Azure Functions runtime-versioner
description: Azure Functions stöder flera versioner av körningen. Lär dig hur du anger körningsversionen av en funktionsapp som finns i Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151963"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Så här inriktar du dig på Azure Functions runtime-versioner

En funktionsapp körs på en specifik version av Azure Functions-körningen. Det finns tre huvudversioner: [1.x, 2.x och 3.x](functions-versions.md). Som standard skapas funktionsappar i version 2.x av körningen. I den här artikeln beskrivs hur du konfigurerar en funktionsapp i Azure för att köras på den version du väljer. Information om hur du konfigurerar en lokal utvecklingsmiljö för en viss version finns i [Kod och testa Azure Functions lokalt](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Automatiska och manuella versionsuppdateringar

Med Azure Functions kan du rikta in dig `FUNCTIONS_EXTENSION_VERSION` på en viss version av körningen med hjälp av programinställningen i en funktionsapp. Funktionsappen behålls på den angivna huvudversionen tills du uttryckligen väljer att flytta till en ny version.

Om du bara anger huvudversionen uppdateras funktionsappen automatiskt till nya delversioner av körningen när de blir tillgängliga. Nya delversioner bör inte införa bryta ändringar. Om du anger en delversion (till exempel "2.0.12345" är funktionsappen fäst vid den specifika versionen tills du uttryckligen ändrar den.

> [!NOTE]
> Om du fäster på en viss version av Azure Functions och sedan försöker publicera till Azure med Visual Studio, kommer ett dialogfönster att dyka upp där du uppmanas att uppdatera till den senaste versionen eller avbryta publiceringen. Du kan undvika `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` detta genom `.csproj` att lägga till egenskapen i filen.

När en ny version är allmänt tillgänglig ger en uppmaning i portalen dig chansen att gå upp till den versionen. När du har flyttat till en `FUNCTIONS_EXTENSION_VERSION` ny version kan du alltid använda programinställningen för att gå tillbaka till en tidigare version.

I följande tabell `FUNCTIONS_EXTENSION_VERSION` visas värdena för varje huvudversion för att aktivera automatiska uppdateringar:

| Större version | `FUNCTIONS_EXTENSION_VERSION`Värde |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

En ändring av körningsversionen gör att en funktionsapp startas om.

## <a name="view-and-update-the-current-runtime-version"></a>Visa och uppdatera den aktuella körningsversionen

Du kan ändra den körningsversion som används av funktionsappen. På grund av risken för att bryta ändringar kan du bara ändra körningsversionen innan du har skapat några funktioner i funktionsappen. 

> [!IMPORTANT]
> Även om körningsversionen bestäms `FUNCTIONS_EXTENSION_VERSION` av inställningen, bör du göra den här ändringen i Azure-portalen och inte genom att ändra inställningen direkt. Detta beror på att portalen validerar dina ändringar och gör andra relaterade ändringar efter behov.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Med Hjälp av Azure-portalen kan du inte ändra körningsversionen för en funktionsapp som redan innehåller funktioner.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Från Azure CLI

Du kan också visa `FUNCTIONS_EXTENSION_VERSION` och ange från Azure CLI.

>[!NOTE]
>Eftersom andra inställningar kan påverkas av körningsversionen bör du ändra versionen i portalen. Portalen gör automatiskt de andra nödvändiga uppdateringarna, till exempel Node.js-versionen och runtime stacken, när du ändrar körningsversioner.  

Med hjälp av Azure CLI visar du den aktuella körningsversionen med kommandot [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Ersätt med `<function_app>` namnet på funktionsappen i den här koden. Ersätt `<my_resource_group>` också med namnet på resursgruppen för funktionsappen. 

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

Du kan `FUNCTIONS_EXTENSION_VERSION` uppdatera inställningen i funktionsappen med kommandot [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Ersätt `<function_app>` med namnet på funktionsappen. Ersätt `<my_resource_group>` också med namnet på resursgruppen för funktionsappen. Ersätt också `<version>` med en giltig version av 1.x-körningen eller `~2` för version 2.x.

Du kan köra det här kommandot från [Azure Cloud Shell](../cloud-shell/overview.md) genom att välja Prova det i **föregående** kodexempel. Du kan också använda [Azure CLI lokalt](/cli/azure/install-azure-cli) för att köra det här kommandot efter att ha kört [az-inloggning](/cli/azure/reference-index#az-login) för att logga in.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Rikta in dig på 2,0-körningen i din lokala utvecklingsmiljö](functions-run-local.md)

> [!div class="nextstepaction"]
> [Se Viktig information för runtime-versioner](https://github.com/Azure/azure-webjobs-sdk-script/releases)
