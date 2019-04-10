---
title: CLI-exempel – Skapa en Azure SQL-databas | Microsoft Docs
description: Använd det här Azure CLI-exempelskriptet för att skapa en SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
manager: craigg
ms.author: carlrab
ms.reviewer: ''
ms.date: 02/08/2019
ms.openlocfilehash: 9ed1512053c06108178275b0523beede470325a5
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361436"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Använd CLI för att skapa en enda Azure SQL-databas och konfigurera en brandväggsregel

Det här Azure CLI-exempelskriptet skapar en Azure SQL-databas och konfigurerar en brandväggsregel på servernivå. När skriptet har körts utan problem kan SQL-databasen nås från alla Azure-tjänster och den konfigurerade IP-adressen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ-grupp](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [Skapa AZ SQLServer](/cli/azure/sql/server#az-sql-server-create) | Skapar en SQL Database-server som är värd för en enkel databas eller en elastisk pool. |
| [Skapa AZ sql server-brandväggen](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Skapar en brandväggsregel för att tillåta åtkomst till alla enkla databaser och elastiska pooler på SQL Database-servern från det angivna IP-adressintervallet. |
| [Skapa AZ sql db](/cli/azure/sql/db#az-sql-db-create) | Skapar en enkel databas eller en elastisk pool. |
| [ta bort AZ-grupp](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).