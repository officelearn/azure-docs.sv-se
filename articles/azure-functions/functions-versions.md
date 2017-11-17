---
title: "Så här avsedda för Azure Functions-runtime-versioner"
description: "Azure Functions stöder flera versioner av körningsmiljön. Lär dig hur du anger runtime version av en Azure-värdtjänsten funktionsapp."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: glenga
ms.openlocfilehash: 063232e40b30d03b0ee8b087a602fed0fee3be0a
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Så här avsedda för Azure Functions-runtime-versioner

En funktionsapp körs på en viss version av Azure Functions-runtime. Det finns två viktiga versioner: 1.x och 2.x. Den här artikeln förklarar hur du väljer som högre version som ska användas och hur du konfigurerar en funktionsapp i Azure för att köras på versionen som du väljer. Information om hur du konfigurerar en lokal utvecklingsmiljö för en viss version finns [kod och testa Azure Functions lokalt](functions-run-local.md).

## <a name="differences-between-runtime-1x-and-2x"></a>Skillnader mellan runtime 1.x och 2.x

> [!IMPORTANT] 
> Runtime 1.x är den enda versionen som godkänts för produktion.

| Runtime | Status |
|---------|---------|
|1.x|Allmänt tillgänglig (GA)|
|2.x|Förhandsversion|

I följande avsnitt beskrivs olika språk, bindningar och plattformsoberoende utveckling.

### <a name="languages"></a>Språk

I följande tabell anger vilket programmeringsspråk som stöds i varje version av körningsmiljön.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Mer information finns i [språk som stöds](supported-languages.md).

### <a name="bindings"></a>Bindningar 

Försök bindningar att runtime 1.x stöder inte är tillgängliga i 2.x. Information om stöd för bindningar och andra funktionella luckor i 2.x finns [Runtime 2.0 kända problem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

Runtime 2.x kan du skapa anpassade [bindning tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). Inbyggda bindningar som använder den här modellen för utökning är bara tillgängliga i 2.x; först av dessa är de [Microsoft Graph bindningar](functions-bindings-microsoft-graph.md).

### <a name="cross-platform-development"></a>Plattformsoberoende appar

Runtime 1.x stöder funktionen utveckling endast i portalen eller på Windows. Du kan utveckla och köra Azure Functions i Linux eller macOS med 2.x.

## <a name="automatic-and-manual-version-updates"></a>Automatisk och manuell versionuppdateringar

Funktioner kan du anpassa en viss version av körningsmiljön med hjälp av den `FUNCTIONS_EXTENSION_VERSION` inställningen i en funktionsapp. Funktionen appen sparas på den angivna huvudversionen tills du uttryckligen väljer att flytta till en ny version.

Om du anger bara den högre versionen (”~ 1 för 1.x) eller” betaversion ”för 2.x uppdateras funktionen appen automatiskt till nya mindre versioner av körningsmiljön när de blir tillgängliga. Nya delversioner inför inte viktiga förändringar. Om du anger en lägre version (till exempel ”1.0.11360”), sparas appen funktionen på den här versionen tills du uttryckligen ändrar den. 

När en ny version är offentligt tillgänglig, kan kommandotolken i portalen du flytta till den här versionen. När du har flyttat till en ny version, kan du alltid använda den `FUNCTIONS_EXTENSION_VERSION` programinställning vill flytta tillbaka till en tidigare version.

En ändring av versionen av körningsmiljön gör en funktionsapp att starta om.

De värden som du kan ange i den `FUNCTIONS_EXTENSION_VERSION` app inställningen för att aktivera Automatiska uppdateringar är för närvarande ”~ 1” för 1.x runtime och ”betaversion” för 2.x.

## <a name="view-the-current-runtime-version"></a>Visa den aktuella versionen av körningsmiljön

Använd följande procedur om du vill visa körtidsversion som för närvarande används av en funktionsapp. 

1. I den [Azure-portalen](https://portal.azure.com), navigera till appen med funktionen och under **konfigurerats funktioner**, Välj **fungerar appinställningar**. 

    ![Välj funktionen app-inställningar](./media/functions-versions/add-update-app-setting.png)

2. I den **fungerar appinställningar** , letar du reda på **körtidsversion**. Observera den specifika runtime-versionen och den begärda huvudversionen. I exemplet nedan, FUNKTIONERNA\_tillägget\_VERSION app inställningen `~1`.
 
   ![Välj funktionen app-inställningar](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-version-20-runtime"></a>Version 2.0 har körningsmiljön

>[!IMPORTANT]   
> Azure Functions-runtime 2.0 är en förhandsversion och stöds för närvarande inte alla funktioner i Azure Functions. Mer information finns i [skillnaderna mellan runtime 1.x och 2.x](#differences-between-runtime-1x-and-2x) tidigare i den här artikeln.

Du kan flytta en funktionsapp runtime version 2.0 förhandsgranskning i Azure-portalen. I den **fungerar appinställningar** , Välj **beta** under **körtidsversion**.  

![Välj funktionen app-inställningar](./media/functions-versions/function-app-view-version.png)

Den här inställningen motsvarar inställningen i `FUNCTIONS_EXTENSION_VERSION` inställningen till `beta`. Välj den **~ 1** om du vill flytta tillbaka till aktuellt du offentligt högre version som stöds. Du kan också använda Azure CLI för att uppdatera den här inställningen. 

## <a name="target-a-version-using-the-portal"></a>Använder en version med hjälp av portalen

När du först välja en version än den aktuella huvudversionen eller 2.0, måste du ange den `FUNCTIONS_EXTENSION_VERSION` inställningen.

1. I den [Azure-portalen](https://portal.azure.com), gå till din funktionsapp och under **konfigurerats funktioner**, Välj **programinställningar**.

    ![Välj funktionen app-inställningar](./media/functions-versions/add-update-app-setting1a.png)

2. I den **programinställningar** fliken, söka efter den `FUNCTIONS_EXTENSION_VERSION` inställningen och ändra värdet till en giltig version av körningsmiljön 1.x eller `beta` för version 2.0. 

    ![Ange om funktionen app](./media/functions-versions/add-update-app-setting2.png)

3. Klicka på **spara** spara inställningen programuppdateringen. 

## <a name="target-a-version-using-azure-cli"></a>Rikta en version med Azure CLI

 Du kan också ange den `FUNCTIONS_EXTENSION_VERSION` från Azure CLI. Med hjälp av Azure CLI, uppdatera inställningen i funktionsapp med den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) kommando.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
I den här koden, ersätter `<function_app>` med namnet på funktionen appen. Även ersätta `<my_resource_group>` med namnet på resursgruppen för din funktionsapp. Ersätt `<version>` med en giltig version av körningsmiljön 1.x eller `beta` för version 2.0. 

Du kan köra det här kommandot från den [Azure Cloud Shell](../cloud-shell/overview.md) genom att välja **prova** i föregående kodexempel. Du kan också använda den [Azure CLI lokalt](/cli/azure/install-azure-cli) att köra det här kommandot efter körning [az inloggningen](/cli/azure#az_login) att logga in.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Körningsmiljön 2.0 i din lokala utvecklingsmiljö](functions-run-local.md)

> [!div class="nextstepaction"]
> [Se viktig information för runtime-versioner](https://github.com/Azure/azure-webjobs-sdk-script/releases)