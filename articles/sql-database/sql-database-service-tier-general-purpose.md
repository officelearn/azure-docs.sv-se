---
title: Allmänna tjänstnivå – Azure SQL Database | Microsoft Docs
description: Lär dig mer om Azure SQL Database generell nivå
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: b972ea985a09457d8b6a17a292e18754761f5a6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479192"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Generell användning tjänstnivå – Azure SQL Database

> [!NOTE]
> Allmänna tjänstnivån i den vCore-baserade inköpsmodellen kallas standard-tjänstnivå i DTU-baserade inköpsmodellen. En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen i [köpa modeller och resurser för Azure SQL Database](sql-database-purchase-models.md).

Azure SQL Database är baserad på SQL Server database engine-arkitektur som anpassade för molnmiljön för att säkerställa 99,99% tillgänglighet även i fall av infrastrukturfel. Det finns tre tjänstnivåer som används i Azure SQL Database, var och en med olika arkitekturmodeller. Dessa är:

- Generellt syfte
- Affärskritisk
- Hyperskala

Arkitektoniska modell för allmänna tjänstnivån baseras på en åtskillnad mellan beräkning och lagring. Den här arkitekturen modellen är beroende av hög tillgänglighet och tillförlitlighet i Azure Blob storage som transparent replikerar databasfiler och garanterar inga data går förlorade om underliggande infrastruktur fel händer.

Följande bild visar fyra noder i arkitekturen standardmodell med avgränsas lagren för beräkning och lagring.

![Uppdelning av beräkning och lagring](media/sql-database-managed-instance/general-purpose-service-tier.png)

I arkitekturen modell för allmänna tjänstnivån finns det två nivåer:

- En tillståndslös beräkningslager som kör den `sqlserver.exe` bearbeta och innehåller bara tillfälliga och cachelagrade data (till exempel – plancachen, buffertpoolen, kolumnen store pool). Den här tillståndslösa SQL Server-noden drivs av Azure Service Fabric som initierar processen, kontrollerar hälsotillståndet för noden och utför redundans till en annan plats om det behövs.
- En tillståndskänslig datalager med databasfiler (.mdf/.ldf) som lagras i Azure Blob storage. Azure Blob storage garanterar att inga data går förlorade i poster som placeras i en databasfil. Azure Storage har inbyggda tillgänglighet/redundans som säkerställer att varje post i loggfilen eller sida i datafilen kommer att bevaras även om SQL Server-processen kraschar.

När databasmotorn eller operativsystemet uppgraderas, en del av underliggande den infrastrukturen misslyckas eller om vissa viktiga problem har identifierats i SQL Server-processen, tillståndslösa SQL Server-processen Azure Service Fabric flyttas till en annan tillståndslösa compute-nod. Det finns en uppsättning ledig noder som väntar på att köra nya beräkningstjänst om en redundansväxling av den primära noden händer för att minimera failover-tid. Data i Azure storage-skiktet påverkas inte och data/loggfiler är kopplade till nyligen initierad SQL Server-processen. Den här processen garanterar 99,99% tillgänglighet, men det kan ha vissa prestandaeffekter på arbetsbelastning som körs på grund av övergångstid och faktumet den nya SQL Server-noden börjar med kalla cache.

## <a name="when-to-choose-this-service-tier"></a>När du ska välja den här

Nivån för allmän användning service är en standard-tjänstnivå i Azure SQL Database som är utformad för de flesta av de allmänna arbetsbelastningarna. Om du behöver en fullständigt hanterad databasmotor med 99,99% SLA med storage fördröjning mellan 5 och 10 ms som matchar Azure SQL IaaS i de flesta fall är alternativ för dig i nivån generell användning.

## <a name="next-steps"></a>Nästa steg

- Hitta resursen egenskaper (antal kärnor, i/o, minne) för allmänna ändamål/Standard-nivå i [Managed Instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), enkel databas i [vCore-modellen](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes) eller [DTU modell](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes), eller Elastisk pool i [vCore-modellen](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes) och [DTU modellen](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Lär dig mer om [affärskritisk](sql-database-service-tier-business-critical.md) och [hyperskala](sql-database-service-tier-hyperscale.md) nivåer.
- Lär dig mer om [Service Fabric](../service-fabric/service-fabric-overview.md).
- Fler alternativ för hög tillgänglighet och katastrofåterställning, se [affärskontinuitet](sql-database-business-continuity.md).
