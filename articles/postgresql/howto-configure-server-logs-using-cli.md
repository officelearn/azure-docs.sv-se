---
title: Hantera loggar – Azure CLI – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du konfigurerar och kommer åt Server loggar (. log-filer) i Azure Database for PostgreSQL-enskild server med hjälp av Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74763580"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurera och få åtkomst till Server loggar med hjälp av Azure CLI
Du kan hämta fel loggarna för PostgreSQL-servern med hjälp av kommando rads gränssnittet (Azure CLI). Åtkomst till transaktions loggar stöds dock inte. 

## <a name="prerequisites"></a>Krav
För att gå igenom den här instruktions guiden behöver du:
- [Azure Database for PostgreSQL Server](quickstart-create-server-database-azure-cli.md)
- Kommando rads verktyget för [Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging"></a>Konfigurera loggning
Du kan konfigurera servern för att få åtkomst till Query-loggar och fel loggar. Fel loggar kan ha Auto-vakuum, anslutnings-och kontroll punkts information.
1. Aktivera loggning.
2. Om du vill aktivera loggning av frågor uppdaterar du **logg\_satsen** och **\_loggen för min\_varaktighet\_**.
3. Uppdaterings kvarhållningsperiod.

Mer information finns i [Anpassa Server konfigurations parametrar](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Lista loggar
Om du vill visa en lista över tillgängliga loggfiler för servern kör du kommandot [AZ postgres Server-logs List](/cli/azure/postgres/server-logs) .

Du kan visa en lista över loggfilerna för Server **mydemoserver.postgres.Database.Azure.com** under resurs gruppen **myresourcegroup**. Dirigera sedan listan över loggfiler till en textfil med namnet **log\_Files\_List. txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Hämta loggar lokalt från servern
Med kommandot [AZ postgres Server-logs Download](/cli/azure/postgres/server-logs) kan du hämta enskilda loggfiler för servern. 

Använd följande exempel för att hämta den speciella logg filen för servern **mydemoserver.postgres.Database.Azure.com** under resurs gruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Nästa steg
- Mer information om Server loggar finns [i Server loggar i Azure Database for PostgreSQL](concepts-server-logs.md).
- Mer information om Server parametrar finns i [Anpassa Server konfigurations parametrar med Azure CLI](howto-configure-server-parameters-using-cli.md).
