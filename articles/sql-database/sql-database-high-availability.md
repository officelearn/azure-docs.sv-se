---
title: Hög tillgänglighet
description: Lär dig mer om Azure SQL Database-tjänsten med hög tillgänglighet och funktioner
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269658"
---
# <a name="high-availability-and-azure-sql-database"></a>Hög tillgänglighet och Azure SQL Database

Målet med arkitekturen Hög tillgänglighet i Azure SQL Database är att garantera att databasen är igång minst 99,99 % av tiden (För mer information om specifika SLA för olika nivåer, Se [SLA för Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/)), utan att oroa dig för effekten av underhållsåtgärder och avbrott. Azure hanterar automatiskt kritiska serviceuppgifter, till exempel korrigering, säkerhetskopior, Windows- och SQL-uppgraderingar, samt oplanerade händelser som underliggande maskinvara, programvara eller nätverksfel.  När den underliggande SQL-instansen är korrigerad eller växlar över, är driftstoppet inte märkbart om du [använder logik för återförsök](sql-database-develop-overview.md#resiliency) i din app. Azure SQL Database kan snabbt återställas även under de mest kritiska omständigheter som säkerställer att dina data alltid är tillgängliga.

Lösningen med hög tillgänglighet är utformad för att säkerställa att bekräftade data aldrig går förlorade på grund av fel, att underhållsåtgärder inte påverkar din arbetsbelastning och att databasen inte blir en enda felpunkt i programvaruarkitekturen. Det finns inga underhållsfönster eller driftstopp som kräver att du stoppar arbetsbelastningen medan databasen uppgraderas eller underhålls. 

Det finns två arkitekturmodeller med hög tillgänglighet som används i Azure SQL Database:

- Standardtillgänglighetsmodell som baseras på en separation av beräkning och lagring.  Den är beroende av hög tillgänglighet och tillförlitlighet på fjärrlagringsnivån. Den här arkitekturen är inriktad på budgetorienterade affärsprogram som kan tolerera viss prestandaförsämring under underhållsaktiviteter.
- Premium tillgänglighet modell som bygger på ett kluster av databasmotor processer. Den bygger på det faktum att det alltid finns ett kvorum för tillgängliga databasmotornoder. Den här arkitekturen riktar sig till verksamhetskritiska program med hög IO-prestanda, hög transaktionsfrekvens och garanterar minimal prestandapåverkan på din arbetsbelastning under underhållsaktiviteter.

Azure SQL Database körs på den senaste stabila versionen av SQL Server Database Engine och Windows OS, och de flesta användare märker inte att uppgraderingar utförs kontinuerligt.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Tillgänglighet för tjänstnivå på grundläggande nivå, standard och allmänt ändamål

Dessa tjänstnivåer utnyttjar standardtillgänglighetsarkitekturen. Följande bild visar fyra olika noder med de separerade beräknings- och lagringslagren.

![Separation av beräkning och lagring](media/sql-database-high-availability/general-purpose-service-tier.png)

Standardtillgänglighetsmodellen innehåller två lager:

- Ett tillståndslöst beräkningslager som kör `sqlservr.exe` processen och innehåller endast tillfälliga och cachelagrade data, till exempel TempDB, modelldatabaser på den bifogade SSD-datorn och plancachen, buffertpoolen och columnstore-poolen i minnet. Den här tillståndslösa noden drivs av `sqlservr.exe`Azure Service Fabric som initierar , styr hälsotillståndet för noden och utför redundans till en annan nod om det behövs.
- Ett tillståndskänsligt datalager med databasfilerna (.mdf/.ldf) som lagras i Azure Blob-lagring. Azure blob storage har inbyggd datatillgänglighet och redundansfunktion. Det garanterar att alla poster i loggfilen eller sidan i datafilen kommer att bevaras även om SQL Server-processen kraschar.

När databasmotorn eller operativsystemet uppgraderas eller ett fel upptäcks flyttar Azure Service Fabric den tillståndslösa SQL Server-processen till en annan tillståndslös beräkningsnod med tillräcklig fri kapacitet. Data i Azure Blob-lagring påverkas inte av flytten och data-/loggfilerna är kopplade till den nyligen initierade SQL Server-processen. Den här processen garanterar 99,99 % tillgänglighet, men en tung arbetsbelastning kan uppleva en viss prestandaförsämring under övergången eftersom den nya SQL Server-instansen börjar med kall cache.

## <a name="premium-and-business-critical-service-tier-availability"></a>Premium- och affärskritisk tjänstnivåtillgänglighet

Premium- och affärskritiska tjänstnivåer utnyttjar Premium-tillgänglighetsmodellen, som integrerar beräkningsresurser (SQL Server Database Engine-process) och lagring (lokalt ansluten SSD) på en enda nod. Hög tillgänglighet uppnås genom att replikera både beräkning och lagring till ytterligare noder som skapar ett kluster med tre till fyra noder. 

![Kluster av databasmotornoder](media/sql-database-high-availability/business-critical-service-tier.png)

De underliggande databasfilerna (.mdf/.ldf) placeras på den bifogade SSD-lagringen för att ge mycket låg latens IO till din arbetsbelastning. Hög tillgänglighet implementeras med hjälp av en teknik som liknar SQL Server [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Klustret innehåller en enda primär replik (SQL Server-process) som är tillgänglig för läs-och skriv-kundarbetsbelastningar och upp till tre sekundära repliker (beräkning och lagring) som innehåller kopior av data. Den primära noden skickar ständigt ändringar till de sekundära noderna i ordning och säkerställer att data synkroniseras till minst en sekundär replik innan varje transaktion genomförs. Den här processen garanterar att om den primära noden kraschar av någon anledning, finns det alltid en helt synkroniserad nod att växla över till. Redundansen initieras av Azure Service Fabric. När den sekundära repliken blir den nya primära noden skapas en annan sekundär replik för att säkerställa att klustret har tillräckligt med noder (kvorumuppsättning). När redundansen är klar omdirigeras SQL-anslutningar automatiskt till den nya primära noden.

Som en extra fördel innehåller premiumtillgänglighetsmodellen möjligheten att omdirigera skrivskyddade SQL-anslutningar till en av de sekundära replikerna. Den här funktionen kallas [Lässkala ut](sql-database-read-scale-out.md). Det ger 100% ytterligare beräkningskapacitet utan extra kostnad för avlästa skrivskyddade åtgärder, till exempel analytiska arbetsbelastningar, från den primära repliken.

## <a name="hyperscale-service-tier-availability"></a>Tillgänglighet för hyperskala på tjänstnivå

Arkitekturen Hyperskala tjänstnivå beskrivs i [arkitekturen Distribuerade funktioner](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Funktionell arkitektur i hyperskala](./media/sql-database-hyperscale/hyperscale-architecture.png)

Tillgänglighetsmodellen i Hyperskala innehåller fyra lager:

- Ett tillståndslöst beräkningslager som kör processerna `sqlservr.exe` och innehåller endast tillfälliga och cachelagrade data, till exempel icke-omfattande RBPEX-cache, TempDB, modelldatabas osv. Det här tillståndslösa lagret innehåller den primära beräkningsrepliken och eventuellt ett antal sekundära beräkningsrepliker som kan fungera som redundansmål.
- Ett tillståndslöst lagringslager som bildas av sidservrar. Det här lagret är den `sqlservr.exe` distribuerade lagringsmotorn för de processer som körs på beräkningsrepliker. Varje sidserver innehåller endast tillfälliga och cachelagrade data, till exempel täcker RBPEX-cache på den bifogade SSD:en och datasidor som cachelagras i minnet. Varje sidserver har en parkopplad sidserver i en aktiv-aktiv konfiguration för att ge belastningsutjämning, redundans och hög tillgänglighet.
- Ett tillståndskänsligt transaktionslogglagringslager som bildas av beräkningsnoden som kör loggtjänstprocessen, landningszonen för transaktionsloggen och långsiktig lagring av transaktionsloggen. Landningszon och långsiktig lagring använder Azure Storage, som ger tillgänglighet och [redundans](https://docs.microsoft.com/azure/storage/common/storage-redundancy) för transaktionslogg, vilket säkerställer datahållbarhet för genomförda transaktioner.
- Ett tillståndskänsligt datalagringslager med databasfilerna (.mdf/.ndf) som lagras i Azure Storage och uppdateras av sidservrar. Det här lagret använder datatillgänglighet och [redundansfunktioner](https://docs.microsoft.com/azure/storage/common/storage-redundancy) i Azure Storage. Det garanterar att varje sida i en datafil bevaras även om processer i andra lager av hyperskalskala arkitektur kraschar, eller om beräkningsnoder misslyckas.

Beräkningsnoder i alla hyperskalalager körs på Azure Service Fabric, som styr hälsotillståndet för varje nod och utför redundans till tillgängliga felfria noder efter behov.

Mer information om hög tillgänglighet i Hyperskala finns [i Hög tillgänglighet för databas i hyperskala](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Zon redundant konfiguration

Som standard skapas klustret med noder för premiumtillgänglighetsmodellen i samma datacenter. Med introduktionen av [Azure-tillgänglighetszoner](../availability-zones/az-overview.md)kan SQL Database placera olika repliker av den affärskritiska databasen till olika tillgänglighetszoner i samma region. För att eliminera en enda felpunkt dupliceras kontrollringen också över flera zoner som tre gatewayringar (GW). Routningen till en specifik gateway-ring styrs av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Eftersom zon redundant konfiguration på Premium- eller Affärskritiska tjänstnivåer inte skapar ytterligare databasredundans kan du aktivera den utan extra kostnad. Genom att välja en zon redundant konfiguration kan du göra dina Premium- eller affärskritiska databaser motståndskraftiga mot en mycket större uppsättning fel, inklusive katastrofala datacenteravbrott, utan några ändringar i programlogiken. Du kan också konvertera befintliga Premium- eller affärskritiska databaser eller pooler till zonens redundanta konfiguration.

Eftersom zon redundanta databaser har repliker i olika datacenter med visst avstånd mellan dem, kan den ökade nätverksfördröjningen öka genomförandetiden och därmed påverka prestanda för vissa OLTP-arbetsbelastningar. Du kan alltid återgå till konfigurationen med en zon genom att inaktivera tidszonsredundansinställningen. Den här processen är en onlineåtgärd som liknar den vanliga uppgraderingen av tjänstnivån. I slutet av processen migreras databasen eller poolen från en zon redundant ring till en enda zonring eller vice versa.

> [!IMPORTANT]
> Zon redundanta databaser och elastiska pooler stöds för närvarande endast i tjänstnivåerna Premium och Affärskritiska i vissa regioner. När du använder nivån Affärskritiska är zon redundant konfiguration endast tillgänglig när Gen5-beräkningsmaskinvaran har valts. Aktuell information om de regioner som stöder zon redundanta databaser finns i Stöd till [Tjänster efter region](../availability-zones/az-overview.md#services-support-by-region).  
> Den här funktionen är inte tillgänglig i Hanterad instans.

Zon redundant version av arkitekturen med hög tillgänglighet illustreras av följande diagram:

![arkitekturzon med hög tillgänglighet redundant](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Snabbare databasåterställning (ADR)

[Accelerated Database Recovery (ADR)](sql-database-accelerated-database-recovery.md) är en ny SQL-databasmotorfunktion som avsevärt förbättrar databasens tillgänglighet, särskilt i närvaro av långvariga transaktioner. ADR är för närvarande tillgängligt för enskilda databaser, elastiska pooler och Azure SQL Data Warehouse.

## <a name="testing-application-fault-resiliency"></a>Testa återhämtning av programfel

Hög tillgänglighet är en grundläggande del av Azure SQL Database-plattformen som fungerar transparent för ditt databasprogram. Vi inser dock att du kanske vill testa hur de automatiska redundansåtgärder som initierades under planerade eller oplanerade händelser skulle påverka programmet innan du distribuerar det till produktion. Du kan anropa ett särskilt API för att starta om en databas eller en elastisk pool, vilket i sin tur utlöser en redundans. När det gäller en zon redundant databas eller elastisk pool, API-anropet skulle resultera i omdirigera klientanslutningar till den nya primära i en tillgänglighetszon skiljer sig från tillgänglighetszonen för den gamla primära. Förutom att testa hur redundans påverkar befintliga databassessioner kan du också kontrollera om prestanda från slutna till slutna resultat ändras på grund av ändringar i nätverksfördröjningen. Eftersom omstarten är påträngande och ett stort antal av dem kan belasta plattformen, tillåts endast ett redundanslag var 30:e minut för varje databas eller elastisk pool. 

En redundans kan initieras med REST API eller PowerShell. För REST API, se [Databas redundans](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) och [elastisk pool redundans](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). För PowerShell finns i [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) och [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). REST API-anrop kan också göras från Azure CLI med kommandot [az rest.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)

> [!IMPORTANT]
> Kommandot Redundans är för närvarande inte tillgängligt på tjänstnivån Hyperskala och för hanterad instans.

## <a name="conclusion"></a>Slutsats

Azure SQL Database har en inbyggd lösning med hög tillgänglighet som är djupt integrerad med Azure-plattformen. Den är beroende av Service Fabric för felidentifiering och återställning, azure blob-lagring för dataskydd och tillgänglighetszoner för högre feltolerans. Dessutom utnyttjar Azure SQL-databasen teknik för alltid på tillgänglighet från SQL Server för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att fullt ut förverkliga fördelarna med en blandad lagringsmodell och stödja de mest krävande SLA:erna.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-tillgänglighetszoner](../availability-zones/az-overview.md)
- Läs mer om [Service Fabric](../service-fabric/service-fabric-overview.md)
- Läs mer om [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Fler alternativ för hög tillgänglighet och haveriberedskap finns i [Affärskontinuitet](sql-database-business-continuity.md)
