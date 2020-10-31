---
title: PowerShell-skript för att skapa resurs lås för Azure Cosmos SQL API-databas och container
description: Skapa resurs lås för Azure Cosmos SQL API-databas och container
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 853780b7c2be3bd574f7c308ef55f7f8f9fc2eec
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074445"
---
# <a name="create-a-resource-lock-for-azure-cosmos-sql-api-database-and-container-using-azure-powershell"></a>Skapa ett resurs lås för Azure Cosmos SQL API-databas och behållare med hjälp av Azure PowerShell
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Resurs lås fungerar inte för ändringar som gjorts av användare som ansluter med hjälp av någon Cosmos DB SDK, verktyg som ansluter via konto nycklar eller Azure Portal om inte Cosmos DB-kontot först är låst med `disableKeyBasedMetadataWriteAccess` egenskapen aktive rad. Mer information om hur du aktiverar den här egenskapen finns i [förhindra ändringar från SDK](../../../role-based-access-control.md#prevent-sdk-changes): er.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
|**Azure-resurs**| |
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Skapar ett resurs lås. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Hämtar ett resurs lås eller visar en lista över resurs lås. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Tar bort ett resurs lås. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell dokumentation](/powershell/).