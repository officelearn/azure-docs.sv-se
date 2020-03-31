---
title: DTU-resurs begränsar enskilda databaser
description: På den här sidan beskrivs några vanliga DTU-resursgränser för enskilda databaser i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: a4c435b4874301fe6fb804085c5b265954cd4f5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256320"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Resursbegränsningar för enskilda databaser med hjälp av inköpsmodellen DTU

Den här artikeln innehåller detaljerade resursgränser för azure SQL Database-enskilda databaser med hjälp av DTU-inköpsmodellen.

För DTU-inköpsmodellresursgränser för elastiska pooler finns i [DTU-resursgränser - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md). För vCore-resursgränser, se [vCore-resursgränser - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-resursgränser - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md). Mer information om de olika inköpsmodellerna finns i [Inköpsmodeller och servicenivåer](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Enkel databas: Lagringsstorlekar och beräkningsstorlekar

Följande tabeller visar de resurser som är tillgängliga för en enskild databas på varje tjänstnivå och beräkningsstorlek. Du kan ange tjänstnivå, beräkningsstorlek och lagringsbelopp för en enskild databas med [Azure-portalen,](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server) [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)eller [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> För skalning av vägledning och överväganden finns i [Skala en enskild databas](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Basic tjänstnivå

| **Beräkna storlek** | **Basic** |
| :--- | --: |
| Maximala DTU:er | 5 |
| Inkluderad lagring (GB) | 2 |
| Max lagringsalternativ (GB) | 2 |
| Maximal InMemory-OLTP-lagring (GB) |Ej tillämpligt |
| Max samtidiga arbetstagare (begäranden) | 30 |
| Maximalt antal samtidiga sessioner | 300 |
|||

> [!IMPORTANT]
> Basic-tjänstnivån ger mindre än en processor (CPU).  För PROCESSOR-intensiva arbetsbelastningar rekommenderas en tjänstnivå på S3 eller mer. 
>
>När det gäller datalagring placeras basic-tjänstnivån på standardsidablobar. Standard page blobbar använder hårddisk (HDD)-baserade lagringsmedia och är bäst lämpade för utveckling, testning och andra sällan använda arbetsbelastningar som är mindre känsliga för prestandavariationer.
>

### <a name="standard-service-tier"></a>Standardtjänstnivå

| **Beräkna storlek** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximala DTU:er | 10 | 20 | 50 | 100 |
| Inkluderad lagring (GB) | 250 | 250 | 250 | 250 |
| Max lagringsalternativ (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |
| Max samtidiga arbetstagare (begäranden)| 60 | 90 | 120 | 200 |
| Maximalt antal samtidiga sessioner |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> Standard-nivåerna S0, S1 och S2 ger mindre än en processor (CPU).  För PROCESSOR-intensiva arbetsbelastningar rekommenderas en tjänstnivå på S3 eller mer. 
>
>När det gäller datalagring placeras standard-S0- och S1-tjänstnivåerna på standardsidablobar. Standard page blobbar använder hårddisk (HDD)-baserade lagringsmedia och är bäst lämpade för utveckling, testning och andra sällan använda arbetsbelastningar som är mindre känsliga för prestandavariationer.
>

### <a name="standard-service-tier-continued"></a>Standardtjänstnivå (fortsättning)

| **Beräkna storlek** | **S4** | **S6** | **S7 (S7)** | **S9 (S9)** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximala DTU:er | 200 | 400 | 800 | 1600 | 3000 |
| Inkluderad lagring (GB) | 250 | 250 | 250 | 250 | 250 |
| Max lagringsalternativ (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |Ej tillämpligt |
| Max samtidiga arbetstagare (begäranden)| 400 | 800 | 1600 | 3200 |6000 |
| Maximalt antal samtidiga sessioner |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium tjänstnivån

| **Beräkna storlek** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximala DTU:er | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Inkluderad lagring (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Max lagringsalternativ (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Maximal InMemory-OLTP-lagring (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Max samtidiga arbetstagare (begäranden)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximalt antal samtidiga sessioner | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\*Från 1024 GB upp till 4096 GB i steg om 256 GB

> [!IMPORTANT]
> Mer än 1 TB lagringsutrymme i Premium-nivån är för närvarande tillgängligt i alla regioner utom: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD-regioner och US Government Central. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> För `tempdb` begränsningar, se [tempdb gränser](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Nästa steg

- För vCore-resursgränser för en enskild databas finns i [resursgränser för enskilda databaser med hjälp av vCore-inköpsmodellen](sql-database-vcore-resource-limits-single-databases.md)
- För vCore-resursgränser för elastiska pooler finns i [resursgränser för elastiska pooler med hjälp av vCore-inköpsmodellen](sql-database-vcore-resource-limits-elastic-pools.md)
- För DTU-resursgränser för elastiska pooler finns i [resursgränser för elastiska pooler med DTU-inköpsmodellen](sql-database-dtu-resource-limits-elastic-pools.md)
- För resursgränser för hanterade instanser finns i [resursbegränsningar för hanterade instanser](sql-database-managed-instance-resource-limits.md).
- Information om allmänna Azure-gränser finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om resursbegränsningar på en databasserver finns i [översikt över resursbegränsningar på en SQL Database-server](sql-database-resource-limits-database-server.md) för information om begränsningar på server- och prenumerationsnivå.
