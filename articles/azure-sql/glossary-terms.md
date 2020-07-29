---
title: Ordlista
titleSuffix: Azure SQL
description: En ord lista med termer för att arbeta med Azure SQL Database, Azure SQL-hanterad instans och SQL på virtuella Azure-datorer.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: f05871c41e2deec3f6a52446844c0b8fd2c9038d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221393"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database ord lista med villkor
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|Kontext|Term|Mer information|
|:---|:---|:---|
|Azure-tjänst|Azure SQL Database eller SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|Inköps modell|DTU-baserad inköps modell|[DTU-baserad inköps modell](database/service-tiers-dtu.md)|
||Köpmodell baserad på virtuell kärna|[Köpmodell baserad på virtuell kärna](database/service-tiers-vcore.md)|
|Distributions alternativ |Enskild databas|[Enkla databaser](database/single-database-overview.md)|
||Elastisk pool|[Elastisk pool](database/elastic-pool-overview.md)|
|Tjänstenivå|Basic, standard, Premium, Generell användning, storskalig Affärskritisk|För tjänst nivåer i vCore-modellen, se [SQL Database tjänst nivåer](database/service-tiers-vcore.md#service-tiers). För tjänst nivåer i DTU-modellen, se [DTU-modellen](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Beräknings nivå|Serverlös databearbetning|[Serverlös databearbetning](database/service-tiers-vcore.md#compute-tiers)
||Allokerad beräkning|[Allokerad beräkning](database/service-tiers-vcore.md#compute-tiers)
|Beräknings generation|Gen5, M-serien, Fsv2-serien|[Maskin varu generationer](database/service-tiers-vcore.md#hardware-generations)
|Server entitet| Server |[Logiska SQL-servrar](database/logical-servers.md)|
|Resurstyp|V-kärna|En processor kärna som tillhandahålls för beräknings resursen för en enskild databas, elastisk pool. |
||Beräknings storlek och lagrings belopp|Compute-storlek är den maximala mängden processor, minne och andra icke-lagrings resurser som är tillgängliga för en enskild databas eller elastisk pool.  Lagrings storleken är den maximala mängden lagring som är tillgänglig för en enskild databas eller elastisk pool. För storleks alternativ i vCore-modellen, se [vCore-enkla databaser](database/resource-limits-vcore-single-databases.md)och [vCore elastiska pooler](database/resource-limits-vcore-elastic-pools.md).  (.. /managed-instance/resource-limits.md).  Storleks alternativ i DTU-modellen finns i både [DTU-databaser](database/resource-limits-dtu-single-databases.md) och [elastiska DTU-pooler](database/resource-limits-dtu-elastic-pools.md).

## <a name="azure-sql-managed-instance"></a>Hanterad Azure SQL-instans

|Kontext|Term|Mer information|
|:---|:---|:---|
|Azure-tjänst|Hanterad Azure SQL-instans|[SQL-hanterad instans](managed-instance/sql-managed-instance-paas-overview.md)|
|Inköps modell|Köpmodell baserad på virtuell kärna|[Köpmodell baserad på virtuell kärna](database/service-tiers-vcore.md)|
|Distributions alternativ |Enskild instans|[Enskild instans](managed-instance/sql-managed-instance-paas-overview.md)|
||Instans-pool (för hands version)|[Instanspooler](managed-instance/instance-pools-overview.md)|
|Tjänstenivå|Generell användning Affärskritisk|[SQL-hanterad instans tjänst nivåer](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|Beräknings nivå|Allokerad beräkning|[Allokerad beräkning](database/service-tiers-vcore.md#compute-tiers)|
|Beräknings generation|Gen5|[Maskin varu generationer](database/service-tiers-vcore.md#hardware-generations)
|Server entitet|Hanterad instans eller instans| Ej tillämpligt eftersom SQL-hanterad instans är i sig själva servern |
|Resurstyp|V-kärna|En processor kärna som tillhandahålls för beräknings resursen för SQL-hanterad instans.|
||Beräknings storlek och lagrings belopp|Compute-storlek är den maximala mängden processor, minne och andra icke-lagrings resurser för SQL-hanterad instans.  Lagrings storleken är den maximala lagrings mängden som är tillgänglig för en SQL-hanterad instans.  För storleks alternativ, [SQL-hanterade instanser](managed-instance/resource-limits.md). |

## <a name="sql-on-azure-vm"></a>SQL på virtuell Azure-dator

behöver du mer saker här
