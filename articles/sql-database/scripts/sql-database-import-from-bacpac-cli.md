---
title: CLI-exempel importera BACPAC-fil Azure SQL Database
description: Exempel skript för Azure CLI för att importera en BACPAC-fil till en SQL-databas
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: load & move data
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 05/24/2019
ms.openlocfilehash: f47d0ec2edf54aaa708c176e92a9e1026b446882
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061841"
---
# <a name="use-cli-to-import-a-bacpac-file-into-an-azure-sql-database"></a>Använd CLI för att importera en BACPAC-fil till en Azure SQL-databas

Detta exempel på Azure CLI-skript importerar en databas från en *. bacpac* -fil till en Azure SQL-databas.  

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/import-from-bacpac/import-from-bacpac.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | Server kommandon. |
| [AZ SQL DB-import](/cli/azure/sql/db#az-sql-db-import) | Kommandot för databas import. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
