---
title: Köra din första Resource Graph-fråga med hjälp av Azure CLI
description: Den här artikeln vägleder dig igenom stegen för att aktivera Resource Graph-tillägget för Azure CLI och köra din första fråga.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: a252dd6698a5e4f724fcbbf821b01f63ab4f529b
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086819"
---
# <a name="run-your-first-resource-graph-query-using-azure-cli"></a>Köra din första Resource Graph-fråga med hjälp av Azure CLI

Det första steget till att använda Azure Resource Graph är att kontrollera att tillägget för [Azure CLI](/cli/azure/) är installerat. Denna snabbstart vägleder dig genom processen för att lägga till tillägget i Azure CLI-installationen. Du kan använda tillägget med Azure CLI installerat lokalt eller via [Azure Cloud Shell](https://shell.azure.com).

I slutet av den här processen kommer du att ha lagt till tillägget till valfri Azure CLI-installation och kört din första Resource Graph-fråga.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="add-the-resource-graph-extension"></a>Lägga till Resource Graph-tillägget

Om du vill aktivera Azure CLI för att skicka frågor till Azure Resource Graph, måste du lägga till tillägget. Det här tillägget fungerar överallt där Azure CLI kan användas, inklusive [bash i Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (både fristående och i portalen), [Azure CLI-dockeravbildningen](https://hub.docker.com/r/microsoft/azure-cli/) och där det är lokalt installerat.

1. Kontrollera att den senaste versionen av Azure CLI är installerad (minst **2.0.45**). Om den ännu inte är installerad följer du [de här instruktionerna](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. I din valda Azure CLI-miljö importerar du den med följande kommando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Kontrollera att tillägget har installerats och är av den förväntade versionen (minst **0.1.7**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Nu när Azure CLI-tillägget har lagts till i din valda miljö är det dags att testa en enkel Resource Graph-fråga. Frågan returnerar de första fem Azure-resurserna med **namn** och **resurstyp** för varje resurs.

1. Kör din första Azure Resource Graph-fråga med hjälp av tillägget `graph` och kommandot `query`:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Uppdatera frågan till `order by` egenskapen **namn**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Det begränsar först frågeresultaten och sorterar sedan dem.

1. Uppdatera frågan till att först `order by` **Namn**-egenskapen och sedan sätta en `limit` för de fem främsta resultaten:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

När den sista frågan har körts flera gånger, och förutsatt att ingenting i din miljö ändras, kommer resultaten som returneras bli konsekventa och som förväntade – sorterade efter **Namn**-egenskapen men fortfarande begränsade till de fem främsta resultaten.

## <a name="cleanup"></a>Rensa

Om du vill ta bort Resource Graph-tillägget från din Azure CLI-miljö, kan göra du det med hjälp av följande kommando:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

> [!NOTE]
> Detta tar inte bort tilläggsfilen som laddades ned tidigare. Det tar bara bort den från den körda Azure CLI-miljö.

## <a name="next-steps"></a>Nästa steg

- Få mer information om [frågespråket](./concepts/query-language.md)
- Lär dig att [utforska resurser](./concepts/explore-resources.md)
- Kör din första fråga med [Azure PowerShell](first-query-powershell.md)
- Se exempel på [startfrågor](./samples/starter.md)
- Se exempel på [avancerade frågor](./samples/advanced.md)
- Ge feedback på [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)