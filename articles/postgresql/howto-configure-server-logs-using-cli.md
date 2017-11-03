---
title: "Konfigurera och komma åt serverloggen för PostgreSQL med Azure CLI | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar och komma åt de server-loggarna i Azure-databas för PostgreSQL med hjälp av Azure CLI-kommandoraden."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.openlocfilehash: 26f8e12c493904f722cad5191ee053feff20f7fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Konfigurera och komma åt server-loggar med hjälp av Azure CLI
Du kan hämta felloggar för PostgreSQL-servern med hjälp av kommandoradsgränssnittet (Azure CLI). Åtkomst till transaktionsloggar stöds dock inte. 

## <a name="prerequisites"></a>Krav
Du behöver följande för att gå igenom den här instruktioner:
- En [Azure-databas för PostgreSQL-server](quickstart-create-server-database-azure-cli.md)
- Installera [Azure CLI 2.0](/cli/azure/install-azure-cli) -kommandoradsverktyget eller Använd Azure Cloud-gränssnittet i webbläsaren.

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Konfigurera loggning för Azure-databas för PostgreSQL
Du kan konfigurera servern för att få åtkomst till frågeloggar och i felloggarna. Felloggarna kan innehålla information om automatisk vakuum, anslutning och kontrollpunkter.
1. Aktivera loggning
2. Update-loggen\_-instruktionen och logga\_min\_varaktighet\_instruktionen för att aktivera loggning av frågor
3. Uppdatera kvarhållningsperioden

Mer information finns i [anpassa server konfigurationsparametrar](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Lista loggar för Azure-databas för PostgreSQL-server
Om du vill visa tillgängliga loggfilerna för din server kör den [az postgres serverloggen listan](/cli/azure/postgres/server-logs#list) kommando.

Du kan visa loggfilerna för server **mypgserver 20170401.postgres.database.azure.com** under resursgrupp **myresourcegroup**, och direkt till en textfil med namnet **loggen\_filer\_list.txt.**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mypgserver-20170401 > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Hämta loggarna lokalt från servern
Den [az postgres serverloggen hämta](/cli/azure/postgres/server-logs#download) kommandot kan du hämta individuella loggfiler för servern. 

Det här exemplet hämtas specifik loggfil för servern **mypgserver 20170401.postgres.database.azure.com** under resursgrupp **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az postgres server-logs download --name 20170414-mypgserver-20170401-postgresql.log --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="next-steps"></a>Nästa steg
- Mer information om server-loggar finns [Server-loggar i Azure-databas för PostgreSQL](concepts-server-logs.md)
- Mer information om serverparametrar finns [anpassa konfigurationsparametrar för servern med hjälp av Azure CLI](howto-configure-server-parameters-using-cli.md)
