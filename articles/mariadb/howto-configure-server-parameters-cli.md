---
title: Konfigurera Server parametrar – Azure CLI – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du konfigurerar tjänst parametrarna i Azure Database for MariaDB med hjälp av kommando rads verktyget för Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 707f4eca440c0e8461420ff0bbc5e67f8e5ad69d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888520"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Anpassa Server konfigurations parametrar med hjälp av Azure CLI
Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure Database for MariaDB-server med hjälp av Azure CLI, kommando rads verktyget för Azure. En del av motor konfigurationerna exponeras på server nivå och kan ändras.

## <a name="prerequisites"></a>Krav
För att gå igenom den här instruktions guiden behöver du:
- [En Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Kommando rads verktyget för [Azure CLI](/cli/azure/install-azure-cli) eller Använd Azure Cloud Shell i webbläsaren.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Visa lista med Server konfigurations parametrar för Azure Database for MariaDB Server
Om du vill visa alla ändrings bara parametrar på en server och deras värden kör du kommandot [AZ MariaDB Server Configuration List](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) .

Du kan ange serverns konfigurations parametrar för servern **mydemoserver.MariaDB.Database.Azure.com** under resurs grupp **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

För definitionen av var och en av de angivna parametrarna, se avsnittet referens för MariaDB på [Server systemets variabler](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Visa information om Server konfigurations parameter
Om du vill visa information om en viss konfigurations parameter för en server kör du kommandot [AZ MariaDB Server Configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) .

Det här exemplet visar information om den **långsamma\_frågan\_logg** Server konfigurations parameter för Server **mydemoserver.MariaDB.Database.Azure.com** under resurs grupp **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett parameter värde för Server konfiguration
Du kan också ändra värdet för en viss server konfigurations parameter, som uppdaterar det underliggande konfiguration svärdet för MariaDB-Server motorn. Om du vill uppdatera konfigurationen använder du kommandot [AZ MariaDB Server Configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) . 

För att uppdatera den **långsamma\_frågan\_logg** Server konfigurations parametern för Server **mydemoserver.MariaDB.Database.Azure.com** under resurs gruppen **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Om du vill återställa värdet för en konfigurations parameter utelämnar du den valfria `--value` parametern, och tjänsten använder standardvärdet. I exemplet ovan skulle det se ut så här:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Den här koden återställer den **långsamma\_frågan\_logg** konfigurationen till standardvärdet **.** 

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med parametern tidszon

### <a name="populating-the-time-zone-tables"></a>Fylla i tabellerna tidszon

Du kan fylla i tids zons tabellerna på servern genom att anropa den `az_load_timezone` lagrade proceduren från ett verktyg som kommando raden för MariaDB eller MariaDB Workbench.

> [!NOTE]
> Om du kör kommandot `az_load_timezone` från MariaDB Workbench kan du först behöva inaktivera säkert uppdaterings läge med `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att se till att tids zons tabellerna är korrekt ifyllda. Om du vill starta om servern använder du [Azure Portal](howto-restart-server-portal.md) eller [CLI](howto-restart-server-cli.md).

Om du vill visa tillgängliga tidszon värden, kör du följande kommando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Tidszonen global nivå

Du kan ange tids zonen för global nivå med hjälp av kommandot [AZ MariaDB Server Configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) .

Följande kommando uppdaterar **tiden\_zon** serverns konfigurations parameter för Server **mydemoserver.MariaDB.Database.Azure.com** under resurs grupp **myresourcegroup** till **US/Pacific**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Tidszonen session nivå

Tids zonen för tids zonen kan anges genom att köra kommandot `SET time_zone` från ett verktyg som MariaDB-kommando rad eller MariaDB Workbench. Exemplet nedan anger tidszonen till den **USA / Stillahavsområdet** tidszon.  

```sql
SET time_zone = 'US/Pacific';
```

I MariaDB-dokumentationen hittar du [datum-och tids funktioner](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Nästa steg

- Konfigurera [Server parametrar i Azure Portal](howto-server-parameters.md)
