---
title: Azure PowerShell-skript – Skapa ett Azure Cosmos DB MongoDB API-konto | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Skapa ett Azure Cosmos DB MongoDB API-konto
services: cosmos-db
documentationcenter: cosmosdb
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/29/2018
ms.author: sngun
ms.openlocfilehash: d3f73651dda3dd57740d61fd341baf5700a20964
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "41918695"
---
# <a name="azure-cosmos-db-create-a-mongodb-api-account-using-powershell"></a>Azure Cosmos DB: Skapa ett MongoDB API-konto med hjälp av PowerShell

Med det här skriptexemplet för PowerShell skapas ett Azure Cosmos DB MongoDB API-konto. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/cosmosdb/create-and-configure-mongodb-database/create-and-configure-mongodb-database.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Create an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>Rensa distribution

När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresource?view=azurermps-3.8.0) | Skapar en logisk server som är värd för en databas eller elastisk pool. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Fler skriptexempel för PowerShell i Azure Cosmos DB finns i [PowerShell-skript för Azure Cosmos DB](../powershell-samples.md).