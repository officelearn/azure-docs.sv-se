---
title: Hög tillgänglighet
description: Lär dig mer om funktioner och funktioner för hög tillgänglighet i Azure SQL Database tjänsten
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: b560cee23855d1c0e8a7b3c2cb9d82c184a1ebf6
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732407"
---
# <a name="high-availability-and-azure-sql-database"></a>Hög tillgänglighet och Azure SQL Database

Målet med hög tillgänglighets arkitekturen i Azure SQL Database är att garantera att databasen är igång minst 99,99% av tiden (mer information om ett särskilt service avtal för olika nivåer finns i [SLA för Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/)), utan att oroa dig över påverkan av underhålls åtgärder och avbrott. Azure hanterar automatiskt kritiska underhålls uppgifter, till exempel korrigeringar, säkerhets kopieringar, Windows-och SQL-uppgraderingar, samt oplanerade händelser som underliggande maskin vara, program eller nätverks fel.  När den underliggande SQL-instansen har korrigerats eller växlar över, märks inte stillestånds tiden om du [använder logik för omprövning](sql-database-develop-overview.md#resiliency) i din app. Azure SQL Database kan snabbt återställas även under de mest kritiska omständigheterna för att se till att dina data alltid är tillgängliga.

Lösningen för hög tillgänglighet är utformad för att säkerställa att allokerade data aldrig går förlorade på grund av problem, att underhålls åtgärder inte påverkar din arbets belastning och att databasen inte är en enskild felpunkt i din program varu arkitektur. Det finns inga underhålls perioder som kräver att du stoppar arbets belastningen medan databasen uppgraderas eller underhålls. 

Det finns två arkitektur modeller med hög tillgänglighet som används i Azure SQL Database:

- Standard tillgänglighets modell som baseras på en separation av data bearbetning och lagring.  Den förlitar sig på hög tillgänglighet och tillförlitlighet för Fjärrlagrings nivån. Den här arkitekturen riktar sig till budgetorienterade företags program som kan tolerera viss prestanda försämring under underhålls aktiviteter.
- Premium-tillgänglighets modell som baseras på ett kluster med databas motor processer. Det förlitar sig på att det alltid finns ett kvorum med tillgängliga databas motor noder. Den här arkitekturen riktar sig till verksamhets kritiska program med hög IO-prestanda, hög transaktions hastighet och garanterar minimal prestanda påverkan för din arbets belastning under underhålls aktiviteter.

Azure SQL Database körs på den senaste stabila versionen av SQL Server Database Engine och Windows OS, och de flesta användare skulle inte märka att uppgraderingar utförs kontinuerligt.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Tillgänglighet för Basic, standard och Generell användning Service Tier

Dessa tjänst nivåer utnyttjar standard arkitekturen för tillgänglighet. Följande bild visar fyra olika noder med de avgränsade beräknings-och lagrings lagren.

![Separering av beräkning och lagring](media/sql-database-high-availability/general-purpose-service-tier.png)

Standard tillgänglighets modellen innehåller två lager:

- Ett tillstånds löst beräknings lager som kör `sqlservr.exe`s processen och bara innehåller temporära och cachelagrade data, till exempel TempDB, modell databaser på anslutna SSD och planera cache, resurspool och columnstore-pool i minnet. Den här tillstånds lösa noden drivs av Azure-Service Fabric som initierar `sqlservr.exe`, kontrollerar nodens hälsa och utför redundans till en annan nod vid behov.
- Ett tillstånds känsligt data lager med databasfiler (. MDF/. ldf) som lagras i Azure Blob Storage. Azure Blob Storage har inbyggd data tillgänglighet och en redundans funktion. Det garanterar att alla poster i logg filen eller sidan i data filen kommer att bevaras även om SQL Server process kraschar.

När databas motorn eller operativ systemet uppgraderas, eller om ett problem upptäcks, kommer Azure Service Fabric flytta processen utan tillstånd SQL Server till en annan tillstånds lös Compute-nod med tillräckligt med ledigt utrymme. Data i Azure Blob Storage påverkas inte av flyttningen och data-/loggfilerna bifogas till den nyligen initierade SQL Server processen. Den här processen garanterar 99,99% tillgänglighet, men en kraftig arbets belastning kan uppleva viss prestanda försämring under över gången sedan den nya SQL Server-instansen börjar med kall cache.

## <a name="premium-and-business-critical-service-tier-availability"></a>Premium-och Affärskritisk Service Tier-tillgänglighet

Premium-och Affärskritisk tjänst nivåerna utnyttjar Premium Availability-modellen, som integrerar beräknings resurser (SQL Server databas motor process) och lagring (lokalt anslutna SSD) på en enda nod. Hög tillgänglighet uppnås genom att replikera både beräkning och lagring till ytterligare noder som skapar ett kluster med tre noder. 

![Kluster med noder i databas motorn](media/sql-database-high-availability/business-critical-service-tier.png)

De underliggande databasfilerna (. MDF/. ldf) placeras på den anslutna SSD-lagringen för att tillhandahålla mycket låg latens i/o för din arbets belastning. Hög tillgänglighet implementeras med hjälp av en teknik som liknar SQL Server [Always on-tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Klustret innehåller en enda primär replik (SQL Server process) som är tillgänglig för kundens arbets belastningar med Läs-och skriv åtgärder och upp till tre sekundära repliker (data bearbetning och lagring) som innehåller kopior av data. Den primära noden skickar konstanter ändringar till de sekundära noderna i ordning och säkerställer att data synkroniseras till minst en sekundär replik innan varje transaktion bekräftas. Den här processen garanterar att om den primära noden kraschar av någon anledning finns det alltid en helt synkroniserad nod att redundansväxla till. Redundansväxlingen initieras av Azure-Service Fabric. När den sekundära repliken blir den nya primära noden skapas en annan sekundär replik för att säkerställa att klustret har tillräckligt många noder (kvorumkonfigurationen). När redundansväxlingen är klar omdirigeras SQL-anslutningar automatiskt till den nya primära noden.

Som en extra förmån innehåller Premium Availability-modellen möjligheten att omdirigera skrivskyddade SQL-anslutningar till en av de sekundära replikerna. Den här funktionen kallas för [Läs utskalning](sql-database-read-scale-out.md). Den ger 100% ytterligare beräknings kapacitet utan extra kostnad för att inaktivera Läs åtgärder, till exempel analytiska arbets belastningar, från den primära repliken.

## <a name="hyperscale-service-tier-availability"></a>Tillgänglighet för storskalig Service Tier

Den storskaliga Service Tier-arkitekturen beskrivs i [arkitekturen distribuerade funktioner](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Funktions arkitektur för skala](./media/sql-database-hyperscale/hyperscale-architecture.png)

Tillgänglighets modellen i storskalig innehåller fyra lager:

- Ett tillstånds löst beräknings lager som kör `sqlservr.exe`s processer och bara innehåller temporära och cachelagrade data, till exempel icke-omfattande RBPEX cache, TempDB, modell databas osv. på anslutna SSD och planera cache, resurspool och columnstore-pool i minnet. Detta tillstånds lösa lager inkluderar den primära beräknings repliken och eventuellt ett antal sekundära beräknings repliker som kan fungera som redundans.
- Ett tillstånds löst lagrings lager som bildas av sid servrar. Det här lagret är den distribuerade lagrings motorn för de `sqlservr.exe` processer som körs på beräknings replikerna. Varje Page Server innehåller bara tillfälliga och cachelagrade data, till exempel för att täcka RBPEX-cache på anslutna SSD och data sidor som cachelagras i minnet. Varje sid Server har en länkad sid server i en aktiv-aktiv konfiguration för att tillhandahålla belastnings utjämning, redundans och hög tillgänglighet.
- Ett tillstånds känsligt lagrings lager för transaktions logg som bildas av Compute-noden som kör logg tjänst processen, landnings zonen för transaktions loggen och transaktions loggen långsiktig lagring. Landnings zon och långsiktig lagring använder Azure Storage, vilket ger tillgänglighet och [redundans](https://docs.microsoft.com/azure/storage/common/storage-redundancy) för transaktions loggen, vilket garanterar data hållbarhet för genomförda transaktioner.
- Ett tillstånds känsligt data lagrings lager med databasfilerna (MDF/. NDF) som lagras i Azure Storage och som uppdateras av sid servrar. Det här lagret använder data tillgänglighet och [redundanta](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funktioner i Azure Storage. Det garanterar att varje sida i en datafil kommer att bevaras även om processer i andra lager av arkitektur krascher i stor skala, eller om det inte går att beräkna noder.

Compute-noder i alla storskaliga lager körs på Azure Service Fabric, som kontrollerar hälso tillståndet för varje nod och utför redundans till tillgängliga felfria noder vid behov.

Mer information om hög tillgänglighet i hög tillgänglighet finns i [databas hög tillgänglighet i hög skala](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Redundant zon konfiguration

Som standard skapas klustret av noder för Premium Availability-modellen i samma data Center. Med introduktionen av [Azure-tillgänglighetszoner](../availability-zones/az-overview.md)kan SQL Database placera olika repliker av affärskritisk-databasen till olika tillgänglighets zoner i samma region. För att eliminera en enskild felpunkt dupliceras också kontroll ringen över flera zoner som tre Gateway-ringar (GW). Routningen till en angiven Gateway-ring styrs av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Eftersom zonens redundanta konfiguration i Premium-eller Affärskritisk tjänst nivåerna inte skapar ytterligare databas-redundans, kan du aktivera den utan extra kostnad. Genom att välja en redundant konfiguration av zonen kan du göra dina Premium-eller Affärskritisk-databaser flexibla till en större mängd olika problem, inklusive oåterkalleliga Data Center avbrott, utan några ändringar i program logiken. Du kan också konvertera befintliga Premium-eller Affärskritisk-databaser eller pooler till zonens redundanta konfiguration.

Eftersom zonens redundanta databaser har repliker i olika data Center med lite avstånd mellan dem, kan den ökade nätverks fördröjningen öka genomförande tiden och därmed påverka prestanda för vissa OLTP-arbetsbelastningar. Du kan alltid återgå till konfigurationen med en zon genom att inaktivera inställningen zon redundans. Den här processen är en online-åtgärd som liknar uppgraderingen av Service nivån. I slutet av processen migreras databasen eller poolen från en zon redundant ring till en enskild zon ring eller vice versa.

> [!IMPORTANT]
> Zon redundanta databaser och elastiska pooler stöds för närvarande bara i Premium-och Affärskritisk tjänst nivåerna i utvalda regioner. När du använder Affärskritisk nivån är zonens redundant konfiguration bara tillgänglig när Gen5 Compute-maskinvaran är vald. För uppdaterad information om de regioner som har stöd för zonens redundanta databaser, se [tjänster support per region](../availability-zones/az-overview.md#services-support-by-region).  
> Den här funktionen är inte tillgänglig i en hanterad instans.

Zonens redundanta version av hög tillgänglighets arkitektur illustreras med följande diagram:

![redundant arkitektur zon för hög tillgänglighet](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Accelererad databas återställning (ADR)

[Accelererad databas återställning (ADR)](sql-database-accelerated-database-recovery.md) är en ny funktion i SQL Database Engine som avsevärt förbättrar databasens tillgänglighet, särskilt i närvaro av tids krävande transaktioner. ADR är för närvarande tillgängligt för enskilda databaser, elastiska pooler och Azure SQL Data Warehouse.

## <a name="testing-application-fault-resiliency"></a>Testa program Fels återhämtning

Hög tillgänglighet är en grundläggande del av Azure SQL Database plattform som fungerar transparent för ditt databas program. Vi känner dock igen att du kanske vill testa hur de automatiska redundansväxlingen som initieras under planerade eller oplanerade händelser skulle påverka programmet innan du distribuerar det till produktion. Du kan anropa ett särskilt API för att starta om en databas eller en elastisk pool, vilket i sin tur utlöser en redundansväxling. I händelse av en redundant databas eller elastisk pool skulle API-anrop leda till omdirigering av klient anslutningar till den nya primära i en tillgänglighets zon som skiljer sig från tillgänglighets zonen för den gamla primära. Förutom att testa hur redundansväxlingen påverkar befintliga Databassessioner, kan du också kontrol lera om den ändrar prestandan från slut punkt till slut punkt på grund av ändringar i nätverks fördröjningen. Eftersom omstarten är påträngande och ett stort antal av dem kan stressa plattformen, är det bara ett failover-anrop som är tillåtet var 30: e minut för varje databas eller elastisk pool. 

En redundansväxling kan initieras med hjälp av REST API eller PowerShell. För REST API, se redundansväxling av [databasen](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) och [redundans för elastisk pool](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). För PowerShell, se [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) och [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). REST API-anrop kan också göras från Azure CLI med kommandot [AZ rest](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest) .

> [!IMPORTANT]
> Redundans kommandot är för närvarande inte tillgängligt i den storskaliga tjänst nivån och för en hanterad instans.

## <a name="conclusion"></a>Slutsats

Azure SQL Database har en inbyggd lösning för hög tillgänglighet som är djupt integrerad med Azure-plattformen. Det beror på Service Fabric för identifiering och återställning av fel i Azure Blob Storage för data skydd och på Tillgänglighetszoner för högre fel tolerans. Dessutom utnyttjar Azure SQL Database alltid tillgänglighets grupp teknik från SQL Server för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att helt kunna utnyttja fördelarna med en blandad lagrings modell och stödja de mest krävande service avtal.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure-tillgänglighetszoner](../availability-zones/az-overview.md)
- Läs mer om [Service Fabric](../service-fabric/service-fabric-overview.md)
- Lär dig mer om [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Fler alternativ för hög tillgänglighet och haveri beredskap finns i [verksamhets kontinuitet](sql-database-business-continuity.md)
