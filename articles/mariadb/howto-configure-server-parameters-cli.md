---
title: Konfigurera Server parametrar – Azure CLI – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du konfigurerar tjänst parametrarna i Azure Database for MariaDB med hjälp av kommando rads verktyget för Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 6616bd8172e9bc049a6e0e2c687390197de2f391
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767321"
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

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tids zons parametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tids zons tabellerna

Du kan fylla i tids zons tabellerna på servern genom att anropa den `az_load_timezone` lagrade proceduren från ett verktyg som kommando raden för MariaDB eller MariaDB Workbench.

> [!NOTE]
> Om du kör kommandot `az_load_timezone` från MariaDB Workbench kan du först behöva inaktivera säkert uppdaterings läge med `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Kör följande kommando för att visa tillgängliga tids zons värden:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ange tidszon på global nivå

Du kan ange tids zonen för global nivå med hjälp av kommandot [AZ MariaDB Server Configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) .

Följande kommando uppdaterar **tiden\_zon** serverns konfigurations parameter för Server **mydemoserver.MariaDB.Database.Azure.com** under resurs grupp **myresourcegroup** till **US/Pacific**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ange tids zonen för sessionen

Tids zonen för tids zonen kan anges genom att köra kommandot `SET time_zone` från ett verktyg som MariaDB-kommando rad eller MariaDB Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.  

```sql
SET time_zone = 'US/Pacific';
```

I MariaDB-dokumentationen hittar du [datum-och tids funktioner](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Nästa steg

- Konfigurera [Server parametrar i Azure Portal](howto-server-parameters.md)
