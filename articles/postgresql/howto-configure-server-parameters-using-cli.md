---
title: Konfigurera parametrar för tjänsten i Azure Database för PostgreSQL
description: Den här artikeln beskriver hur du konfigurerar parametrar för tjänsten i Azure Database för PostgreSQL med hjälp av Azure CLI-kommandoraden.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: c88518749129abed1cf43a70b9165035626a780f
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731571"
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Anpassa konfigurationsparametrar för server med Azure CLI
Du kan lista, visa och uppdatera konfigurationsparametrar för en Azure PostgreSQL-server med hjälp av kommandoradsgränssnittet (Azure CLI). En delmängd av motorkonfigurationer är exponerad på servernivå och kan ändras. 

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- Skapa en Azure Database for PostgreSQL-server och databas genom att följa [skapa en Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Installera [Azure CLI](/cli/azure/install-azure-cli) kommandoradsgränssnitt på din dator eller använder den [Azure Cloud Shell](../cloud-shell/overview.md) i Azure portal med din webbläsare.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista konfigurationsparametrar för server för Azure Database för PostgreSQL-server
Om du vill visa alla ändringsbar parametrar i en server och deras värden, kör den [az postgres server configuration list](/cli/azure/postgres/server/configuration) kommando.

Du kan visa konfigurationsparametrar för server för servern **mydemoserver.postgres.database.azure.com** under resursgruppen **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Visa serverkonfiguration parameterinformation
För att visa information om en specifik konfigurationsparameter för en server, kör den [az postgres server configuration show](/cli/azure/postgres/server/configuration) kommando.

Det här exemplet visar information om den **log\_min\_meddelanden** server konfigurationsparametrar för server **mydemoserver.postgres.database.azure.com** under resursgrupp **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Ändra parametervärdet för server-konfiguration
Du kan också ändra värdet för en viss server konfigurationsparameter, som uppdaterar underliggande Konfigurationsvärdet för PostgreSQL-server-motorn. Uppdatera konfigurationen med den [az postgres server configuration set](/cli/azure/postgres/server/configuration) kommando. 

Att uppdatera den **log\_min\_meddelanden** Serverparametern configuration Server **mydemoserver.postgres.database.azure.com** under resursgruppen  **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Om du vill återställa värdet för en konfigurationsparameter du helt enkelt välja att lämna ut den valfria `--value` parametern och tjänsten används standardvärdet. I ovanstående exempel ser det ut som:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Det här kommandot återställer den **log\_min\_meddelanden** konfiguration till standardvärdet **varning**. Mer information om konfiguration och tillåtna värden finns i dokumentationen för PostgreSQL på [serverkonfiguration](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Nästa steg
- Om du vill konfigurera och komma åt loggar, se [serverloggar i Azure Database för PostgreSQL](concepts-server-logs.md)
