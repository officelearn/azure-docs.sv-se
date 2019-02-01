---
title: Nivån för allmän användning – Azure SQL Database-tjänsten | Microsoft Docs
description: Lär dig mer om Azure SQL databas för generell användning-nivå
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 943982b056a83488426c48763deac14fd5347b8e
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509012"
---
# <a name="general-purpose-tier---azure-sql-database"></a>Nivån för allmän användning – Azure SQL Database

> [!NOTE]
> Nivån för allmän användning kallas Standard i DTU köpa modellen. En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen i [köpa modeller och resurser för Azure SQL Database](sql-database-service-tiers.md).

Azure SQL Database är baserad på SQL Server Database Engine-arkitektur som är anpassat för molnmiljön för att säkerställa 99,99% tillgänglighet även i fall av infrastrukturfel. Det finns tre arkitekturmodeller som används i Azure SQL Database:
- Generellt syfte 
- Affärskritisk
- Hyperskala

Modell för allmänna ändamål är baserad på en åtskillnad mellan beräkning och lagring. Den här arkitekturen modellen är beroende av hög tillgänglighet och tillförlitlighet i Azure Premium Storage som transparent replikerar databasfiler och garanterar inga data går förlorade om underliggande infrastruktur fel händer.

Följande bild visar fyra noder i arkitekturen standardmodell med avgränsas lagren för beräkning och lagring.

![Uppdelning av beräkning och lagring](media/sql-database-managed-instance/general-purpose-service-tier.png)

Det finns två nivåer i generell användning-modell:

- En tillståndslös beräkningslager som kör den `sqlserver.exe` bearbeta och innehåller bara tillfälliga och cachelagrade data (till exempel – plancachen, buffertpoolen, kolumnen store pool). Den här tillståndslösa SQL Server-noden drivs av Azure Service Fabric som initierar processen, kontrollerar hälsotillståndet för noden och utför redundans till en annan plats om det behövs.
- En tillståndskänslig datalager med databasfiler (.mdf/.ldf) som lagras i Azure Premium Storage. Azure Storage garanterar att inga data går förlorade i poster som placeras i en databasfil. Azure Storage har inbyggda tillgänglighet/redundans som säkerställer att varje post i loggfilen eller sida i datafilen kommer att bevaras även om SQL Server-processen kraschar.

När databasmotorn eller operativsystemet uppgraderas, en del av underliggande den infrastrukturen misslyckas eller om vissa viktiga problem har identifierats i SQL Server-processen, tillståndslösa SQL Server-processen Azure Service Fabric flyttas till en annan tillståndslösa compute-nod. Det finns en uppsättning ledig noder som väntar på att köra nya beräkningstjänst om en redundansväxling av den primära noden händer för att minimera failover-tid. Data i Azure Storage-skiktet påverkas inte och data/loggfiler är kopplade till nyligen initierad SQL Server-processen. Den här processen garanterar 99,99% tillgänglighet, men det kan ha vissa prestandaeffekter på arbetsbelastning som körs på grund av övergångstid och faktumet den nya SQL Server-noden börjar med kalla cache.

## <a name="when-to-choose-this-service-tier"></a>När du ska välja den här?

Nivån för allmän användning service är en standard-tjänstnivå i Azure SQL Database som är utformad för de flesta av de allmänna arbetsbelastningarna. Om du behöver en fullständigt hanterad databasmotor med 99,99% SLA med storage fördröjning mellan 5 och 10 ms som matchar Azure SQL IaaS i de flesta fall är alternativ för dig i nivån generell användning.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [affärskritisk](sql-database-service-tier-business-critical.md) och [hyperskala](sql-database-service-tier-hyperscale.md) nivåer.
- Lär dig mer om [Service Fabric](../service-fabric/service-fabric-overview.md).
- Fler alternativ för hög tillgänglighet och katastrofåterställning, se [affärskontinuitet](sql-database-business-continuity.md).
