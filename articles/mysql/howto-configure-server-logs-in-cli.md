---
title: "Åtkomst loggas i Azure-databas för MySQL med Azure CLI | Microsoft Docs"
description: "Den här artikeln beskriver hur du kommer åt de server-loggarna i Azure-databas för MySQL med hjälp av kommandoradsverktyget Azure CLI."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 6ee2c2c6e6cff824d3167ea600a1ddc778ad011b
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Konfigurera och komma åt server-loggar med hjälp av Azure CLI
Du kan hämta Azure-databas för MySQL server-loggar med hjälp av Azure CLI, Azure-kommandoradsverktyget.

## <a name="prerequisites"></a>Krav
Du behöver följande för att gå igenom den här instruktioner:
- [Azure-databas för MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md)
- Den [Azure CLI 2.0](/cli/azure/install-azure-cli) eller använda Azure Cloud Shell i webbläsaren.

## <a name="configure-logging-for-azure-database-for-mysql"></a>Konfigurera loggning för Azure-databas för MySQL
Du kan konfigurera servern för att komma åt MySQL långsam frågeloggen.
1. Aktiverar loggning genom att ange **långsam\_frågan\_loggen** parameter till ON.
2. Justera andra parametrar som **lång\_frågan\_tid** och **loggen\_långsam\_admin\_instruktioner**.

Se [hur du konfigurerar serverparametrar](howto-configure-server-parameters-using-cli.md) att lära dig hur du ställer in värdet för dessa parametrar via Azure CLI.

Till exempel kommandot CLI aktiverar långsam frågeloggen anger tid för tidskrävande fråga till 10 sekunder och inaktiverar loggning för långsam admin-instruktionen. Slutligen visas en lista med konfigurationsalternativ för granskning.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista loggar för Azure-databas för MySQL-server
Om du vill visa tillgängliga loggfilerna för din server kör den [az mysql-serverloggar listan](/cli/azure/mysql/server-logs#list) kommando.

Du kan visa loggfilerna för server **myserver4demo.mysql.database.azure.com** under resursgrupp **myresourcegroup**, och direkt till en textfil med namnet **loggen\_filer \_list.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Den [az mysql-serverloggar hämta](/cli/azure/mysql/server-logs#download) kommandot kan du hämta individuella loggfiler för servern. 

Det här exemplet hämtas specifik loggfil för servern **myserver4demo.mysql.database.azure.com** under resursgrupp **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [loggas i Azure-databas för MySQL](concepts-server-logs.md)
