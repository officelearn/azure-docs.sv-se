---
title: Konfigurera serverparametrar - Azure CLI - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du konfigurerar tjänstparametrarna i Azure Database för MariaDB med hjälp av kommandoradsverktyget i Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 56975c52b22b90840fb1534187e99f6efa19469e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527681"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Anpassa serverkonfigurationsparametrar med hjälp av Azure CLI
Du kan lista, visa och uppdatera konfigurationsparametrar för en Azure-databas för MariaDB-server med hjälp av Azure CLI, kommandoradsverktyget i Azure. En delmängd av motorkonfigurationer exponeras på servernivå och kan ändras.

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- [En Azure-databas för MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) kommandoradsverktyget eller använd Azure Cloud Shell i webbläsaren.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Lista serverkonfigurationsparametrar för Azure Database för MariaDB-server
Om du vill visa alla ändringsbara parametrar på en server och deras värden kör du kommandot [az mariadb-serverkonfigurationslist.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list)

Du kan ange serverkonfigurationsparametrarna för servern **mydemoserver.mariadb.database.azure.com** under resursgrupp **minresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Information om definitionen av var och en av de angivna parametrarna finns i avsnittet MariaDB-referens på [serversystemvariabler](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Visa information om serverkonfigurationsparameter
Om du vill visa information om en viss konfigurationsparameter för en server kör du kommandot [az mariadb-serverkonfiguration.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show)

I det här exemplet visas information om **mydemoserver.mariadb.database.azure.com** den långsamma **\_\_** konfigurationsparametern för frågeloggserver för server mydemoserver.mariadb.database.azure.com under resursgrupp **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett serverkonfigurationsparametervärde
Du kan också ändra värdet för en viss serverkonfigurationsparameter, som uppdaterar det underliggande konfigurationsvärdet för MariaDB-servermotorn. Om du vill uppdatera konfigurationen använder du kommandot [az mariadb-serverkonfigurationsuppsättning.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) 

Så här uppdaterar du den långsamma **mydemoserver.mariadb.database.azure.com** **\_\_** konfigurationsparametern för frågeloggserver för server mydemoserver.mariadb.database.azure.com under resursgrupp **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Om du vill återställa värdet för en konfigurationsparameter utelämnar du den valfria `--value` parametern och tjänsten tillämpar standardvärdet. I exemplet ovan skulle det se ut som:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Den här koden återställer den **långsamma\_frågeloggkonfigurationen\_** till standardvärdet **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tidszonsparametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tidszonstabellerna

Tidszonstabellerna på servern kan fyllas `az_load_timezone` i genom att anropa den lagrade proceduren från ett verktyg som MariaDB-kommandoraden eller MariaDB Workbench.

> [!NOTE]
> Om du kör `az_load_timezone` kommandot från MariaDB Workbench kan du behöva stänga `SET SQL_SAFE_UPDATES=0;`av felsäkert uppdateringsläge först med .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att säkerställa att tidszonstabellerna är korrekt ifyllda. Om du vill starta om servern använder du [Azure-portalen](howto-restart-server-portal.md) eller [CLI](howto-restart-server-cli.md).

Om du vill visa tillgängliga tidszonsvärden kör du följande kommando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ställa in tidszonen på global nivå

Tidszonen på global nivå kan ställas in med kommandot [az mariadb-serverkonfigurationsuppsättning.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set)

Följande kommando uppdaterar **\_tidszonsserverkonfigurationsparametern** **för** server mydemoserver.mariadb.database.azure.com under resursgrupp **minresourcegroup** till **USA/Stillahavsområdet**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ställa in tidszonen för sessionsnivå

Tidszonen för sessionsnivå kan ställas `SET time_zone` in genom att köra kommandot från ett verktyg som MariaDB-kommandoraden eller MariaDB Workbench. I exemplet nedan anges tidszonen till tidszonen **USA/Stillahavsområdet.**  

```sql
SET time_zone = 'US/Pacific';
```

Se MariaDB-dokumentationen för [datum- och tidsfunktioner](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Nästa steg

- Konfigurera [serverparametrar i Azure-portalen](howto-server-parameters.md)
