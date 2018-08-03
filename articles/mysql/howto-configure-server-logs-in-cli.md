---
title: Öppna serverloggar i Azure Database för MySQL med hjälp av Azure CLI
description: Den här artikeln beskriver hur du kommer åt serverloggar i Azure Database för MySQL med hjälp av verktyget Azure CLI.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 57b72ded77484dc1c8ca4c62811b62e171365db4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423465"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurera och öppna serverloggar med hjälp av Azure CLI
Du kan hämta Azure Database for MySQL server-loggar med hjälp av Azure CLI, Azure kommandoradsverktyget.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md)
- Den [Azure CLI 2.0](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging-for-azure-database-for-mysql"></a>Konfigurera loggning för Azure Database for MySQL
Du kan konfigurera servern för att komma åt den långsam frågeloggen för MySQL genom att utföra följande steg:
1. Aktivera loggning genom att ange den **långsam\_fråga\_log** parametern on.
2. Justera andra parametrar som **lång\_fråga\_tid** och **log\_långsam\_admin\_instruktioner**.

Läs hur du ställer in värdet för dessa parametrar via Azure CLI i [hur du konfigurerar serverparametrar](howto-configure-server-parameters-using-cli.md). 

Till exempel följande CLI-kommando aktiverar långsam frågelogg anger lång Frågetid till 10 sekunder och sedan inaktiverar loggning för långsam admin-instruktionen. Dessutom visas en lista med konfigurationsalternativ för granskning.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista loggar för Azure Database for MySQL-server
Om du vill visa tillgängliga loggfilerna för din server kör den [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) kommando.

Du kan visa loggfilerna för server **mydemoserver.mysql.database.azure.com** under resursgrupp **myresourcegroup**. Dirigera listan över loggfiler till en textfil med namnet **log\_filer\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Med den [az mysql server-logs hämta](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) kommandot, du kan hämta individuella loggfiler för din server. 

Använd följande exempel för att hämta specifik loggfil för servern **mydemoserver.mysql.database.azure.com** under resursgrupp **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [serverloggar i Azure Database for MySQL](concepts-server-logs.md).
