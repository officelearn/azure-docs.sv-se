---
title: CLI-exempel – Skapa en Azure SQL-databas | Microsoft Docs
description: Använd det här Azure CLI-exempelskriptet för att skapa en SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
manager: craigg
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 2a5fb6fcd9b1f0c327df23741284ec0d5fc26c71
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447866"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Använd CLI för att skapa en enda Azure SQL-databas och konfigurera en brandväggsregel

Det här Azure CLI-exempelskriptet skapar en Azure SQL-databas och konfigurerar en brandväggsregel på servernivå. När skriptet har körts utan problem kan SQL-databasen nås från alla Azure-tjänster och den konfigurerade IP-adressen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Skapa en SQL Database-server som är värd för en enskild databas eller en elastisk pool. |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Skapar en brandväggsregel för att tillåta åtkomst till alla enkla databaser och elastiska pooler på SQL Database-servern från det angivna IP-adressintervallet. |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create) | Skapar en enkel databas eller en elastisk pool. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
