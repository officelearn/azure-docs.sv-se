---
title: Konfigurera parametrar för tjänsten i Azure Database för MySQL
description: Den här artikeln beskriver hur du konfigurerar parametrar för tjänsten i Azure Database for MySQL med kommandoradsverktyget Azure CLI.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: 637e2d27e92c1a2618fcf8b524e475a4d2f88f12
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136380"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Anpassa konfigurationsparametrar för server med hjälp av Azure CLI
Du kan visa, visa och uppdatera konfigurationsparametrar för en Azure Database for MySQL-server med hjälp av Azure CLI, Azure kommandoradsverktyget. En delmängd av motorkonfigurationer är exponerad på servernivå och kan ändras. 

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- [En Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) -kommandoradsverktyget eller Använd Azure Cloud Shell i webbläsaren.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista konfigurationsparametrar för server för Azure Database för MySQL-server
Om du vill visa alla ändringsbar parametrar i en server och deras värden, kör den [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) kommando.

Du kan visa konfigurationsparametrar för server för servern **mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Definitionen av var och en av parametrarna i listan, finns i referensavsnittet MySQL på [Server systemvariabler](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visa serverkonfiguration parameterinformation
För att visa information om en specifik konfigurationsparameter för en server, kör den [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) kommando.

Det här exemplet visar information om den **långsam\_fråga\_log** server konfigurationsparametrar för server **mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett parametervärde för server-konfiguration
Du kan också ändra värdet för en viss server konfigurationsparameter, som uppdaterar underliggande Konfigurationsvärdet för MySQL server-motorn. Uppdatera konfigurationen med den [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) kommando. 

Att uppdatera den **långsam\_fråga\_log** Serverparametern configuration Server **mydemoserver.mysql.database.azure.com** under resursgruppen  **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Om du vill återställa värdet för en konfigurationsparameter utelämna det valfria `--value` parametern och tjänsten används standardvärdet. För exemplet ovan ser ut som:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Den här koden återställer den **långsam\_fråga\_log** konfiguration till standardvärdet **OFF**. 

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

Den globala nivån tidszonen kan anges med hjälp av den [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) kommando.

Sedan följande kommando för uppdateringar i **tid\_zon** Serverparametern configuration Server **mydemoserver.mysql.database.azure.com** under resursgruppen  **myresourcegroup** till **USA / Stillahavsområdet**.

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

- Så här konfigurerar du [serverparametrar i Azure-portalen](howto-server-parameters.md)