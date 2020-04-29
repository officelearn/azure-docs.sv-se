---
title: CLI-exempel – Lägg till en enskild databas i gruppen redundans-Azure SQL Database
description: Exempel skript för Azure CLI för att skapa en Azure SQL Database enskild databas, lägga till den i en grupp för redundans och testa redundans.
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
ms.openlocfilehash: bc238f08021bb9fb16b8c7319e63acebdfec3948
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061893"
---
# <a name="use-cli-to-add-an-azure-sql-database-into-a-failover-group"></a>Använd CLI för att lägga till en Azure SQL Database i en grupp för redundans

Det här skript exemplet för Azure CLI skapar en enda databas, skapar en grupp för växling vid fel, lägger till databasen i den och testar redundansväxlingen.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| | |
|---|---|
| [AZ SQL DB](/cli/azure/sql/db) | Databas kommandon. |
| [AZ SQL-redundans – grupp](/cli/azure/sql/failover-group) | Kommandon för redundans grupp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
