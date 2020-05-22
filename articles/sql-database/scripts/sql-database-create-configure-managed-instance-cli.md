---
title: CLI-exempel – skapa en hanterad instans i Azure SQL Database
description: Exempel skript för Azure CLI för att skapa en hanterad instans i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: f52a460be33b23da3da0cb32075ab5f75bb8d129
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772637"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Använd CLI för att skapa en Azure SQL Database Hanterad instans

Detta exempel på Azure CLI-skript skapar en Azure SQL Database Hanterad instans i ett dedikerat undernät i ett nytt virtuellt nätverk. Den konfigurerar också en routningstabell och en nätverks säkerhets grupp för det virtuella nätverket. När skriptet har körts kan den hanterade instansen nås från det virtuella nätverket eller från en lokal miljö. Se [Konfigurera virtuell Azure-dator för att ansluta till en Azure SQL Database Hanterad instans](../sql-database-managed-instance-configure-vm.md) och [Konfigurera en punkt-till-plats-anslutning till en Azure SQL Database Hanterad instans från den lokala](../sql-database-managed-instance-configure-p2s.md)datorn.

> [!IMPORTANT]
> För begränsningar, se [regioner som stöds](../sql-database-managed-instance-resource-limits.md#supported-regions) och [prenumerations typer som stöds](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| | |
|---|---|
| [AZ nätverkets VNet](/cli/azure/network/vnet) | Virtuella nätverks kommandon. |
| [AZ Network VNet Subnet](/cli/azure/network/vnet/subnet) | Under näts kommandon för virtuellt nätverk. |
| [AZ nätverks väg-tabell](/cli/azure/network/route-table) | Kommandon för nätverks väg tabell. |
| [AZ SQL mi](/cli/azure/sql/mi) | Kommandon för hanterade instanser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
