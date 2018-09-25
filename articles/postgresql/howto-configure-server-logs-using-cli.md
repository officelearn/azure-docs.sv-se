---
title: Konfigurera och komma åt loggar för PostgreSQL med Azure CLI
description: Den här artikeln beskriver hur du konfigurerar och få åtkomst till serverloggar i Azure Database för PostgreSQL med hjälp av Azure CLI-kommandoraden.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: af5a0843acced4611b219088b7662f9ea4b32beb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969594"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurera och öppna serverloggar med hjälp av Azure CLI
Du kan hämta felloggar för PostgreSQL-server med hjälp av kommandoradsgränssnittet (Azure CLI). Dock stöds åtkomst till transaktionsloggar inte. 

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- [Azure Database for PostgreSQL-server](quickstart-create-server-database-azure-cli.md)
- Den [Azure CLI](/cli/azure/install-azure-cli) -kommandoradsverktyget eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Konfigurera loggning för Azure Database för PostgreSQL
Du kan konfigurera servern för att komma åt frågeloggar och felloggar. Felloggarna kan ha information om automatisk vakuum, anslutning och kontrollpunkt.
1. Aktivera loggning.
2. Om du vill aktivera loggning av frågor, uppdatera **log\_instruktionen** och **log\_min\_varaktighet\_instruktionen**.
3. Uppdatera kvarhållningsperioden.

Mer information finns i [anpassa konfigurationsparametrar för server](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Lista loggar för Azure Database for PostgreSQL-server
Om du vill visa tillgängliga loggfilerna för din server kör den [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) kommando.

Du kan visa loggfilerna för server **mydemoserver.postgres.database.azure.com** under resursgrupp **myresourcegroup**. Dirigera listan över loggfiler till en textfil med namnet **log\_filer\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Ladda ned loggar lokalt från servern
Med den [az postgres server-logs hämta](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) kommandot, du kan hämta individuella loggfiler för din server. 

Använd följande exempel för att hämta specifik loggfil för servern **mydemoserver.postgres.database.azure.com** under resursgrupp **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Nästa steg
- Läs mer om serverloggar i [serverloggar i Azure Database for PostgreSQL](concepts-server-logs.md).
- Mer information om serverparametrar finns i [anpassa konfigurationsparametrar för server med Azure CLI](howto-configure-server-parameters-using-cli.md).
