---
title: CLI-exempel - skapa en hanterad instans i Azure SQL Database
description: Azure CLI-exempelskript för att skapa en hanterad instans i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 998bd8e39863dd9520d1a05e7fff52095c3be6c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067452"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Använd CLI för att skapa en hanterad Azure SQL-databas-hanterad instans

Det här Azure CLI-skriptexempeln skapar en Azure SQL Database-hanterad instans i ett dedikerat undernät i ett nytt virtuellt nätverk. Den konfigurerar också en vägtabell och en nätverkssäkerhetsgrupp för det virtuella nätverket. När skriptet har körts kan den hanterade instansen nås från det virtuella nätverket eller från en lokal miljö. Se [Konfigurera Azure VM för att ansluta till en hanterad Azure SQL-databas-instans](../sql-database-managed-instance-configure-vm.md) och konfigurera en [point-to-site-anslutning till en Hanterad Azure SQL-databas-instans från lokal](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Om du har begränsningar finns i [regioner som stöds](../sql-database-managed-instance-resource-limits.md#supported-regions) och [prenumerationstyper som stöds](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempelreferens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| | |
|---|---|
| [az nätverk vnet](/cli/azure/network/vnet) | Kommandon för virtuella nätverk. |
| [az nätverk vnet undernät](/cli/azure/network/vnet/subnet) | Kommandon för virtuellt nätverksundernät. |
| [az-nätverksvägtabell](/cli/azure/network/route-table) | Kommandon för nätverksvägtabell. |
| [az sql mi](/cli/azure/sql/mi) | Hanterade instanskommandon. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
