---
title: Långsamma frågeloggar i Access - Azure CLI - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du kommer åt de långsamma loggarna i Azure Database för MariaDB med hjälp av kommandoradsverktyget Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 75efdd8ed855fe78651fce5828aacb2384052ae5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270544"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurera och komma åt långsamma frågeloggar med hjälp av Azure CLI
Du kan hämta Azure Database för MariaDB långsamma frågeloggar med hjälp av Azure CLI, Azure kommandoradsverktyget.

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- [Azure-databas för MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging"></a>Konfigurera loggning
Du kan konfigurera servern så att den kommer åt den långsamma frågeloggen mysQL genom att vidta följande åtgärder:
1. Aktivera långsam frågeloggning genom att ange parametern **\_långsam frågelogg\_** till PÅ.
2. Välj var loggarna ska matas ut med hjälp av **loggutdata\_**. Om du vill skicka loggar till både lokala lagrings- och Azure Monitor-diagnostikloggar väljer du **Arkiv**. Om du bara vill skicka loggar till Azure Monitor Logs väljer du **Ingen**
3. Justera andra parametrar, till exempel **lång\_frågetid\_** och logga **\_långsamma\_admin-satser\_**.

Mer information om hur du anger värdet för dessa parametrar via Azure CLI finns i [Konfigurera serverparametrar](howto-configure-server-parameters-cli.md).

Följande CLI-kommando aktiverar till exempel den långsamma frågeloggen, anger den långa frågetiden till 10 sekunder och inaktiverar sedan loggningen av den långsamma administratörssatsen. Slutligen visas konfigurationsalternativen för din granskning.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Lista loggar för Azure Database för MariaDB-server
Om **log_output** är konfigurerat till "Arkiv" kan du komma åt loggar direkt från serverns lokala lagring. Om du vill visa tillgängliga långsamma frågeloggfiler för servern kör du kommandot [az mariadb server-logs list.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

Du kan lista loggfilerna för server **mydemoserver.mariadb.database.azure.com** under resursgruppen **myresourcegroup**. Sedan rikta listan över loggfiler till en textfil som kallas **loggfiler\_\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Om **log_output** är konfigurerad till "Arkiv", kan du ladda ner enskilda loggfiler från din server med [az mariadb server-loggar nedladdning](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) kommandot.

Använd följande exempel för att hämta den specifika loggfilen för servern **mydemoserver.mariadb.database.azure.com** under resursgruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [långsamma frågeloggar i Azure Database för MariaDB](concepts-server-logs.md).
