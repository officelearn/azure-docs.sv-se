---
title: Tjänst nivå för allmän användning
description: Lär dig mer om Azure SQL Database generell nivå
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255891"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Tjänst nivå för generell användning – Azure SQL Database

> [!NOTE]
> Tjänst nivån General-Purpose i den vCore-baserade inköps modellen kallas för standard tjänst nivån i den DTU-baserade inköps modellen. En jämförelse av den vCore-baserade inköps modellen med den DTU-baserade inköps modellen finns i [Azure SQL Database köpa modeller och resurser](sql-database-purchase-models.md).

Azure SQL Database baseras på SQL Server databas motor arkitektur som är anpassad för moln miljön för att säkerställa 99,99% tillgänglighet även i händelse av infrastruktur haverier. Det finns tre tjänst nivåer som används i Azure SQL Database, var och en med olika arkitektur modeller. Dessa tjänst nivåer är:

- Generellt syfte
- Verksamhets kritisk
- Hyperskala

Arkitektur modellen för tjänst nivån General-Purpose baseras på en separation av data bearbetning och lagring. Den här arkitektur modellen är beroende av hög tillgänglighet och tillförlitlighet för Azure Blob Storage som transparent replikerar databasfiler och garanterar ingen data förlust om det uppstår ett underliggande infrastruktur haveri.

Följande bild visar fyra noder i standard arkitektur modellen med de avgränsade beräknings-och lagrings lagren.

![Separering av beräkning och lagring](media/sql-database-managed-instance/general-purpose-service-tier.png)

I arkitektur modellen för tjänst nivån General-Purpose finns det två lager:

- Ett tillstånds löst beräknings lager som kör `sqlservr.exe`s processen och bara innehåller tillfälliga och cachelagrade data (till exempel plan cache, resurspool, Column Store pool). Den här tillstånds lösa SQL Server-noden drivs av Azure Service Fabric som initierar process, styr hälsan för noden och utför redundans till en annan plats vid behov.
- Ett tillstånds känsligt data lager med databasfiler (. MDF/. ldf) som lagras i Azure Blob Storage. Azure Blob Storage garanterar att det inte finns någon data förlust för poster som placeras i en databas fil. Azure Storage har inbyggd data tillgänglighet/redundans som säkerställer att alla poster i logg filen eller i data filen kommer att bevaras även om SQL Server processen kraschar.

När databas motorn eller operativ systemet uppgraderas Miss lyckas en del av den underliggande infrastrukturen, eller om ett allvarligt problem upptäcks i SQL Server processen, flyttar Azure Service Fabric den tillstånds lösa SQL Server processen till en annan tillstånds lös beräknings nod. Det finns en uppsättning reserv noder som väntar på att köra en ny beräknings tjänst om en redundansväxling av den primära noden sker för att minimera redundansväxlingen. Data i Azure Storage Layer påverkas inte och data/loggfiler bifogas till nyinitierad SQL Server process. Den här processen garanterar 99,99% tillgänglighet, men kan ha vissa prestanda påverkan på tung arbets belastning som körs på grund av över gångs tiden och det faktum att den nya SQL Server-noden börjar med kall cache.

## <a name="when-to-choose-this-service-tier"></a>När du ska välja den här tjänst nivån

Generell användning Service Tier är en standard tjänst nivå i Azure SQL Database som är utformad för de flesta allmänna arbets belastningar. Om du behöver en fullständigt hanterad databas motor med 99,99% SLA med lagrings fördröjningen mellan 5 och 10 MS som matchar Azure SQL-IaaS i de flesta fall, är Generell användning nivån alternativet för dig.

## <a name="next-steps"></a>Nästa steg

- Hitta resurs egenskaper (antal kärnor, IO, minne) för Generell användning/standard-nivån i [hanterad instans](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), enkel databas i [vCore-modellen](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) eller [DTU-modellen](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)eller elastisk pool i vCore- [modellen](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) och [DTU-modellen](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Lär dig mer om [affärskritisk](sql-database-service-tier-business-critical.md) -och [skalnings](sql-database-service-tier-hyperscale.md) nivåer.
- Läs mer om [Service Fabric](../service-fabric/service-fabric-overview.md).
- Fler alternativ för hög tillgänglighet och haveri beredskap finns i [affärs kontinuitet](sql-database-business-continuity.md).
