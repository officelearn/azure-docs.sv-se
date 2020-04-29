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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061720"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-exempel för Azure SQL Database

Azure SQL Database kan konfigureras med <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Enkel databas & elastiska pooler](#tab/single-database)

| | |
|---|---|
|**Skapa en databas och en elastisk pool**||
| [Skapa en databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-cli.md) | Skapar en Azure SQL-databas och konfigurerar en brand Väggs regel på server nivå. |
| [Skapa elastiska pooler och flytta pooldatabaser](scripts/sql-database-move-database-between-pools-cli.md) | Skapar elastiska SQL-pooler, flyttar Azure SQL-databaser i pooler och ändrar beräknings storlekarna. |
|**Skala en databas och en elastisk pool**||
| [Skala en databas](scripts/sql-database-monitor-and-scale-database-cli.md) | Skalar en Azure SQL-databas till en annan beräknings storlek när du har frågat efter storleks informationen för databasen. |
| [Skala en elastisk pool](scripts/sql-database-scale-pool-cli.md) | Skalar en elastisk SQL-pool till en annan beräknings storlek. |
|**Konfigurera geo-replikering och redundans**||
| [Lägg till en enkel databas i gruppen redundans](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Skapar en databas och en failover-grupp, lägger till databasen i gruppen redundans och testar sedan redundansväxlingen till den sekundära servern. |
| [Konfigurera en failover-grupp för en elastisk pool](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Skapar en databas, lägger till den i en elastisk pool, lägger till den elastiska poolen i gruppen redundans och testar sedan redundansväxlingen till den sekundära servern. |
| [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Konfigurerar aktiv geo-replikering för en Azure SQL-databas och växlar den till den sekundära repliken. |
| [Konfigurera och redundansväxla en pooldatabas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Konfigurerar aktiv geo-replikering för en Azure SQL-databas i en elastisk SQL-pool och växlar sedan till den sekundära repliken. |
| **Granskning och hotidentifiering** |
| [Konfigurera granskning och identifiering av hot](scripts/sql-database-auditing-and-threat-detection-cli.md)| Konfigurerar principer för granskning och hot identifiering för en Azure SQL-databas. |
| **Säkerhetskopiera, återställa, kopiera och importera en databas**||
| [Säkerhetskopiera en databas](scripts/sql-database-backup-database-cli.md)| Säkerhetskopierar en Azure SQL-databas till en Azure Storage-säkerhetskopiering. |
| [Återställ en databas](scripts/sql-database-restore-database-cli.md)| Återställer en Azure SQL-databas från en Geo-redundant säkerhets kopia och återställer en borttagen Azure SQL-databas till den senaste säkerhets kopian. |
| [Kopiera en databas till en ny server](scripts/sql-database-copy-database-to-new-server-cli.md) | Skapar en kopia av en befintlig Azure SQL-databas i en ny Azure SQL-Server. |
| [Importera en databas från en BACPAC-fil](scripts/sql-database-import-from-bacpac-cli.md)| Importerar en databas till en Azure SQL-Server från en *. bacpac* -fil. |
|||

Läs mer om [Azure CLI-API: et för enkel databas](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Hanterad instans](#tab/managed-instance)

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure SQL Database – Managed Instance.

| | |
|---|---|
| **Skapa en hanterad instans**||
| [Skapa en hanterad instans](scripts/sql-database-create-configure-managed-instance-cli.md)| Skapar en hanterad instans. |
| **Konfigurera transparent datakryptering (TDE)**||
| [Hantera transparent datakryptering i en hanterad instans med Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Konfigurerar transparent datakryptering (TDE) i Azure SQL-hanterad instans med hjälp av Azure Key Vault med olika nyckel scenarier. |
|**Konfigurera en failover-grupp**||
| [Konfigurera en failover-grupp för en hanterad instans](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Skapar två hanterade instanser, lägger till dem i en failover-grupp och testar sedan redundansväxlingen från den primära hanterade instansen till den sekundära hanterade instansen. |
|||

Fler exempel på hanterade instanser finns i [skapa](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [Uppdatera](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [flytta en databas](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)och [arbeta med](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) skript.

Läs mer om [Azure CLI API för hanterade instanser](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---
