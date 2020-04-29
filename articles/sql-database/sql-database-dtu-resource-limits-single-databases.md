---
title: DTU-resurs begränsar enskilda databaser
description: På den här sidan beskrivs några vanliga värden för DTU-resurser för enskilda databaser i Azure SQL Database.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79256320"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Resursbegränsningar för enskilda databaser med hjälp av inköpsmodellen DTU

Den här artikeln innehåller detaljerade resurs gränser för Azure SQL Database enskilda databaser med hjälp av inköps modellen DTU.

För resurs gränser för DTU-inköp för elastiska pooler, se [resurs gränser för DTU-elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md). För vCore resurs gränser, se [vCore-resurs gränser – enkla databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore resurs gränser – elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md). Mer information om olika inköps modeller finns i [inköps modeller och tjänst nivåer](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Enkel databas: lagrings storlekar och beräknings storlekar

Följande tabeller visar de resurser som är tillgängliga för en enskild databas på varje tjänst nivå och beräknings storlek. Du kan ange tjänst nivå, beräknings storlek och lagrings belopp för en enskild databas med hjälp av [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)eller [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Information om skalning och överväganden finns i [skala en enskild databas](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Basic tjänstnivå

| **Beräknings storlek** | **Basic** |
| :--- | --: |
| Maximala DTU:er | 5 |
| Inkluderat lagrings utrymme (GB) | 2 |
| Maximalt lagrings alternativ (GB) | 2 |
| Maximal InMemory-OLTP-lagring (GB) |Ej tillämpligt |
| Maximalt antal samtidiga arbetare (begär Anden) | 30 |
| Maximalt antal samtidiga sessioner | 300 |
|||

> [!IMPORTANT]
> Den grundläggande tjänst nivån ger mindre än en vCore (CPU).  För CPU-intensiva arbets belastningar rekommenderas tjänst nivån S3 eller högre. 
>
>För data lagring placeras den grundläggande tjänst nivån på standard-sid-blobar. Med standard Page blobbar används hård diskbaserade lagrings medier som är bäst lämpade för utveckling, testning och andra arbets belastningar som inte används ofta och som är mindre känsliga för prestanda variationer.
>

### <a name="standard-service-tier"></a>Standardtjänstnivå

| **Beräknings storlek** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximala DTU:er | 10 | 20 | 50 | 100 |
| Inkluderat lagrings utrymme (GB) | 250 | 250 | 250 | 250 |
| Maximalt lagrings alternativ (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |
| Maximalt antal samtidiga arbetare (begär Anden)| 60 | 90 | 120 | 200 |
| Maximalt antal samtidiga sessioner |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> Standard S0, S1-och S2-nivåerna ger mindre än en vCore (CPU).  För CPU-intensiva arbets belastningar rekommenderas tjänst nivån S3 eller högre. 
>
>För data lagring, placeras Standard-S0 och S1-tjänst nivåerna på standard sid-blobar. Med standard Page blobbar används hård diskbaserade lagrings medier som är bäst lämpade för utveckling, testning och andra arbets belastningar som inte används ofta och som är mindre känsliga för prestanda variationer.
>

### <a name="standard-service-tier-continued"></a>Standard tjänst nivå (forts.)

| **Beräknings storlek** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximala DTU:er | 200 | 400 | 800 | 1600 | 3000 |
| Inkluderat lagrings utrymme (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximalt lagrings alternativ (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |Ej tillämpligt |
| Maximalt antal samtidiga arbetare (begär Anden)| 400 | 800 | 1600 | 3200 |6000 |
| Maximalt antal samtidiga sessioner |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium tjänstnivån

| **Beräknings storlek** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximala DTU:er | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Inkluderat lagrings utrymme (GB) | 500 | 500 | 500 | 500 | 4096 * | 4096 * |
| Maximalt lagrings alternativ (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 * | 4096 * |
| Maximal InMemory-OLTP-lagring (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximalt antal samtidiga arbetare (begär Anden)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximalt antal samtidiga sessioner | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\*Från 1024 GB upp till 4096 GB i steg om 256 GB

> [!IMPORTANT]
> Mer än 1 TB lagrings utrymme på Premium-nivån är för närvarande tillgängligt i alla regioner utom: Kina, östra, Kina, norra, Tyskland, centrala, Tyskland nordöstra, västra centrala USA, US DoD regioner och USA, centrala. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> För `tempdb` gränser, se [tempdb-gränser](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Nästa steg

- För vCore resurs gränser för en enskild databas, se [resurs gränser för enskilda databaser med vCore inköps modell](sql-database-vcore-resource-limits-single-databases.md)
- VCore resurs gränser för elastiska pooler finns i [resurs gränser för elastiska pooler med hjälp av vCore inköps modell](sql-database-vcore-resource-limits-elastic-pools.md)
- För resurs gränser för DTU för elastiska pooler, se [resurs gränser för elastiska pooler med inköps modellen DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Resurs begränsningar för hanterade instanser finns i [resurs gränser för hanterade](sql-database-managed-instance-resource-limits.md)instanser.
- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om resurs gränser på en databas server finns i [Översikt över resurs gränser på en SQL Database-Server](sql-database-resource-limits-database-server.md) för information om begränsningar på Server-och prenumerations nivåer.
