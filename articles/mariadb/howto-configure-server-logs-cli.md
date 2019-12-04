---
title: Åtkomst till långsamma Query-loggar – Azure CLI – Azure Database for MariaDB
description: Den här artikeln beskriver hur du kommer åt långsamma loggar i Azure Database for MariaDB med hjälp av kommando rads verktyget för Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 32e73835732538813f90de5cb737429373c3762a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767389"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurera och komma åt långsamma Query-loggar med hjälp av Azure CLI
Du kan hämta Azure Database for MariaDB långsamma frågemeddelanden med hjälp av Azure CLI, kommando rads verktyget för Azure.

## <a name="prerequisites"></a>Krav
För att gå igenom den här instruktions guiden behöver du:
- [Azure Database for MariaDB Server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Konfigurera loggning för Azure Database for MariaDB
Du kan konfigurera servern för att få åtkomst till MariaDB långsamma Query-loggen genom att utföra följande steg:
1. Aktivera loggning genom att ställa in den **långsamma\_frågan\_logg** parameter till på.
2. Justera andra parametrar, till exempel **lång\_fråga\_tid** och **logg\_långsamma\_admin\_-instruktioner**.

Information om hur du ställer in värdet för dessa parametrar via Azure CLI finns i [så här konfigurerar du Server parametrar](howto-configure-server-parameters-cli.md).

Följande CLI-kommando aktiverar till exempel loggen långsam fråga, ställer in tiden för lång tid till 10 sekunder och stänger sedan av loggningen av den långsamma administratörs instruktionen. Slutligen visas konfigurations alternativen för din granskning.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Lista loggar för Azure Database for MariaDB Server
Om du vill visa en lista över tillgängliga långsamma loggfiler för servern kör du kommandot [AZ MariaDB Server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

Du kan visa en lista över loggfilerna för Server **mydemoserver.MariaDB.Database.Azure.com** under resurs gruppen **myresourcegroup**. Dirigera sedan listan över loggfiler till en textfil med namnet **log\_filer\_List. txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Med kommandot [AZ MariaDB Server-logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) kan du hämta enskilda loggfiler för servern.

Använd följande exempel för att hämta den speciella logg filen för servern **mydemoserver.MariaDB.Database.Azure.com** under resurs gruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [långsamma frågemeddelanden i Azure Database for MariaDB](concepts-server-logs.md).
