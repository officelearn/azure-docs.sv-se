---
title: Komma åt långsamma Query-loggar i Azure Database for MySQL med hjälp av Azure CLI
description: Den här artikeln beskriver hur du kommer åt långsamma frågemeddelanden i Azure Database for MySQL med hjälp av Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 0ab4162d11642ec7df53040bd744711002227497
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030630"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurera och komma åt långsamma Query-loggar med hjälp av Azure CLI
Du kan hämta Azure Database for MySQL långsamma frågemeddelanden med hjälp av Azure CLI, kommando rads verktyget för Azure.

## <a name="prerequisites"></a>Krav
För att gå igenom den här instruktions guiden behöver du:
- [Azure Database for MySQL Server](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging"></a>Konfigurera loggning
Du kan konfigurera servern för att få åtkomst till MySQL-långsamma Query-loggen genom att utföra följande steg:
1. Aktivera långsam loggning av frågor genom att ställa in den **långsamma\_frågan\_logg** parameter till på.
2. Justera andra parametrar, till exempel **lång\_fråga\_tid** och **logg\_långsamma\_admin\_-instruktioner**.

Information om hur du ställer in värdet för dessa parametrar via Azure CLI finns i [så här konfigurerar du Server parametrar](howto-configure-server-parameters-using-cli.md).

Följande CLI-kommando aktiverar till exempel loggen långsam fråga, ställer in tiden för lång tid till 10 sekunder och stänger sedan av loggningen av den långsamma administratörs instruktionen. Slutligen visas konfigurations alternativen för din granskning.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista loggar för Azure Database for MySQL Server
Om du vill visa en lista över tillgängliga långsamma frågemeddelanden för servern kör du kommandot [AZ MySQL server-logs List](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) .

Du kan visa en lista över loggfilerna för Server **mydemoserver.mysql.Database.Azure.com** under resurs gruppen **myresourcegroup**. Dirigera sedan listan över loggfiler till en textfil med namnet **log\_filer\_List. txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Med kommandot [AZ MySQL server-logs Download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) kan du hämta enskilda loggfiler för servern. 

Använd följande exempel för att hämta den speciella logg filen för servern **mydemoserver.mysql.Database.Azure.com** under resurs gruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [långsamma frågemeddelanden i Azure Database for MySQL](concepts-server-logs.md).
