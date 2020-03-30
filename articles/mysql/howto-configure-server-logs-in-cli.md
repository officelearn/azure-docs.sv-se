---
title: Komma åt långsamma frågeloggar - Azure CLI - Azure Database för MySQL
description: I den här artikeln beskrivs hur du kommer åt de långsamma frågeloggarna i Azure Database for MySQL med hjälp av Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 93840af61a69599447588be01869a20290b2db94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062487"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurera och komma åt långsamma frågeloggar med hjälp av Azure CLI
Du kan hämta Azure Database for MySQL långsam fråga loggar med hjälp av Azure CLI, Azure kommandoradsverktyget.

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- [Azure-databas för MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging"></a>Konfigurera loggning
Du kan konfigurera servern så att den kommer åt den långsamma frågeloggen mysQL genom att vidta följande åtgärder:
1. Aktivera långsam frågeloggning genom att ange parametern **\_långsam frågelogg\_** till PÅ.
2. Justera andra parametrar, till exempel **lång\_frågetid\_** och logga **\_långsamma\_admin-satser\_**.

Mer information om hur du anger värdet för dessa parametrar via Azure CLI finns i [Konfigurera serverparametrar](howto-configure-server-parameters-using-cli.md).

Följande CLI-kommando aktiverar till exempel den långsamma frågeloggen, anger den långa frågetiden till 10 sekunder och inaktiverar sedan loggningen av den långsamma administratörssatsen. Slutligen visas konfigurationsalternativen för din granskning.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista loggar för Azure Database för MySQL-server
Om du vill visa de tillgängliga långsamma frågeloggfilerna för servern kör du kommandot [az mysql server-logs list.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

Du kan lista loggfilerna för server **mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup**. Sedan rikta listan över loggfiler till en textfil som kallas **loggfiler\_\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Med [az mysql server-loggar nedladdning](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) kommando, kan du ladda ner enskilda loggfiler för din server. 

Använd följande exempel för att hämta den specifika loggfilen för servern **mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [långsamma frågeloggar i Azure Database för MySQL](concepts-server-logs.md).
