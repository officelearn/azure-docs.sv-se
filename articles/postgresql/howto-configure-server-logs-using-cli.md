---
title: Hantera loggar - Azure CLI - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du konfigurerar och kommer åt serverloggarna (.loggfiler) i Azure Database for PostgreSQL - Single Server med hjälp av Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763580"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurera och komma åt serverloggar med hjälp av Azure CLI
Du kan hämta PostgreSQL-serverfelloggarna med hjälp av kommandoradsgränssnittet (Azure CLI). Åtkomst till transaktionsloggar stöds dock inte. 

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- [Azure-databas för PostgreSQL-server](quickstart-create-server-database-azure-cli.md)
- Kommandoradsverktyget [i Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging"></a>Konfigurera loggning
Du kan konfigurera servern så att den kommer åt frågeloggar och felloggar. Felloggar kan ha automatisk vakuum-, anslutnings- och kontrollpunktsinformation.
1. Aktivera loggning.
2. Om du vill aktivera frågeloggning uppdaterar **du\_loggsatsen** och **\_loggminivaraktighetssatsen\_\_**.
3. Uppdatera kvarhållningsperioden.

Mer information finns i [Anpassa serverkonfigurationsparametrar](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Lista loggar
Om du vill visa de tillgängliga loggfilerna för servern kör du kommandot [az postgres server-logs list.](/cli/azure/postgres/server-logs)

Du kan lista loggfilerna för server **mydemoserver.postgres.database.azure.com** under resursgruppen **myresourcegroup**. Sedan rikta listan över loggfiler till en textfil som kallas **loggfiler\_\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Hämta loggar lokalt från servern
Med [az postgres server-loggar nedladdning](/cli/azure/postgres/server-logs) kommando, kan du ladda ner enskilda loggfiler för din server. 

Använd följande exempel för att hämta den specifika loggfilen för servern **mydemoserver.postgres.database.azure.com** under resursgruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Nästa steg
- Mer information om serverloggar finns [i Serverloggar i Azure Database för PostgreSQL](concepts-server-logs.md).
- Mer information om serverparametrar finns i [Anpassa serverkonfigurationsparametrar med Azure CLI](howto-configure-server-parameters-using-cli.md).
