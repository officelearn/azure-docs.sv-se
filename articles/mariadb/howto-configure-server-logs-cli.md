---
title: Åtkomst till serverloggar i Azure Database for MariaDB med hjälp av Azure CLI
description: Den här artikeln beskriver hur du kommer åt serverloggar i Azure Database for MariaDB med hjälp av verktyget Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: 403f111882b1bd151b26af56fd614355e7ba88e3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539219"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurera och öppna serverloggar med hjälp av Azure CLI
Du kan hämta Azure Database for MariaDB serverloggar med hjälp av Azure CLI, Azure kommandoradsverktyget.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- [Azure Database for MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Den [Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Konfigurera loggning för Azure Database for MariaDB
Du kan konfigurera servern för att komma åt den MariaDB långsam frågeloggen genom att utföra följande steg:
1. Aktivera loggning genom att ange den **långsam\_fråga\_log** parametern on.
2. Justera andra parametrar som **lång\_fråga\_tid** och **log\_långsam\_admin\_instruktioner**.

Läs hur du ställer in värdet för dessa parametrar via Azure CLI i [hur du konfigurerar serverparametrar](howto-configure-server-parameters-cli.md).

Till exempel följande CLI-kommando aktiverar långsam frågelogg anger lång Frågetid till 10 sekunder och sedan inaktiverar loggning för långsam admin-instruktionen. Dessutom visas en lista med konfigurationsalternativ för granskning.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Lista loggar för Azure Database for MariaDB-server
Om du vill visa tillgängliga loggfilerna för din server kör den [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) kommando.

Du kan visa loggfilerna för server **mydemoserver.mariadb.database.azure.com** under resursgrupp **myresourcegroup**. Dirigera listan över loggfiler till en textfil med namnet **log\_filer\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Med den [az mariadb-serverloggar hämta](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) kommandot, du kan hämta individuella loggfiler för din server.

Använd följande exempel för att hämta specifik loggfil för servern **mydemoserver.mariadb.database.azure.com** under resursgrupp **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [serverloggar i Azure Database for MariaDB](concepts-server-logs.md).
