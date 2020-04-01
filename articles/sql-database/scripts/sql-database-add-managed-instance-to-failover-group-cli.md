---
title: CLI exempel- Redundansgrupp - Azure SQL Database hanterad instans
description: Azure CLI-exempelskript för att skapa en hanterad Azure SQL-databas-hanterad instans, lägga till den i en redundansgrupp och testa redundans.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061921"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Använd CLI för att lägga till en hanterad Azure SQL-databas-hanterad instans i en redundansgrupp

Det här Azure CLI-exemplet skapar två hanterade instanser, lägger till dem i en redundansgrupp och testar sedan redundans från den primära hanterade instansen till den sekundära hanterade instansen.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den. Du måste ta bort resursgruppen två gånger. Om du tar bort resursgruppen första gången tas den hanterade instansen `az group delete : Long running operation failed with status 'Conflict'.`och de virtuella klustren bort, men felmeddelandet misslyckas sedan . Kör kommandot az-gruppborttagning en andra gång för att ta bort eventuella kvarvarande resurser samt resursgruppen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempelreferens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| | |
|---|---|
| [az nätverk vnet](/cli/azure/network/vnet) | Kommandon för virtuella nätverk.  |
| [az nätverk vnet undernät](/cli/azure/network/vnet/subnet) | Kommandon för virtuellt nätverksundernät. |
| [az nätverk nsg](/cli/azure/network/nsg) | Kommandon för nätverkssäkerhetsgrupp. |
| [az-nätverksvägtabell](/cli/azure/network/route-table) | Kommandon för flödestabeller. |
| [az sql mi](/cli/azure/sql/mi) | Hanterade instanskommandon. |
| [az nätverk public-ip](/cli/azure/network/public-ip) | Kommandon för offentliga IP-adresser i nätverket. |
| [az nätverk vnet-gateway](/cli/azure/network/vnet-gateway) | Kommandon för Virtual Network Gateway. |
| [az sql-instans-redundansgrupp](/cli/azure/sql/instance-failover-group) | Hanterade förekomst redundansgruppskommandon. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
