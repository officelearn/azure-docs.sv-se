---
title: Exempel på Azure CLI-skript
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Exempel på Azure CLI-skript för att skapa och hantera Azure SQL Database och Azure SQL-hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: f5a8e5c9af9f3dc27b517c386c4abae24dc45239
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053419"
---
# <a name="azure-cli-samples-for-azure-sql-database--sql-managed-instance"></a>Azure CLI-exempel för Azure SQL Database & SQL-hanterad instans 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database-och SQL-hanterad instans kan konfigureras med hjälp av <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Följande tabell innehåller länkar till exempel på Azure CLI-skript för att hantera enskilda databaser och databaser i Azure SQL Database. 

| | |
|---|---|
|**Skapa en Azure SQL Database**||
| [Skapa en databas och konfigurera en brandväggsregel](scripts/create-and-configure-database-cli.md) | Skapar en SQL Database och konfigurerar en brand Väggs regel på server nivå. |
| [Skapa elastiska pooler och flytta pooldatabaser](scripts/move-database-between-elastic-pools-cli.md) | Skapar elastiska pooler, flyttar SQL-databaser i pooler och ändrar beräknings storlekarna. |
|**Skala Azure SQL Database**||
| [Skala en databas](scripts/monitor-and-scale-database-cli.md) | Skalar en SQL Database till en annan beräknings storlek efter att ha frågat efter storleks informationen för databasen. |
| [Skala en elastisk pool](scripts/scale-pool-cli.md) | Skalar en elastisk SQL-pool till en annan beräknings storlek. |
|**Konfigurera geo-replikering och redundans**||
| [Lägg till en enkel databas i gruppen redundans](scripts/add-database-to-failover-group-cli.md)| Skapar en databas och en failover-grupp, lägger till databasen i gruppen redundans och testar sedan redundansväxlingen till den sekundära servern. |
| [Konfigurera en failover-grupp för en elastisk pool](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Skapar en databas, lägger till den i en elastisk pool, lägger till den elastiska poolen i gruppen redundans och testar sedan redundansväxlingen till den sekundära servern. |
| [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Konfigurerar aktiv geo-replikering för en Azure SQL-databas och växlar den till den sekundära repliken. |
| [Konfigurera och redundansväxla en pooldatabas med aktiv geo-replikering](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Konfigurerar aktiv geo-replikering för en Azure SQL-databas i en elastisk SQL-pool och växlar sedan till den sekundära repliken. |
| **Granskning och hotidentifiering** |
| [Konfigurera granskning och identifiering av hot](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Konfigurerar principer för granskning och hot identifiering för en Azure SQL-databas. |
| **Säkerhetskopiera, återställa, kopiera och importera en databas**||
| [Säkerhetskopiera en databas](../../sql-database/scripts/sql-database-backup-database-cli.md)| Säkerhetskopierar en SQL-databas till en Azure Storage-säkerhetskopiering. |
| [Återställ en databas](../../sql-database/scripts/sql-database-restore-database-cli.md)| Återställer en SQL-databas från en Geo-redundant säkerhets kopia och återställer en borttagen SQL-databas till den senaste säkerhets kopian. |
| [Kopiera en databas till en ny server](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Skapar en kopia av en befintlig SQL-databas på en ny server. |
| [Importera en databas från en BACPAC-fil](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importerar en databas till SQL Database från en *. bacpac* -fil. |
|||

Läs mer om [Azure CLI-API: et för enkel databas](single-database-manage.md#azure-cli).

# <a name="azure-sql-managed-instance"></a>[Hanterad Azure SQL-instans](#tab/managed-instance)

Följande tabell innehåller länkar till exempel på Azure CLI-skript för Azure SQL-hanterad instans.

| | |
|---|---|
| **Skapa en SQL-hanterad instans**||
| [Skapa en SQL-hanterad instans](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Skapar en SQL-hanterad instans. |
| **Konfigurera transparent datakryptering (TDE)**||
| [Hantera transparent datakryptering i en SQL-hanterad instans med hjälp av Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Konfigurerar transparent datakryptering (TDE) i SQL-hanterad instans med hjälp av Azure Key Vault med olika nyckel scenarier. |
|**Konfigurera en failover-grupp**||
| [Konfigurera en failover-grupp för SQL-hanterad instans](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Skapar två SQL-hanterade instanser, lägger till dem i en grupp för växling vid fel och testar sedan redundansväxlingen från den primära SQL-hanterade instansen till den sekundära SQL-hanterade |
|||

Fler exempel på SQL-hanterade instanser finns i avsnittet [skapa](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [Uppdatera](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [flytta en databas](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)och [arbeta med](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) skript.

Läs mer om [Azure CLI API för SQL-hanterad instans](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-manage-managed-instances).

---
