---
title: Konfigurera parametrar - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du konfigurerar Postgres-parametrar i Azure Database för PostgreSQL - Single Server med Hjälp av Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 4e029428a3709bacdbcd50a6ac3714e730377242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763631"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Anpassa serverkonfigurationsparametrar för Azure Database för PostgreSQL - Single Server med Azure CLI
Du kan lista, visa och uppdatera konfigurationsparametrar för en Azure PostgreSQL-server med hjälp av Kommandoradsgränssnittet (Azure CLI). En delmängd av motorkonfigurationer exponeras på servernivå och kan ändras. 

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- Skapa en Azure-databas för PostgreSQL-server och databas genom att följa [Skapa en Azure-databas för PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Installera Azure CLI-kommandoradsgränssnittet på din dator eller använd [Azure Cloud Shell](../cloud-shell/overview.md) i Azure-portalen med din webbläsare. [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista serverkonfigurationsparametrar för Azure Database för PostgreSQL-server
Om du vill visa alla ändringsbara parametrar på en server och deras värden kör du kommandot [az postgres server configuration list.](/cli/azure/postgres/server/configuration)

Du kan ange serverkonfigurationsparametrarna för servern **mydemoserver.postgres.database.azure.com** under resursgrupp **minresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Visa information om serverkonfigurationsparameter
Om du vill visa information om en viss konfigurationsparameter för en server kör du kommandot [az postgres server configuration show.](/cli/azure/postgres/server/configuration)

I det här exemplet visas information om serverkonfigurationsparametern för **loggmeddelanden\_\_** server för server **mydemoserver.postgres.database.azure.com** under resursgrupp **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Ändra parametervärde för serverkonfiguration
Du kan också ändra värdet för en viss serverkonfigurationsparameter, som uppdaterar det underliggande konfigurationsvärdet för PostgreSQL-servermotorn. Om du vill uppdatera konfigurationen använder du kommandot [az postgres server configuration set.](/cli/azure/postgres/server/configuration) 

Så här uppdaterar du **konfigurationsparametern för loggmini-meddelanden\_\_** server för server **mydemoserver.postgres.database.azure.com** under resursgrupp **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Om du vill återställa värdet för en konfigurationsparameter väljer `--value` du helt enkelt att utelämna den valfria parametern och tjänsten tillämpar standardvärdet. I exemplet ovan skulle det se ut som:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Det här kommandot **\_återställer konfigurationen för\_loggmini-meddelanden** till standardvärdet **VARNING**. Mer information om serverkonfiguration och tillåtna värden finns i PostgreSQL-dokumentationen på [Serverkonfiguration](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du startar om en server](howto-restart-server-cli.md)
- Information om hur du konfigurerar och kommer åt serverloggar finns [i Serverloggar i Azure Database för PostgreSQL](concepts-server-logs.md)
