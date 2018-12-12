---
title: Affärsnivå kritiska – Azure SQL Database-tjänsten | Microsoft Docs
description: Lär dig mer om nivån affärskritisk för Azure SQL-databas
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: aecbc9415fc7fe93b5df355c97a1a51a74f01e97
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883538"
---
# <a name="business-critical-tier---azure-sql-database"></a>Affärsnivå kritiska – Azure SQL Database

> [!NOTE]
> Kritiska affärsnivå kallas Premium i DTU-inköpsmodellen. En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen i [köpa modeller och resurser för Azure SQL Database](sql-database-service-tiers.md).

Azure SQL Database är baserad på SQL Server Database Engine-arkitektur som justeras för molnmiljön för att säkerställa 99,99% tillgänglighet även i fall av infrastrukturfel. Det finns tre arkitekturmodeller som används i Azure SQL Database:
- Allmänt syfte/Standard 
- Kritisk Business/Premium
- Hyperskala

Premium/affärskritisk nivåmodell baseras på ett kluster med databasen sökmotor-processer. Den här arkitekturen modellen är beroende av ett faktum att det är alltid ett nodkvorum tillgängliga database engine och har minimal prestandapåverkan på din arbetsbelastning även under underhållsaktiviteter.

Azure uppgraderar och korrigerar underliggande operativsystemet, drivrutiner och SQL Server Database Engine transparent med minimalt driftstopp för slutanvändare. 

Premium-tillgänglighet är aktiverat på Premium och affärskritisk tjänstnivåer i Azure SQL Database och den är utformad för intensiva arbetsbelastningar som inte tolererar en prestandaförsämring på grund av pågående underhållsåtgärder.

I premium-modellen integreras Azure SQL-databas beräkning och lagring på en enda nod. Hög tillgänglighet i den här arkitekturen modellen uppnås genom att replikeringen av beräkning (SQL Server Database Engine-processen) och lagring (lokalt anslutna SSD) som distribuerats i kluster med fyra noder, med hjälp av teknik och SQL Server [alltid på Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Kluster på database engine-noder](media/sql-database-managed-instance/business-critical-service-tier.png)

Både SQL database engine-processen och underliggande mdf/ldf-filerna är placerade på samma nod med lokalt anslutna SSD-lagring som ger låg fördröjning till din arbetsbelastning. Hög tillgänglighet har implementerats med hjälp av teknik och SQL Server [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Varje databas är ett kluster av databasnoder med en primär databas som är tillgänglig för kunds arbetsbelastning och en tre sekundära processer som innehåller kopior av data. Den primära noden skickar ständigt ändringarna till sekundära noder för att säkerställa att data är tillgängliga på sekundära repliker om den primära noden kraschar av någon anledning. Redundansväxling hanteras av SQL Server Database Engine – en sekundär replik blir den primära noden och en ny sekundär replik skapas för att se till att tillräckligt många noder i klustret. Arbetsbelastningen omdirigeras automatiskt till den nya primära noden.

Dessutom affärskritisk kluster har inbyggd [Lässkalning](sql-database-read-scale-out.md) funktioner som ger kostnadsfri av debiterar inbyggda skrivskyddad nod som kan användas för att köra skrivskyddade frågor (till exempel rapporter) som inte påverkar prestanda i din primära arbetsbelastning.

## <a name="when-to-choose-this-service-tier"></a>När du ska välja den här?

Företag kritiska-nivån är utformad för program som kräver låg latens svar från underliggande SSD-lagring (1 – 2 ms i genomsnitt), snabb återställning om inte den underliggande infrastrukturen och behovet av att avlasta rapporter, analyser, och skrivskyddade frågor till den kostnadsfria kostnad läsbar sekundär replik av den primära databasen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [generella](sql-database-service-tier-general-purpose.md) och [hyperskala](sql-database-service-tier-hyperscale.md) nivåer.
- Lär dig mer om [Service Fabric](../service-fabric/service-fabric-overview.md).
- Fler alternativ för hög tillgänglighet och katastrofåterställning, se [affärskontinuitet](sql-database-business-continuity.md).
