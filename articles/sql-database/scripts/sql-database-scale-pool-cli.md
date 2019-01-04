---
title: CLI-exempel för skalning av en elastisk SQL-databaspool – Azure SQL Database | Microsoft Docs
description: Skriptexempel för Azure CLI för skalning av en elastisk SQL-databaspool i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: af7af24497054f7868e373150eefae9141239ec6
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605042"
---
# <a name="use-cli-to-scale-a-sql-elastic-pool-in-azure-sql-database"></a>Använda skriptexempel för Azure CLI för skalning av en elastisk SQL-databaspool i Azure SQL Database

Det här skriptexemplet för Azure CLI skapar elastiska SQL-databaspooler, flyttar pooldatabaser och ändrar beräkningsstorlekarna för de elastiska poolerna. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>Rensa distribution

När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, logisk server, SQL-databas och brandväggsregler. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Skapar en logisk server som är värd för SQL-databasen. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create) | Skapar en elastisk databaspool på den logiska servern. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) | Skapar SQL-databasen på den logiska servern. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) | Uppdaterar en elastisk databaspool, i det här exemplet ändras tilldelad eDTU. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
