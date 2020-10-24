---
title: PowerShell-skript för att skapa ett Azure Cosmos DB-konto med IP-brandvägg
description: Azure PowerShell skript exempel – skapa ett Azure Cosmos DB konto med IP-brandvägg
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 726242b9a4a6585d440322540dc5353bcbe20d29
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488239"
---
# <a name="create-an-azure-cosmos-db-account-with-ip-firewall"></a>Skapa ett Azure Cosmos DB-konto med IP-brandvägg

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

> [!NOTE]
> Det här exemplet visar hur du använder ett SQL (Core) API-konto. Om du vill använda det här exemplet för andra API: er kopierar du de relaterade egenskaperna och tillämpar på ditt API-speciella skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-firewall-create.ps1 "Create an Azure Cosmos account with IP Firewall")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Skapar ett nytt Cosmos DB-konto. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).