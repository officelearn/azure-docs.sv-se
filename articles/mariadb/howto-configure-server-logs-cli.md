---
title: Långsamma frågeloggar i Access - Azure CLI - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du kommer åt de långsamma loggarna i Azure Database för MariaDB med hjälp av kommandoradsverktyget Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f33a02ff0e287c135a7d63277cf3d8d3c0cd13d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527664"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurera och komma åt långsamma frågeloggar med hjälp av Azure CLI
Du kan hämta Azure Database för MariaDB långsamma frågeloggar med hjälp av Azure CLI, Azure kommandoradsverktyget.

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- [Azure-databas för MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Konfigurera loggning för Azure Database för MariaDB
Du kan konfigurera servern så att den kommer åt den långsamma frågeloggen för MariaDB genom att vidta följande åtgärder:
1. Aktivera loggning genom att ange parametern **\_långsam frågelogg\_** på PÅ.
2. Justera andra parametrar, till exempel **lång\_frågetid\_** och logga **\_långsamma\_admin-satser\_**.

Mer information om hur du anger värdet för dessa parametrar via Azure CLI finns i [Konfigurera serverparametrar](howto-configure-server-parameters-cli.md).

Följande CLI-kommando aktiverar till exempel den långsamma frågeloggen, anger den långa frågetiden till 10 sekunder och inaktiverar sedan loggningen av den långsamma administratörssatsen. Slutligen visas konfigurationsalternativen för din granskning.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Lista loggar för Azure Database för MariaDB-server
Om du vill visa tillgängliga långsamma frågeloggfiler för servern kör du kommandot [az mariadb server-logs list.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

Du kan lista loggfilerna för server **mydemoserver.mariadb.database.azure.com** under resursgruppen **myresourcegroup**. Sedan rikta listan över loggfiler till en textfil som kallas **loggfiler\_\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Med [az mariadb server-loggar nedladdning](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) kommandot kan du ladda ner enskilda loggfiler för din server.

Använd följande exempel för att hämta den specifika loggfilen för servern **mydemoserver.mariadb.database.azure.com** under resursgruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [långsamma frågeloggar i Azure Database för MariaDB](concepts-server-logs.md).
