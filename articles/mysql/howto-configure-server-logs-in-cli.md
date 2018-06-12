---
title: Åtkomst loggas i Azure-databas för MySQL med hjälp av Azure CLI
description: Den här artikeln beskriver hur du kommer åt de server-loggarna i Azure-databas för MySQL med hjälp av kommandoradsverktyget Azure CLI.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 85c7840c0e919e77e807e6114c4d0c65601ff334
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265834"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurera och använda server-loggar med hjälp av Azure CLI
Du kan hämta Azure-databas för MySQL server-loggar med hjälp av Azure CLI, Azure-kommandoradsverktyget.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att gå igenom den här instruktioner:
- [Azure-databas för MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md)
- Den [Azure CLI 2.0](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging-for-azure-database-for-mysql"></a>Konfigurera loggning för Azure-databas för MySQL
Du kan konfigurera servern för att komma åt MySQL långsam frågeloggen genom att utföra följande steg:
1. Aktiverar loggning genom att ange den **långsam\_frågan\_loggen** parameter till ON.
2. Justera andra parametrar som **lång\_frågan\_tid** och **loggen\_långsam\_admin\_instruktioner**.

Information om hur du ställer in värdet för dessa parametrar med Azure CLI finns [hur du konfigurerar serverparametrar](howto-configure-server-parameters-using-cli.md). 

Till exempel kommandot CLI aktiverar långsam frågeloggen anger tid för tidskrävande fråga till 10 sekunder och sedan inaktiverar loggning för långsam admin-instruktionen. Slutligen visas en lista med konfigurationsalternativ för granskning.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista loggar för Azure-databas för MySQL-server
Om du vill visa tillgängliga loggfilerna för din server kör den [az mysql-serverloggar listan](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) kommando.

Du kan visa loggfilerna för server **mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup**. Dirigera listan över loggfiler till en textfil med namnet **loggen\_filer\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Med den [az mysql-serverloggar hämta](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) kommandot, du kan hämta individuella loggfiler för servern. 

Använd följande exempel för att hämta specifika loggfil för servern **mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [loggas i Azure-databas för MySQL](concepts-server-logs.md).
