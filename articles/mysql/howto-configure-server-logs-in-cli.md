---
title: Åtkomst långsam fråga loggar i Azure Database för MySQL med hjälp av Azure CLI
description: Den här artikeln beskriver hur du kommer åt de långsamma frågeloggarna i Azure Database för MySQL med hjälp av Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: e6d25a4d8b470580626cab4a84f9d912a3f79f75
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612648"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurera och komma åt långsamma frågeloggar med Azure CLI
Du kan hämta Azure Database for MySQL långsamma frågeloggar med hjälp av Azure CLI, Azure kommandoradsverktyget.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md)
- Den [Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging"></a>Konfigurera loggning
Du kan konfigurera servern för att komma åt den långsam frågeloggen för MySQL genom att utföra följande steg:
1. Aktivera loggning av långsamma frågor genom att ange den **långsam\_fråga\_log** parametern on.
2. Justera andra parametrar som **lång\_fråga\_tid** och **log\_långsam\_admin\_instruktioner**.

Läs hur du ställer in värdet för dessa parametrar via Azure CLI i [hur du konfigurerar serverparametrar](howto-configure-server-parameters-using-cli.md).

Till exempel följande CLI-kommando aktiverar långsam frågelogg anger lång Frågetid till 10 sekunder och sedan inaktiverar loggning för långsam admin-instruktionen. Dessutom visas en lista med konfigurationsalternativ för granskning.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista loggar för Azure Database for MySQL-server
Om du vill visa tillgängliga långsam fråga loggfilerna för din server kör den [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) kommando.

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
- Lär dig mer om [långsam fråga loggar i Azure Database för MySQL](concepts-server-logs.md).
