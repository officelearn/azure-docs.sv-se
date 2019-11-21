---
title: 'Quickstart: Your first Azure CLI query'
description: In this quickstart, you follow the steps to enable the Resource Graph extension for Azure CLI and run your first query.
ms.date: 10/18/2019
ms.topic: quickstart
ms.openlocfilehash: a3ef32b2c2dcaa7a043bbb3e1e859ee295f02f2f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216615"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Quickstart: Run your first Resource Graph query using Azure CLI

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

1. Validate that the extension has been installed and is the expected version (at least **1.0.0**):

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
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Uppdatera frågan till `order by` egenskapen **namn**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Det begränsar först frågeresultaten och sorterar sedan dem.

1. Uppdatera frågan till att först `order by` **Namn**-egenskapen och sedan sätta en `limit` för de fem främsta resultaten:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

När den sista frågan har körts flera gånger, och förutsatt att ingenting i din miljö ändras, kommer resultaten som returneras bli konsekventa och som förväntade – sorterade efter **Namn**-egenskapen men fortfarande begränsade till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort Resource Graph-tillägget från din Azure CLI-miljö, kan göra du det med hjälp av följande kommando:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Nästa steg

- Get more information about the [query language](./concepts/query-language.md).
- Learn more about how to [explore resources](./concepts/explore-resources.md).
- Run your first query by using the [Azure portal](first-query-portal.md).
- Run your first query with [Azure PowerShell](first-query-powershell.md).
- See samples of [Starter queries](./samples/starter.md).
- See samples of [Advanced queries](./samples/advanced.md).
- Provide feedback on [UserVoice](https://feedback.azure.com/forums/915958-azure-governance).