---
title: Azure SQL Database DTU-baserad resurs begränsar enskilda databaser | Microsoft Docs
description: Den här sidan beskriver några vanliga DTU-baserade resursbegränsningar för enskilda databaser i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: 7c35c1f3568ab98bdd3c7fba8acd3aa958aa795b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285149"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Resursgränser för enskilda databaser med hjälp av den DTU-baserade inköpsmodellen

Den här artikeln innehåller detaljerade resursgränser för enskilda databaser i Azure SQL Database med hjälp av den DTU-baserade inköpsmodellen.

DTU-baserade inköpschef modellen resursgränser för elastiska pooler, se [DTU-baserade resursbegränsningar - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md). VCore-baserade resursbegränsningar finns [vCore-baserade resursbegränsningar - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md). Läs mer om de olika modellerna för inköp, [köpa modeller och tjänstnivåer](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Databas: Lagringsstorlekar och storlekar

Följande tabeller visar resurserna som är tillgängliga för en enskild databas på varje tjänstnivå och beräkna storleken. Du kan ange tjänstnivå, beräkningsstorleken och lagringsutrymmet för en enkel databas med hjälp av den [Azure-portalen](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [ Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), eller [REST-API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Skala vägledning och överväganden finns i [skala en enskild databas](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Grundläggande tjänstenivå

| **Beräkna storlek** | **Basic** |
| :--- | --: |
| Maximala DTU:er | 5 |
| Lagringsutrymme (GB) | 2 |
| Maximalt antal lagringsalternativ (GB) | 2 |
| Maximal InMemory-OLTP-lagring (GB) |Gäller inte |
| Maximalt antal samtidiga arbetare (begäranden) | 30 |
| Maximalt antal samtidiga sessioner | 300 |
|||

### <a name="standard-service-tier"></a>Standardtjänstenivå

| **Beräkna storlek** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximala DTU:er | 10 | 20 | 50 | 100 |
| Lagringsutrymme (GB) | 250 | 250 | 250 | 250 |
| Maximalt antal lagringsalternativ (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Gäller inte | Saknas | Saknas | Gäller inte |
| Maximalt antal samtidiga arbetare (begäranden)| 60 | 90 | 120 | 200 |
| Maximalt antal samtidiga sessioner |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard-tjänstnivå (forts.)

| **Beräkna storlek** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximala DTU:er | 200 | 400 | 800 | 1600 | 3000 |
| Lagringsutrymme (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximalt antal lagringsalternativ (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Gäller inte | Saknas | Saknas | Saknas |Gäller inte |
| Maximalt antal samtidiga arbetare (begäranden)| 400 | 800 | 1600 | 3200 |6000 |
| Maximalt antal samtidiga sessioner |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium tjänstenivån

| **Beräkna storlek** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximala DTU:er | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Lagringsutrymme (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Maximalt antal lagringsalternativ (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Maximal InMemory-OLTP-lagring (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximalt antal samtidiga arbetare (begäranden)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximalt antal samtidiga sessioner | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* Från 1 024 GB upp till 4096 GB ökningar på 256 GB

> [!IMPORTANT]
> Mer än 1 TB lagringsutrymme på Premium-nivån är för närvarande tillgängligt i alla regioner förutom: Kina, östra; Kina, norra; Tyskland, centrala; Tyskland, nordöstra; USA, västra centrala; US DoD-regioner samt US Government Central. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i aktuella begränsningar för sidan 11-15.  
> [!NOTE]
> För `tempdb` begränsningar, finns i [tempdb gränser](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Nästa steg

- VCore-resursgränser för en enskild databas, se [resursgränser för enskilda databaser med hjälp av den vCore-baserade inköpsmodellen](sql-database-vcore-resource-limits-single-databases.md)
- VCore-resursgränser för elastiska pooler, se [resursgränser för elastiska pooler med hjälp av den vCore-baserade inköpsmodellen](sql-database-vcore-resource-limits-elastic-pools.md)
- DTU-resursgränser för elastiska pooler, se [resursgränser för elastiska pooler med hjälp av den DTU-baserade inköpsmodellen](sql-database-dtu-resource-limits-elastic-pools.md)
- Resursgränser för hanterade instanser, se [hanterad instans resursbegränsningar](sql-database-managed-instance-resource-limits.md).
- Information om allmänna Azure-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Läs om hur resursbegränsningar på en databasserver, [översikt över resursgränser på en SQL-databasserver](sql-database-resource-limits-database-server.md) information om begränsningar på de server och prenumeration.
