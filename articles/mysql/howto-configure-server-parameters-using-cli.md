---
title: Konfigurera tjänst parametrarna – Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar tjänst parametrarna i Azure Database for MySQL med hjälp av kommando rads verktyget för Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: a107c5130968ca960036d7e0f948cf6ea5d209a8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350326"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Anpassa Server konfigurations parametrar med hjälp av Azure CLI
Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure Database for MySQL-server med hjälp av Azure CLI, kommando rads verktyget för Azure. En del av motor konfigurationerna exponeras på server nivå och kan ändras. 

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- [En Azure Database for MySQL-Server](quickstart-create-mysql-server-database-using-azure-cli.md)
- Kommando rads verktyget för [Azure CLI](/cli/azure/install-azure-cli) eller Använd Azure Cloud Shell i webbläsaren.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Visa lista med Server konfigurations parametrar för Azure Database for MySQL Server
Om du vill visa alla ändrings bara parametrar på en server och deras värden kör du kommandot [AZ MySQL Server Configuration List](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) .

Du kan ange serverns konfigurations parametrar för servern **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
För definitionen av var och en av de angivna parametrarna, se referens avsnittet MySQL på [Server system-variabler](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visa information om Server konfigurations parameter
Om du vill visa information om en viss konfigurations parameter för en server kör du kommandot [AZ MySQL Server Configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) .

Det här exemplet visar information om konfigurations parametern **långsam @ no__t-1query @ no__t-2log** för Server **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett parameter värde för Server konfiguration
Du kan också ändra värdet för en viss server konfigurations parameter, som uppdaterar det underliggande konfiguration svärdet för MySQL Server-motorn. Om du vill uppdatera konfigurationen använder du kommandot [AZ MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) . 

Uppdatera Server konfigurations parametern **långsam @ no__t-1query @ no__t-2log** för Server **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Om du vill återställa värdet för en konfigurations parameter utelämnar du den valfria parametern `--value` och tjänsten använder standardvärdet. I exemplet ovan skulle det se ut så här:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Den här koden återställer den **långsamma @ no__t-1query @ no__t-2log-** konfigurationen till standardvärdet **.** 

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med parametern tidszon

### <a name="populating-the-time-zone-tables"></a>Fylla i tabellerna tidszon

Tidszon tabellerna på din server kan fyllas genom att anropa den `az_load_timezone` lagrade proceduren från ett verktyg som MySQL-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör den `az_load_timezone` kommandot från MySQL Workbench, du kan behöva inaktivera säker uppdateringsläget första med `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Om du vill visa tillgängliga tidszon värden, kör du följande kommando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Tidszonen global nivå

Du kan ange tids zonen för global nivå med hjälp av kommandot [AZ MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) .

Följande kommando uppdaterar **Time @ no__t-1zone-** serverns konfigurations parameter för Server **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup** till **US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Tidszonen session nivå

Sessionen kan ställas in på tidszonen genom att köra den `SET time_zone` från ett verktyg som MySQL-kommandoraden eller MySQL Workbench. Exemplet nedan anger tidszonen till den **USA / Stillahavsområdet** tidszon.  

```sql
SET time_zone = 'US/Pacific';
```

MySQL-dokumentationen för [datum- och tidsfunktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Nästa steg

- Konfigurera [Server parametrar i Azure Portal](howto-server-parameters.md)