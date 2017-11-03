---
title: "Så här avsedda för Azure Functions-runtime versioner | Microsoft Docs"
description: "Functions stöder flera versioner av körningsmiljön. Lär dig hur du anger runtime version av en Azure-värdtjänsten funktionsapp."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Så här avsedda för Azure Functions-runtime-versioner

Azure Functions-runtime implementerar serverlösa körning av din kod i Azure. Du hittar den här körning i olika miljöer än finns i Azure. Den [Azure Functions grundläggande verktyg](functions-run-local.md) implementerar det här runtime på utvecklingsdatorn. [Azure Functions-Runtime](functions-runtime-overview.md) kan du använda funktioner i lokala miljöer. 

Functions stöder flera större versioner av körningsmiljön. En högre versionsuppdatering kan introducera viktiga förändringar. Det här avsnittet beskrivs hur du kan anpassa dina funktionen appar till en specifik runtime version när finns i Azure. 

Funktioner kan du anpassa en specifik högre version av körningsmiljön med hjälp av den `FUNCTIONS_EXTENSION_VERSION` inställningen i appen funktion. Detta gäller både offentliga och förhandsgranska versioner. Funktionen appen sparas på den angivna större Körtidsversionen tills du uttryckligen väljer att flytta till en ny version. Du funktionsapp uppdateras till nya delversioner av körningsmiljön när de blir tillgängliga. Lägre versionuppdateringar inför inte viktiga förändringar.  

När en ny högre version är offentligt tillgänglig, ges möjlighet att flytta till den versionen när du visar funktionsapp i portalen. När du har flyttat till en ny version, kan du alltid använda den `FUNCTIONS_EXTENSION_VERSION` programinställning vill flytta tillbaka till en tidigare version av körningsmiljön.

Varje ändring av versionen av körningsmiljön gör funktionen appen för att starta om. Viktig information för alla runtime-versioner (högre och lägre) har publicerats i den [GitHub-lagringsplatsen](https://github.com/Azure/azure-webjobs-sdk-script/releases).   
## <a name="view-the-current-runtime-version"></a>Visa den aktuella versionen av körningsmiljön

Använd följande procedur om du vill visa den specifika körtidsversion som för närvarande används av appen funktionen. 

1. I den [Azure-portalen](https://portal.azure.com), gå till din funktionsapp och under **konfigurerats funktioner**, Välj **fungerar app-inställningar**. 

    ![Välj funktionen app-inställningar](./media/functions-versions/add-update-app-setting.png)

2. I den **fungerar appinställningar** , letar du reda på **körtidsversion**. Observera den specifika runtime-versionen och den begärda huvudversionen. I exemplet nedan anges den högre versionen till `~1.0`.
 
   ![Välj funktionen app-inställningar](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>Version 2.0 har funktioner körningsmiljön

>[!IMPORTANT]   
> Azure Functions-runtime 2.0 är en förhandsversion och stöds för närvarande inte alla funktioner i Azure Functions. Mer information finns i [Azure Functions-runtime 2.0 kända problem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Du kan flytta funktionen appen till runtime version 2.0 förhandsversionen i Azure-portalen. I den **fungerar appinställningar** , Välj **beta** under **körtidsversion**.  

   ![Välj funktionen app-inställningar](./media/functions-versions/function-app-view-version.png)

Den här inställningen motsvarar inställningen i `FUNCTIONS_EXTENSION_VERSION` inställningen till `beta`. Välj den **~ 1** om du vill flytta tillbaka till aktuellt du offentligt högre version som stöds. Du kan också använda Azure CLI för att uppdatera den här inställningen. 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>Mål för en specifik runtime-versionen från portalen

När du behöver riktade till en högre version än den aktuella större version eller 2.0, måste du ange den `FUNCTIONS_EXTENSION_VERSION` inställningen.

1. I den [Azure-portalen](https://portal.azure.com), gå till din funktionsapp och under **konfigurerats funktioner**, Välj **programinställningar**.

    ![Välj funktionen app-inställningar](./media/functions-versions/add-update-app-setting1a.png)

2. I den **programinställningar** fliken, söka efter den `FUNCTIONS_EXTENSION_VERSION` inställningen och ändra värdet till en giltig högre version av körningsmiljön 1.x eller `beta` för version 2.0. 

    ![Ange om funktionen app](./media/functions-versions/add-update-app-setting2.png)

3. Klicka på **spara** spara inställningen programuppdateringen. 

## <a name="target-a-specific-version-using-azure-cli"></a>Använder en viss version med hjälp av Azure CLI

 Du kan också ange den `FUNCTIONS_EXTENSION_VERSION` från Azure CLI. Med hjälp av Azure CLI, uppdatera inställningen i funktionsapp med den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) kommando.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
I den här koden, ersätter `<function_app>` med namnet på funktionen appen. Även ersätta `<my_resource_group>` med namnet på resursgruppen för din funktionsapp. Ersätt `<version>` med en giltig högre version av körningsmiljön 1.x eller `beta` för version 2.0. 

Du kan köra det här kommandot från den [Azure Cloud Shell](../cloud-shell/overview.md) genom att välja **prova** i föregående kodexempel. Du kan också använda den [Azure CLI lokalt](/cli/azure/install-azure-cli) att köra det här kommandot efter körning [az inloggningen](/cli/azure#az_login) att logga in.
