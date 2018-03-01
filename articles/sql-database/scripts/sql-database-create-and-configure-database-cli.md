---
title: "CLI-exempel – Skapa en Azure SQL-databas | Microsoft Docs"
description: "Använd det här Azure CLI-exempelskriptet för att skapa en SQL-databas."
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 10/11/2017
ms.author: janeng
ms.openlocfilehash: 5824821a2e1f06425cae46cf8b3cf28582e63056
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Använd CLI för att skapa en enda Azure SQL-databas och konfigurera en brandväggsregel

Det här Azure CLI-exempelskriptet skapar en Azure SQL-databas och konfigurerar en brandväggsregel på servernivå. När skriptet har körts utan problem kan SQL-databasen nås från alla Azure-tjänster och den konfigurerade IP-adressen. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Rensa distribution

När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](/cli/azure/sql/server#az_sql_server_create) | Skapar en logisk server som är värd för SQL-databasen. |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | Skapar en brandväggsregel för att tillåta åtkomst till alla SQL-databaser på servern från det angivna IP-adressintervallet. |
| [az sql db create](/cli/azure/sql/db#az_sql_db_create) | Skapar SQL-databasen på den logiska servern. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).

