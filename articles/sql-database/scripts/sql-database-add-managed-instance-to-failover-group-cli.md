---
title: CLI-exempel – failover-grupp – Azure SQL Database Hanterad instans
description: Exempel skript för Azure CLI för att skapa en Azure SQL Database Hanterad instans, lägga till den i en grupp för redundans och testa redundans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 8ffe40662ffaf8a1fb35a3d31acfaea78ea0fbeb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061921"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Använd CLI för att lägga till en Azure SQL Database Hanterad instans i en failover-grupp

Det här Azure CLI-exemplet skapar två hanterade instanser, lägger till dem i en grupp för redundans och testar sedan redundans från den primära hanterade instansen till den sekundära hanterade instansen.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den. Du måste ta bort resurs gruppen två gånger. Om du tar bort resurs gruppen första gången tas den hanterade instansen och virtuella kluster bort, men fel meddelandet `az group delete : Long running operation failed with status 'Conflict'.`kommer att Miss sen. Kör kommandot AZ Group Delete en andra gång för att ta bort eventuella kvarvarande resurser samt resurs gruppen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| | |
|---|---|
| [AZ nätverkets VNet](/cli/azure/network/vnet) | Virtuella nätverks kommandon.  |
| [AZ Network VNet Subnet](/cli/azure/network/vnet/subnet) | Under näts kommandon för virtuellt nätverk. |
| [AZ Network NSG](/cli/azure/network/nsg) | Kommandon för nätverks säkerhets grupp. |
| [AZ nätverks väg-tabell](/cli/azure/network/route-table) | Route Table-kommandon. |
| [AZ SQL mi](/cli/azure/sql/mi) | Kommandon för hanterade instanser. |
| [AZ Network Public-IP](/cli/azure/network/public-ip) | Nätverks kommandon för offentlig IP-adress. |
| [AZ Network VNet-Gateway](/cli/azure/network/vnet-gateway) | Virtual Network Gateway-kommandon. |
| [AZ SQL-instans – redundans-grupp](/cli/azure/sql/instance-failover-group) | Kommandon för redundans grupp för hanterade instanser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
