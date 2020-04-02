---
title: Konfigurera serverparametrar - Azure CLI - Azure Database för MySQL
description: I den här artikeln beskrivs hur du konfigurerar tjänstparametrarna i Azure Database för MySQL med hjälp av kommandoradsverktyget i Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: ca5f80e57f90e4dd26ac2e4a175998ff3de2c102
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546422"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Anpassa serverparametrar med hjälp av Azure CLI
Du kan lista, visa och uppdatera konfigurationsparametrar för en Azure-databas för MySQL-server med hjälp av Azure CLI, kommandoradsverktyget i Azure. En delmängd av motorkonfigurationer exponeras på servernivå och kan ändras. 

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- [En Azure-databas för MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) kommandoradsverktyget eller använd Azure Cloud Shell i webbläsaren.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista serverkonfigurationsparametrar för Azure Database för MySQL-server
Om du vill visa alla ändringsbara parametrar i en server och deras värden kör du kommandot [az mysql server configuration list.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list)

Du kan ange serverkonfigurationsparametrarna för servern **mydemoserver.mysql.database.azure.com** under resursgrupp **minresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Information om definitionen av var och en av de angivna parametrarna finns i referensavsnittet MySQL på [serversystemvariabler](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visa information om serverkonfigurationsparameter
Om du vill visa information om en viss konfigurationsparameter för en server kör du kommandot [az mysql-serverkonfiguration.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show)

I det här exemplet visas information om **mydemoserver.mysql.database.azure.com** den långsamma **\_\_** konfigurationsparametern för frågeloggserver för server mydemoserver.mysql.database.azure.com under resursgrupp **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett serverkonfigurationsparametervärde
Du kan också ändra värdet för en viss serverkonfigurationsparameter, som uppdaterar det underliggande konfigurationsvärdet för MySQL-servermotorn. Om du vill uppdatera konfigurationen använder du kommandot [az mysql-serverkonfigurationsuppsättning.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) 

Så här uppdaterar du den långsamma **mydemoserver.mysql.database.azure.com** **\_\_** konfigurationsparametern för frågeloggserver för server mydemoserver.mysql.database.azure.com under **resursgrupp myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Om du vill återställa värdet för en konfigurationsparameter utelämnar du den valfria `--value` parametern och tjänsten tillämpar standardvärdet. I exemplet ovan skulle det se ut som:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Den här koden återställer den **långsamma\_frågeloggkonfigurationen\_** till standardvärdet **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tidszonsparametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tidszonstabellerna

Tidszonstabellerna på servern kan fyllas `mysql.az_load_timezone` i genom att anropa den lagrade proceduren från ett verktyg som MySQL-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör `mysql.az_load_timezone` kommandot från MySQL Workbench kan du behöva stänga `SET SQL_SAFE_UPDATES=0;`av felsäkert uppdateringsläge först med .

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

Tidszonen på global nivå kan ställas in med kommandot [az mysql-serverkonfiguration.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set)

Följande kommando uppdaterar **\_tidszonsserverkonfigurationsparametern** **för** server mydemoserver.mysql.database.azure.com under resursgrupp **minresourcegroup** till **USA/Stillahavsområdet**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ställa in tidszonen för sessionsnivå

Tidszonen för sessionsnivå kan ställas `SET time_zone` in genom att köra kommandot från ett verktyg som mysql-kommandoraden eller MySQL Workbench. I exemplet nedan anges tidszonen till tidszonen **USA/Stillahavsområdet.**  

```sql
SET time_zone = 'US/Pacific';
```

Se MySQL-dokumentationen för [datum- och tidsfunktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Nästa steg

- Konfigurera [serverparametrar i Azure-portalen](howto-server-parameters.md)