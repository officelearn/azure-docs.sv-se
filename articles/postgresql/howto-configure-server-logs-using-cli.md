---
title: Konfigurera och använda serverloggar för PostgreSQL med hjälp av Azure CLI
description: Den här artikeln beskriver hur du konfigurerar och komma åt de server-loggarna i Azure-databas för PostgreSQL med hjälp av Azure CLI-kommandoraden.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 951dcca562c08698b4ce4528d005fc91152ea337
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurera och använda server-loggar med hjälp av Azure CLI
Du kan hämta PostgreSQL server-felloggarna genom att använda kommandoradsgränssnittet (Azure CLI). Dock stöds åtkomst till transaktionsloggar inte. 

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att gå igenom den här instruktioner:
- [Azure-databas för PostgreSQL-server](quickstart-create-server-database-azure-cli.md)
- Den [Azure CLI 2.0](/cli/azure/install-azure-cli) -kommandoradsverktyget eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Konfigurera loggning för Azure-databas för PostgreSQL
Du kan konfigurera servern för att få åtkomst till frågeloggar och i felloggarna. Felloggarna kan ha automatisk vakuum, anslutning och kontrollpunkt information.
1. Aktivera loggning.
2. Om du vill aktivera loggning av frågor, uppdatera **loggen\_instruktionen** och **loggen\_min\_varaktighet\_instruktionen**.
3. Kvarhållningsperiod för uppdateringen.

Mer information finns i [anpassa server konfigurationsparametrar](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Lista loggar för Azure-databas för PostgreSQL-server
Om du vill visa tillgängliga loggfilerna för din server kör den [az postgres serverloggen listan](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) kommando.

Du kan visa loggfilerna för server **mydemoserver.postgres.database.azure.com** under resursgruppen **myresourcegroup**. Dirigera listan över loggfiler till en textfil med namnet **loggen\_filer\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Hämta loggarna lokalt från servern
Med den [az postgres serverloggen hämta](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) kommandot, du kan hämta individuella loggfiler för servern. 

Använd följande exempel för att hämta specifika loggfil för servern **mydemoserver.postgres.database.azure.com** under resursgruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Nästa steg
- Mer information om server-loggar finns [loggas i Azure-databas för PostgreSQL](concepts-server-logs.md).
- Läs mer om serverparametrar [anpassa konfigurationsparametrar för servern med hjälp av Azure CLI](howto-configure-server-parameters-using-cli.md).
