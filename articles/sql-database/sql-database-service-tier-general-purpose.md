---
title: Tjänstnivå för allmänt ändamål
description: Lär dig mer om Azure SQL Database-nivån för allmänt ändamål
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255891"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Tjänstnivå för allmänt ändamål - Azure SQL Database

> [!NOTE]
> Den allmänna servicenivån i den vCore-baserade inköpsmodellen kallas standardtjänstnivån i den DTU-baserade inköpsmodellen. En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen finns i [Azure SQL Database som köper modeller och resurser](sql-database-purchase-models.md).

Azure SQL Database baseras på SQL Server databasmotorarkitektur anpassad för molnmiljön för att säkerställa 99,99 % tillgänglighet även vid infrastrukturfel. Det finns tre tjänstnivåer som används i Azure SQL Database, var och en med olika arkitektoniska modeller. Dessa tjänstnivåer är:

- Generellt syfte
- Affärskritiskt
- Hyperskala

Den arkitektoniska modellen för den allmänna servicenivån baseras på en separation av beräkning och lagring. Den här arkitekturmodellen är beroende av hög tillgänglighet och tillförlitlighet för Azure Blob-lagring som på ett transparent sätt replikerar databasfiler och garanterar ingen dataförlust om underliggande infrastrukturfel inträffar.

Följande bild visar fyra noder i standardlagisk modell med de separerade beräknings- och lagringslagren.

![Separation av beräkning och lagring](media/sql-database-managed-instance/general-purpose-service-tier.png)

I den arkitektoniska modellen för den allmänna servicenivån finns det två lager:

- Ett tillståndslöst beräkningslager `sqlservr.exe` som kör processen och som endast innehåller tillfälliga och cachelagrade data (till exempel – plancache, buffertpool, kolumnarkivpool). Den här tillståndslösa SQL Server-noden drivs av Azure Service Fabric som initierar processen, styr hälsotillståndet för noden och utför redundans till en annan plats om det behövs.
- Ett tillståndskänsligt datalager med databasfiler (.mdf/.ldf) som lagras i Azure Blob-lagring. Azure Blob storage garanterar att det inte blir någon dataförlust för någon post som placeras i en databasfil. Azure Storage har inbyggd datatillgänglighet/redundans som säkerställer att varje post i loggfilen eller sidan i datafilen bevaras även om SQL Server-processen kraschar.

När databasmotor eller operativsystem uppgraderas misslyckas en del av den underliggande infrastrukturen, eller om något kritiskt problem identifieras i SQL Server-processen, flyttar Azure Service Fabric den tillståndslösa SQL Server-processen till en annan tillståndslös beräkningsnod. Det finns en uppsättning extranoder som väntar på att köra ny beräkningstjänst om en redundans av den primära noden inträffar för att minimera redundanstiden. Data i Azure-lagringslagret påverkas inte och data/loggfiler är kopplade till den nyligen initierade SQL Server-processen. Den här processen garanterar 99,99 % tillgänglighet, men det kan ha vissa prestandaeffekter på tung arbetsbelastning som körs på grund av övergångstid och det faktum att den nya SQL Server-noden börjar med kall cache.

## <a name="when-to-choose-this-service-tier"></a>När du ska välja den här tjänstnivån

Servicenivån för allmänt syfte är en standardtjänstnivå i Azure SQL Database som är utformad för de flesta allmänna arbetsbelastningar. Om du behöver en fullständigt hanterad databasmotor med 99,99 % SLA med lagringsfördröjning mellan 5 och 10 ms som matchar Azure SQL IaaS i de flesta fall är allmän nivå alternativet för dig.

## <a name="next-steps"></a>Nästa steg

- Sök efter resursegenskaper (antal kärnor, IO, minne) på nivån Allmänt syfte/standard i [hanterad instans,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)En databas i [vCore-modell](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) eller [DTU-modell](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)eller Elastisk pool i [vCore-modell](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) och [DTU-modell](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Läs mer om [affärskritiska](sql-database-service-tier-business-critical.md) nivåer och [hyperskala.](sql-database-service-tier-hyperscale.md)
- Läs mer om [Service Fabric](../service-fabric/service-fabric-overview.md).
- Fler alternativ för hög tillgänglighet och haveriberedskap finns i [Kontinuitet i verksamheten](sql-database-business-continuity.md).
