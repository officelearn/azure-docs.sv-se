---
title: Exempel på Azure CLI-skript
description: Azure CLI-exempelskript för att skapa och hantera Azure SQL Database-servrar, elastiska pooler, databaser och brandväggar.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061720"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-exempel för Azure SQL Database

Azure SQL Database kan konfigureras med <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[En enda databas & elastiska pooler](#tab/single-database)

| | |
|---|---|
|**Skapa en databas och en elastisk pool**||
| [Skapa en databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-cli.md) | Skapar en Azure SQL-databas och konfigurerar en brandväggsregel på servernivå. |
| [Skapa elastiska pooler och flytta pooldatabaser](scripts/sql-database-move-database-between-pools-cli.md) | Skapar SQL-elastiska pooler, flyttar poolade Azure SQL-databaser och ändrar beräkningsstorlekar. |
|**Skala en databas och en elastisk pool**||
| [Skala en databas](scripts/sql-database-monitor-and-scale-database-cli.md) | Skalar en Azure SQL-databas till en annan beräkningsstorlek efter att ha frågat storleksinformationen för databasen. |
| [Skala en elastisk pool](scripts/sql-database-scale-pool-cli.md) | Skalar en ELASTISK SQL-pool till en annan beräkningsstorlek. |
|**Konfigurera geo-replikering och redundans**||
| [Lägga till en databas i redundansgruppen](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Skapar en databas och en redundansgrupp, lägger till databasen i redundansgruppen och testar sedan redundans till den sekundära servern. |
| [Konfigurera en redundanskitetsgrupp för en elastisk pool](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Skapar en databas, lägger till den i en elastisk pool, lägger till den elastiska poolen i redundansgruppen och testar sedan redundans till den sekundära servern. |
| [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Konfigurerar aktiv geo-replikering för en Azure SQL-databas och växlar över den till den sekundära repliken. |
| [Konfigurera och redundansväxla en pooldatabas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Konfigurerar aktiv geo-replikering för en Azure SQL-databas i en ELASTISK SQL-pool och växlar sedan över den till den sekundära repliken. |
| **Granskning och hotidentifiering** |
| [Konfigurera granskning och identifiering av hot](scripts/sql-database-auditing-and-threat-detection-cli.md)| Konfigurerar gransknings- och hotidentifieringsprinciper för en Azure SQL-databas. |
| **Säkerhetskopiera, återställa, kopiera och importera en databas**||
| [Säkerhetskopiera en databas](scripts/sql-database-backup-database-cli.md)| Säkerhetskopierar en Azure SQL-databas till en Azure-lagringssäkerhetskopiering. |
| [Återställ en databas](scripts/sql-database-restore-database-cli.md)| Återställer en Azure SQL-databas från en geouppsagbar säkerhetskopia och återställer en borttagen Azure SQL-databas till den senaste säkerhetskopian. |
| [Kopiera en databas till en ny server](scripts/sql-database-copy-database-to-new-server-cli.md) | Skapar en kopia av en befintlig Azure SQL-databas i en ny Azure SQL-server. |
| [Importera en databas från en bacpac-fil](scripts/sql-database-import-from-bacpac-cli.md)| Importerar en databas till en Azure SQL-server från en *.bacpac-fil.* |
|||

Läs mer om Azure CLI API för [den enskilda databasen](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Hanterad instans](#tab/managed-instance)

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure SQL Database – Managed Instance.

| | |
|---|---|
| **Skapa en hanterad instans**||
| [Skapa en hanterad instans](scripts/sql-database-create-configure-managed-instance-cli.md)| Skapar en hanterad instans. |
| **Konfigurera transparent datakryptering (TDE)**||
| [Hantera transparent datakryptering i en hanterad instans med Hjälp av Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Konfigurerar Transparent datakryptering (TDE) i Azure SQL Managed Instance med Azure Key Vault med olika viktiga scenarier. |
|**Konfigurera en redundansgrupp**||
| [Konfigurera en redundansgrupp för en hanterad instans](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Skapar två hanterade instanser, lägger till dem i en redundansgrupp och testar sedan redundans från den primära hanterade instansen till den sekundära hanterade instansen. |
|||

Ytterligare exempel på hanterad instans finns i [skapa](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [uppdatera](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), flytta [en databas](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), arbeta [med](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) skript.

Läs mer om den [hanterade instansen Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---
