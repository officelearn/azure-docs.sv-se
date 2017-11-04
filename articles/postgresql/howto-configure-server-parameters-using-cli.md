---
title: "Konfigurera parametrar för tjänsten i Azure-databas för PostgreSQL | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar parametrar för tjänsten i Azure-databas för PostgreSQL med hjälp av Azure CLI-kommandoraden."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 96c5ab5caa4fea178a3108947fa858d395650e08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Anpassa konfigurationsparametrar för servern med hjälp av Azure CLI
Du kan visa, visa och uppdatera konfigurationsparametrar för en Azure PostgreSQL-server via kommandoradsgränssnittet (Azure CLI). En delmängd av motorkonfigurationer är exponerad på servernivå och kan ändras. 

## <a name="prerequisites"></a>Krav
Du behöver följande för att gå igenom den här instruktioner:
- Skapa en Azure-databas för PostgreSQL-servern och databasen genom att följa [skapa en Azure-databas för PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Installera [Azure CLI 2.0](/cli/azure/install-azure-cli) kommandoradsgränssnittet på din dator eller använder den [Azure Cloud Shell](../cloud-shell/overview.md) i Azure portal med din webbläsare.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista server konfigurationsparametrar för Azure-databas för PostgreSQL-server
Om du vill visa en lista med alla parametrar som kan ändras i en server och deras värden, kör den [az postgres server konfigurationslistan](/cli/azure/postgres/server/configuration#list) kommando.

Du kan ange serverns konfigurationsparametrar för servern **mypgserver 20170401.postgres.database.azure.com** under resursgrupp **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="show-server-configuration-parameter-details"></a>Visa serverkonfiguration parameterinformation
Om du vill visa information om en specifik konfigurationsparameter för en server som kör den [az postgres server configuration visa](/cli/azure/postgres/server/configuration#show) kommando.

Det här exemplet visas information om den **loggen\_min\_meddelanden** server konfigurationsparameter för server **mypgserver 20170401.postgres.database.azure.com** under resursgruppen **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="modify-server-configuration-parameter-value"></a>Ändra parametervärdet för server-konfiguration
Du kan också ändra värdet för en viss server konfigurationsparameter, som uppdaterar det underliggande Konfigurationsvärdet som för server-databasmotorn PostgreSQL. Uppdatera konfigurationen med den [az postgres server configuration set](/cli/azure/postgres/server/configuration#set) kommando. 

Uppdatera den **loggen\_min\_meddelanden** server konfigurationsparameter Server **mypgserver 20170401.postgres.database.azure.com** under resursgrupp **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401 --value INFO
```
Om du vill återställa värdet för en konfigurationsparameter du helt enkelt välja att lämna ut den valfria `--value` parameter och tjänsten används standardvärdet. I ovanstående exempel ser det ut som:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
Det här kommandot återställer den **loggen\_min\_meddelanden** konfiguration till standardvärdet **varning**. Mer information om konfiguration och tillåtna värden finns i dokumentationen för PostgreSQL på [serverkonfiguration](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Nästa steg
- Om du vill konfigurera och komma åt server-loggar finns [Server-loggar i Azure-databas för PostgreSQL](concepts-server-logs.md)
