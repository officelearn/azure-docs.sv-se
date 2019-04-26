---
title: Hög tillgänglighet – Azure SQL Database-tjänsten | Microsoft Docs
description: Lär dig mer om Azure SQL Database tjänstfunktioner för hög tillgänglighet och funktioner
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392651"
---
# <a name="high-availability-and-azure-sql-database"></a>Hög tillgänglighet och Azure SQL-databas

Målet med arkitektur med hög tillgänglighet i Azure SQL Database är att garanti för att databasen är igång och körs 99,99% av tiden, utan att behöva bekymra dig om effekten av underhåll och driftstopp. Azure hanterar automatiskt kritiska Underhåll aktiviteter, till exempel korrigeringar, säkerhetskopieringar, uppgraderingar för Windows och SQL såväl som oplanerade händelser, till exempel underliggande maskinvara, program- eller jobbavbrott.  När den underliggande SQL-instansen är uppdaterad eller växlar över, avbrottstiden är inte märkbar om du [använder logik för omprövning](sql-database-develop-overview.md#resiliency) i din app. Azure SQL Database kan snabbt återställa även i de mest kritiska omständigheter som säkerställer att dina data alltid är tillgänglig.

Lösning för hög tillgänglighet är utformad för att se till att allokerade data aldrig går förlorad på grund av fel, att underhållsåtgärder inte påverkar din arbetsbelastning och att databasen inte är en enskild felpunkt i din programvaruarkitektur. Det finns inga underhållsperioder eller stilleståndstider som bör kräver att du stoppar arbetsbelastningen när databasen har uppgraderats eller bibehålls. 

Det finns två hög tillgänglighet arkitekturmodeller som används i Azure SQL Database:

- Standard tillgänglighet modell som baseras på en åtskillnad mellan beräkning och lagring.  Den förlitar sig på hög tillgänglighet och tillförlitlighet för Fjärrlagring nivån. Den här arkitekturen som mål budgetinriktade program som kan tolerera prestandan försämras under underhållsaktiviteter.
- Premium tillgänglighet modell som baseras på ett kluster med databasen sökmotor-processer. Den är beroende av det faktum att det finns alltid ett nodkvorum tillgängliga database engine. Den här arkitekturen riktar sig mot verksamhetskritiska program med höga i/o-prestanda, höga transaktionsintervall och garantier minimal prestandapåverkan på din arbetsbelastning under underhållsaktiviteter.

Azure SQL-databas som körs på den senaste stabila versionen av SQL Server Database Engine och Windows OS och de flesta användare skulle inte lägger märke till att uppgraderingar utförs kontinuerligt.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Basic, Standard och generell användning tjänsttillgänglighet nivå

Dessa utnyttja arkitektur standard tillgänglighet. Följande bild visar fyra olika noder med avgränsas lagren för beräkning och lagring.

![Uppdelning av beräkning och lagring](media/sql-database-high-availability/general-purpose-service-tier.png)

Standard tillgänglighet modellen innehåller två nivåer:

- En tillståndslös beräkningslager som kör den `sqlserver.exe` bearbeta och innehåller bara tillfälligt och cachelagrade data på anslutna SSD, t.ex. TempDB, modelldatabasen, plancachen, buffertpoolen och kolumnen pool. Den här tillståndslösa noden drivs av Azure Service Fabric som initierar `sqlserver.exe`kontrollerar hälsotillståndet för noden och utför redundans till en annan nod vid behov.
- En tillståndskänslig datalager med databasfilerna (.mdf/.ldf) som är lagrade i Azure Blob storage. Azure blob-lagring har inbyggda tillgänglighet och redundans-funktionen. Det garanterar att varje post i loggfilen eller sidan i datafilen kommer att bevaras även om SQL Server-processen kraschar.

Varje gång databasmotorn eller operativsystemet uppgraderas eller ett fel upptäcks, flyttas Azure Service Fabric tillståndslösa SQL Server-processen till en annan tillståndslösa beräkningsnod med tillräckligt med kapacitet. Data i Azure Blob storage påverkas inte av övergången och data/loggfiler är kopplade till nyligen initierad SQL Server-processen. Den här processen garanterar 99,99% tillgänglighet, men hård belastning kan uppstå vissa prestandaförsämring under övergången eftersom den nya SQL Server-instansen som börjar med kalla cache.

## <a name="premium-and-business-critical-service-tier-availability"></a>Premium och affärskritisk nivå tjänsttillgänglighet

Premium och affärskritisk service nivåerna utnyttja Premium tillgänglighet modellen, som integrerar beräkningsresurser (SQL Server Database Engine-processen) och lagring (lokalt anslutna SSD) på en enda nod. Hög tillgänglighet uppnås genom att replikera beräkning och lagring till ytterligare noder skapar tre till fyra - kluster med en nod. 

![Kluster på database engine-noder](media/sql-database-high-availability/business-critical-service-tier.png)

Underliggande databasfilerna (.mdf/.ldf) placeras på den anslutna SSD-lagringen för mycket låg latens i/o din arbetsbelastning. Hög tillgänglighet implementeras med hjälp av en teknik som liknar SQL Server [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Klustret innehåller en enda primär replik (SQL Server-processen) som är tillgänglig för Läs-och kundens arbetsbelastningar och upp till tre sekundära repliker (beräkning och lagring) som innehåller kopior av data. Den primära noden ständigt skickar ändringarna till de sekundära noderna i ordning och garanterar att data har synkroniserats till minst en sekundär replik innan du genomför varje transaktion. Den här processen garanterar att om den primära noden kraschar av någon anledning, det finns alltid en helt synkroniserade nod att växla över till. Redundansen initieras av Azure Service Fabric. När den sekundära repliken blir den nya primära noden, skapas en annan sekundär replik för att säkerställa att klustret har tillräckligt många noder (kvorum set). När redundansväxlingen är klar omdirigeras automatiskt SQL-anslutningar till den nya primära noden.

Som en extra förmån innehåller premium tillgänglighet modellen kan omdirigera skrivskyddade SQL-anslutningar till en av de sekundära replikerna. Den här funktionen kallas [Lässkalning](sql-database-read-scale-out.md). Det ger 100% ytterligare beräkningskapacitet utan extra kostnad att avlasta skrivskyddade åtgärder, till exempel analytiska arbetsbelastningar från den primära repliken.

## <a name="zone-redundant-configuration"></a>Zonen redundant konfiguration

Som standard skapas kluster på noder för premium tillgänglighet modellen i samma datacenter. Med introduktionen av [Azure Availability Zones](../availability-zones/az-overview.md), SQL-databas kan placera olika repliker i klustret till olika tillgänglighetszoner i samma region. För att ta bort en enskild felpunkt dupliceras också ringen kontroll över flera zoner som tre gateway-ringar (GW). Routning till en specifik gateway-ring styrs av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Eftersom zonen redundant konfigurationen på tjänstnivåerna Premium och affärskritiska inte skapar ytterligare databasredundans, du kan aktivera den utan extra kostnad. Genom att välja en zonen redundant konfiguration kan du din Premium- eller affärskritiska databaser elastiska ett mycket större antal fel, inklusive oåterkalleligt datacenter-avbrott utan ändringar i programlogiken. Du kan också konvertera alla befintliga Premium eller affärskritiska databaser eller pooler till zonen redundant konfiguration.

Eftersom zonen redundant databaserna har repliker i olika datacenter med vissa avståndet mellan dem, kan ökad Nätverksfördröjningen öka tid som commit och därmed påverka prestandan för vissa OLTP-arbetsbelastningar. Du kan alltid återgå till den enskilda zon konfigurationen genom att inaktivera inställningen zon redundans. Den här processen är en liknande nivå för regelbundna tjänsteuppgraderingen Onlineåtgärden. I slutet av processen har databas eller pool migrerats från en zonen redundant ring en enskild zon ringer eller vice versa.

> [!IMPORTANT]
> Zonen redundant databaser och elastiska pooler finns för närvarande stöds endast på tjänstnivåerna Premium och affärskritisk. Som standard, säkerhetskopior och granska poster lagras i RA-GRS-lagring och därför inte automatiskt tillgängliga i händelse av ett avbrott på hela zonen. 

Zonen redundant versionen av arkitektur med hög tillgänglighet är illustreras med följande diagram:

![hög tillgänglighet arkitektur zonredundant](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Accelererad databasåterställning (ADR)

[Accelerated Database Recovery (ADR)](sql-database-accelerated-database-recovery.md) körs en ny SQL database engine-funktion som avsevärt förbättrar databastillgänglighet, särskilt om det förekommer långa transaktioner. Regel för automatisk distribution är för närvarande tillgängligt för enskilda databaser, elastiska pooler och Azure SQL Data Warehouse.

## <a name="conclusion"></a>Sammanfattning

Azure SQL Database har en inbyggd hög tillgänglighet-lösning som är helt integrerat med Azure-plattformen. Det beror på Service Fabric för identifiering och återställning, Azure Blob storage för att skydda data och Tillgänglighetszoner för högre feltolerans. Dessutom utnyttjar Azure SQL-databas Always On Availability Group-teknik från SQL Server för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att fullständigt utnyttja fördelarna med en blandad lagring modellera och stöd för de mest krävande serviceavtal.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Tillgänglighetszoner i Azure](../availability-zones/az-overview.md)
- Lär dig mer om [Service Fabric](../service-fabric/service-fabric-overview.md)
- Lär dig mer om [med Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Fler alternativ för hög tillgänglighet och katastrofåterställning finns [kontinuitet för företag](sql-database-business-continuity.md)
