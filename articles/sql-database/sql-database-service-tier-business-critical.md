---
title: Affärskritisk nivå – Azure SQL Database-tjänsten | Microsoft Docs
description: Lär dig mer om Affärskritisks nivån för Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 48cde2f96083779bdeb13ba5f39b68c18b395045
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515375"
---
# <a name="business-critical-tier---azure-sql-database"></a>Affärskritisk nivå – Azure SQL Database

> [!NOTE]
> Affärskritisk nivån kallas Premium i inköps modell för DTU. En jämförelse av den vCore-baserade inköps modellen med den DTU-baserade inköps modellen finns i [Azure SQL Database köpa modeller och resurser](sql-database-purchase-models.md).

Azure SQL Database baseras på SQL Server databas motor arkitektur som justeras för moln miljön för att säkerställa 99,99% tillgänglighet även i händelse av infrastruktur haverier. Det finns tre arkitektur modeller som används i Azure SQL Database:
- Generell användning/standard 
- Affärskritisk/Premium
- Storskalig

Premium-/Affärskritisk tjänst nivå modellen är baserad på ett kluster med databas motor processer. Den här arkitektur modellen bygger på ett faktum att det alltid finns ett kvorum med tillgängliga databas motor noder och har minimal påverkan på din arbets belastning även under underhålls aktiviteter.

Azure uppgraderar och korrigeringsfiler underliggande operativ system, driv rutiner och SQL Server Database Engine transparent med minimal drift tid för slutanvändare. 

Premium-tillgänglighet är aktiverat i Premium-och Affärskritisk tjänst nivåerna för Azure SQL Database och är utformad för intensiva arbets belastningar som inte kan tolerera prestanda påverkan på grund av pågående underhålls åtgärder.

I Premium-modellen integrerar Azure SQL Database data bearbetning och lagring på den enskilda noden. Hög tillgänglighet i den här arkitektur modellen uppnås genom att data replikeras (SQL Server databas motor process) och lagring (lokalt ansluten SSD) distribueras i fyra noder i klustret, med teknik som liknar SQL Server [Always on-tillgänglighetsgrupper ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Kluster med noder i databas motorn](media/sql-database-managed-instance/business-critical-service-tier.png)

Både processen för SQL Database-motorn och de underliggande MDF-/ldf-filerna placeras på samma nod med lokalt ansluten SSD-lagring som ger låg latens för din arbets belastning. Hög tillgänglighet implementeras med teknik som liknar SQL Server [Always on-tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Varje databas är ett kluster med databasnoder med en primär databas som är tillgänglig för kund arbets belastning och en tre sekundära processer som innehåller kopior av data. Den primära noden skickar konstant ändringar till sekundära noder för att säkerställa att data är tillgängliga på sekundära repliker om den primära noden kraschar av någon anledning. Redundansväxlingen hanteras av SQL Server Database Engine – en sekundär replik blir den primära noden och en ny sekundär replik skapas för att se till att det finns tillräckligt många noder i klustret. Arbets belastningen omdirigeras automatiskt till den nya primära noden.

Dessutom har Affärskritisk Cluster inbyggd [Läs skalnings](sql-database-read-scale-out.md) funktion som tillhandahåller en inbyggd skrivskyddad skrivskyddad nod som kan användas för att köra skrivskyddade frågor (till exempel rapporter) som inte ska påverka prestandan för din primära arbets belastning.

## <a name="when-to-choose-this-service-tier"></a>När ska du välja den här tjänst nivån?

Affärskritisk tjänst nivå är utformad för program som kräver svar med låg latens från den underliggande SSD-lagringen (1-2 MS i genomsnitt), snabb återställning om den underliggande infrastrukturen Miss lyckas eller om du behöver stänga av och läsa in rapporter, analyser och skriv skydd frågar om den kostnads fria läsbara sekundära repliken för den primära databasen.

## <a name="next-steps"></a>Nästa steg

- Hitta resurs egenskaper (antal kärnor, IO, minne) för Affärskritisk nivå i hanterad [instans](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), enkel databas i [vCore-modell](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute) eller [DTU-modell](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)eller elastisk pool i [vCore-modellen](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) och DTU- [modellen](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Lär dig mer om [generell användning](sql-database-service-tier-general-purpose.md) -och [skalnings](sql-database-service-tier-hyperscale.md) nivåer.
- Läs mer om [Service Fabric](../service-fabric/service-fabric-overview.md).
- Fler alternativ för hög tillgänglighet och haveri beredskap finns i [affärs kontinuitet](sql-database-business-continuity.md).
