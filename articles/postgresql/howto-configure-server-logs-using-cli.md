---
title: Konfigurera och öppna serverloggar för PostgreSQL – enskild Server med hjälp av Azure CLI
description: Den här artikeln beskriver hur du konfigurerar och få åtkomst till serverloggar i Azure Database för PostgreSQL – enskild Server med hjälp av Azure CLI-kommandoraden.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4702db31ffeb15481584b9638f5be1aa640ff39e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067213"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurera och öppna serverloggar med hjälp av Azure CLI
Du kan hämta felloggar för PostgreSQL-server med hjälp av kommandoradsgränssnittet (Azure CLI). Dock stöds åtkomst till transaktionsloggar inte. 

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom den här guiden, måste du:
- [Azure Database for PostgreSQL-server](quickstart-create-server-database-azure-cli.md)
- Den [Azure CLI](/cli/azure/install-azure-cli) -kommandoradsverktyget eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging"></a>Konfigurera loggning
Du kan konfigurera servern för att komma åt frågeloggar och felloggar. Felloggarna kan ha information om automatisk vakuum, anslutning och kontrollpunkt.
1. Aktivera loggning.
2. Om du vill aktivera loggning av frågor, uppdatera **log\_instruktionen** och **log\_min\_varaktighet\_instruktionen**.
3. Uppdatera kvarhållningsperioden.

Mer information finns i [anpassa konfigurationsparametrar för server](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Lista loggar
Om du vill visa tillgängliga loggfilerna för din server kör den [az postgres server-logs list](/cli/azure/postgres/server-logs) kommando.

Du kan visa loggfilerna för server **mydemoserver.postgres.database.azure.com** under resursgrupp **myresourcegroup**. Dirigera listan över loggfiler till en textfil med namnet **log\_filer\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Ladda ned loggar lokalt från servern
Med den [az postgres server-logs hämta](/cli/azure/postgres/server-logs) kommandot, du kan hämta individuella loggfiler för din server. 

Använd följande exempel för att hämta specifik loggfil för servern **mydemoserver.postgres.database.azure.com** under resursgrupp **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Nästa steg
- Läs mer om serverloggar i [serverloggar i Azure Database for PostgreSQL](concepts-server-logs.md).
- Mer information om serverparametrar finns i [anpassa konfigurationsparametrar för server med Azure CLI](howto-configure-server-parameters-using-cli.md).
