---
title: Konfigurera parametrar för tjänsten i Azure-databas för MariaDB
description: Den här artikeln beskriver hur du konfigurerar tjänstparametrar i Azure Database for MariaDB med hjälp av kommandoradsverktyget Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 4e0bf45f1c67a5e07d6ed632f6560d094b673c0a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547160"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Anpassa konfigurationsparametrar för server med hjälp av Azure CLI
Du kan visa, visa och uppdatera konfigurationsparametrar för en Azure Database for MariaDB-server med hjälp av Azure CLI, Azure kommandoradsverktyget. En delmängd av motorkonfigurationer är exponerad på servernivå och kan ändras.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- [En Azure Database for MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) -kommandoradsverktyget eller Använd Azure Cloud Shell i webbläsaren.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Lista konfigurationsparametrar för server för Azure Database for MariaDB-server
Om du vill visa alla ändringsbar parametrar i en server och deras värden, kör den [az mariadb-serverlista configuration](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) kommando.

Du kan visa konfigurationsparametrar för server för servern **mydemoserver.mariadb.database.azure.com** under resursgruppen **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Definitionen av var och en av parametrarna i listan, finns i avsnittet MariaDB referens på [Server systemvariabler](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Visa serverkonfiguration parameterinformation
För att visa information om en specifik konfigurationsparameter för en server, kör den [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) kommando.

Det här exemplet visar information om den **långsam\_fråga\_log** server konfigurationsparametrar för server **mydemoserver.mariadb.database.azure.com** under resursgrupp **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett parametervärde för server-konfiguration
Du kan också ändra värdet för en viss server konfigurationsparameter, som uppdaterar underliggande Konfigurationsvärdet för MariaDB server-motorn. Uppdatera konfigurationen med den [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) kommando. 

Att uppdatera den **långsam\_fråga\_log** Serverparametern configuration Server **mydemoserver.mariadb.database.azure.com** under resursgruppen  **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Om du vill återställa värdet för en konfigurationsparameter utelämna det valfria `--value` parametern och tjänsten används standardvärdet. För exemplet ovan ser ut som:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Den här koden återställer den **långsam\_fråga\_log** konfiguration till standardvärdet **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med parametern tidszon

### <a name="populating-the-time-zone-tables"></a>Fylla i tabellerna tidszon

Tidszon tabellerna på din server kan fyllas genom att anropa den `az_load_timezone` lagrade proceduren från ett verktyg som MariaDB från kommandoraden eller MariaDB Workbench.

> [!NOTE]
> Om du kör den `az_load_timezone` kommandot från MariaDB Workbench, du kan behöva inaktivera säker uppdateringsläget första med `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Om du vill visa tillgängliga tidszon värden, kör du följande kommando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Tidszonen global nivå

Den globala nivån tidszonen kan anges med hjälp av den [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) kommando.

Sedan följande kommando för uppdateringar i **tid\_zon** Serverparametern configuration Server **mydemoserver.mariadb.database.azure.com** under resursgruppen  **myresourcegroup** till **USA / Stillahavsområdet**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Tidszonen session nivå

Sessionen kan ställas in på tidszonen genom att köra den `SET time_zone` från ett verktyg som MariaDB från kommandoraden eller MariaDB Workbench. Exemplet nedan anger tidszonen till den **USA / Stillahavsområdet** tidszon.  

```sql
SET time_zone = 'US/Pacific';
```

MariaDB-dokumentationen för [datum- och tidsfunktioner](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Nästa steg

- Så här konfigurerar du [serverparametrar i Azure-portalen](howto-server-parameters.md)
