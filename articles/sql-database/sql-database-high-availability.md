---
title: Hög tillgänglighet – Azure SQL Database-tjänsten | Microsoft Docs
description: Lär dig mer om Azure SQL Database-funktioner för hög tillgänglighet och funktioner
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: 4e1963e97a7458db8badb63e28dbc3d215ad88b2
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309638"
---
# <a name="high-availability-and-azure-sql-database"></a>Hög tillgänglighet och Azure SQL-databas

Azure SQL-databasen är hög tillgänglighet databas plattform som en tjänst som garanterar att din databas är igång och körs 99,99% av tiden, utan att bekymra dig om underhåll och stillestånd. Det här är en helt hanterad SQL Server Database Engine-process som finns i Azure-molnet som gör att SQL Server-databasen alltid uppgraderas/korrigeras utan att påverka din arbetsbelastning. Azure SQL Database kan snabbt återställa även i de mest kritiska omständigheter att säkerställa att dina data alltid är tillgängligt.

Azure-plattformen fullständigt hanterar alla Azure SQL Database och garanterar att inga data går förlorade och en hög andel datatillgänglighet. Azure hanterar automatiskt korrigering, säkerhetskopior, replikering, felidentifiering, potentiella underliggande maskinvara, programvara eller nätverksfel, distribuera felkorrigeringar, redundans, databasen uppgraderingar och andra underhållsaktiviteter. SQL Server-tekniker har implementerat best-known praxis att säkerställa att alla underhållsåtgärder har utförts i mindre än 0,01% tiden för din databas livslängd. Den här arkitekturen är utformat för att se till att spara data aldrig går förlorade och att underhållsåtgärder utförs utan att påverka arbetsbelastning. Det finns inga underhållsfönster eller stillestånd som bör du behöver avbryta arbetsbelastningen när databasen uppgraderas eller bibehålls. Inbyggd hög tillgänglighet i Azure SQL Database garanterar att databasen blir aldrig enskild felpunkt i programvaruarkitektur.

Det finns två modeller för hög tillgänglighet som används i Azure SQL:

- Standard/allmänt syfte modell som innehåller 99,99% tillgänglighet, men med potentiella prestanda försämras under underhållsaktiviteter.
- Premium/kritiska affärsmodell som tillhandahåller dessutom 99,99% tillgänglighet med minimal prestandapåverkan på din arbetsbelastning även under underhållsaktiviteter.

Azure uppgraderar och korrigeringsfiler underliggande operativsystemet, drivrutiner och SQL Server Database Engine transparent med minimalt driftstopp för slutanvändare. Azure SQL-databas som körs på den senaste säkra versionen av SQL Server Database Engine och Windows-Operativsystemet och de flesta användarna skulle inte lägger märke till att uppgraderingen utförs kontinuerligt.

## <a name="standard-availability"></a>Standard tillgänglighet

Standard tillgänglighet syftar på 99,99% SLA som används i Standard/Basic/allmänt syfte nivåer. Tillgänglighet uppnås genom uppdelning av beräkning och lagring lager. Vi har två lager i modellen standard tillgänglighet:

- En tillståndslös beräkningslager som körs sqlserver.exe-processen och innehåller endast tillfälligt och cachelagrade data (till exempel – plancache, buffertpool, kolumnen store pool). Den här tillståndslös SQL Server-noden drivs av Azure Service Fabric som initierar processen, kontrollerar hälsotillståndet för noden och utför växling till en annan plats om det behövs.
- En tillståndskänslig datalager med databasfiler (.mdf/.ldf) som lagras i Azure Premium Storage diskar. Azure Storage garanterar att inga data går förlorade i poster som placeras i en databasfil. Azure Storage har inbyggda tillgänglighet/dataredundans som garanterar att alla poster i loggfilen eller sidan i datafilen bevaras även om SQL Server-processen kraschar.

När databasmotorn eller operativsystemet uppgraderas eller om vissa viktiga problem upptäcks i Sql Server-processen, tillståndslös SQL Server-processen Azure Service Fabric flyttas till en annan tillståndslös compute-nod. Data i Azure Storage-lagret påverkas inte och data/loggfiler är kopplade till nyligen initierad SQL Server-processen. Förväntade failover-tid kan mäts i sekunder. Den här processen garanterar 99,99% tillgänglighet, men den kan ha vissa prestandaförsämringar på arbetsbelastning som körs på grund av övergångstid och faktum den nya noden i SQL Server startar med kalla cache.

## <a name="premium-availability"></a>Premium-tillgänglighet

Premium tillgång är aktiverad i Premium-nivån i Azure SQL Database och är avsedd för arbetsbelastningar som inte tolererar att eventuella inverkan på prestanda på grund av pågående underhållsåtgärder.

Azure SQL database integrerar beräkning och lagring på nod i premium-modellen. Både SQL Server Database Engine-processen och underliggande mdf/ldf-filerna placeras på samma nod med lokalt anslutna SSD-lagringen tillhandahåller låg latens för din arbetsbelastning.

Hög tillgänglighet implementeras med hjälp av standard [alltid på Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Varje databas är ett kluster med databasnoder med en primär databas som är tillgänglig för kundens arbetsbelastning och några andra processer som innehåller kopior av data. Den primära noden skickar ständigt ändringarna till sekundära noder för att säkerställa att data är tillgängliga på sekundära repliker om den primära noden kraschar av någon anledning. Redundans hanteras av SQL Server Database Engine – en sekundär replik blir den primära noden och en sekundär replik skapas för att säkerställa tillräckligt med noder i klustret. Arbetsbelastningen omdirigeras automatiskt till den nya primära noden. Redundans tiden mäts i millisekunder och den nya primära-instansen är klar att fortsätta begäranden.

## <a name="zone-redundant-configuration-preview"></a>Redundant zonkonfiguration (förhandsgranskning)

Som standard skapas kvorum set-repliker för lokal lagringskonfigurationer i samma datacenter. Med introduktionen av [Azure tillgänglighet zoner](../availability-zones/az-overview.md), har du möjlighet att placera olika repliker i kvorum-aktiverar till olika tillgänglighet zoner i samma region. Om du vill ta bort en enskild felpunkt dupliceras också ringen kontroll över flera zoner som tre gateway ringar (GW). Routning till en specifik gateway ring styrs av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Eftersom zonen redundant konfiguration inte skapa ytterligare databasredundans med tillgänglighet zoner i Premium eller Business kritiska (förhandsgranskning) tjänstnivåer är tillgänglig utan extra kostnad. Genom att välja en zon redundant databas du din Premium eller Business kritiska (förhandsgranskning) databaser motståndskraftiga mot ett mycket större antal fel, inklusive oåterkalleligt datacenteravbrott, utan några ändringar av programlogiken. Du kan också konvertera alla befintliga Premium eller Business kritiska databaser eller pooler (förhandsgranskning) till zonen redundant konfiguration.

Eftersom zonen redundant kvorum-uppsättning har repliker i olika datacenter med några avståndet mellan dem, ökad Nätverksfördröjningen öka tid som genomförande och därmed påverka prestandan för vissa OLTP-arbetsbelastningar. Du kan alltid återgå till en zon konfigurationen genom att inaktivera inställningen zonen redundans. Den här processen liknar den vanliga tjänstuppdateringen mål för servicenivå (SLO) är en storlek på data igen. I slutet av processen har databasen eller poolen migrerats från en zon redundant ring till en enda zon ring eller vice versa.

> [!IMPORTANT]
> Zonen redundant databaser och elastiska pooler är för närvarande stöds bara i premiumnivån. Under förhandsversion, säkerhetskopior och granska poster lagras i RA-GRS lagring och kan därför inte automatiskt tillgänglig vid ett avbrott för hela zonen. 

I följande diagram visas zonen redundant version av arkitekturen för hög tillgänglighet:
 
![hög tillgänglighet arkitektur zonredundant](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Läs skalbar
Enligt beskrivningen, tjänsten Premium och företag kritiska (förhandsgranskning) nivåer utnyttjar kvorum-uppsättningar och alltid på teknik för både i samma zon och redundanta konfigurationer med hög tillgänglighet. En av fördelarna med AlwasyON är att replikerna är alltid i ett konsekvent tillstånd. Eftersom replikerna har samma prestandanivå som den primära servern, programmet kan dra nytta av den extra kapaciteten för att underhålla skrivskyddade arbetsbelastningar utan extra kostnad (skrivskyddade skalbar). Det här sättet skrivskyddad frågorna isoleras från den huvudsakliga skrivskyddad arbetsbelastningen och påverkar inte dess prestanda. Läs skalbar funktionen är avsedd för de program som är logiskt avgränsade skrivskyddade arbetsbelastningar som till exempel analytics och därför kan utnyttja denna ytterligare kapacitet utan att ansluta till primärt. 

Om du vill använda funktionen för Läs skalbar med en viss databas, måste du uttryckligen aktivera den när du skapar databasen eller efteråt genom att ändra konfigurationen med hjälp av PowerShell genom att anropa den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlets eller via Azure Resource Manager REST-API med hjälp av [databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate) metod.

När Läs skalbar har aktiverats för en databas måste program som ansluter till databasen kommer att dirigeras till skrivskyddad-repliken eller till en skrivskyddad replik av databasen enligt den `ApplicationIntent` egenskapen som konfigurerats i programmets anslutningssträng. Mer information om den `ApplicationIntent` egenskapen finns [ange Programavsikt](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Om Läs skalbar är inaktiverat eller egenskapen ReadScale på en tjänstnivå som inte stöds, dirigeras alla anslutningar till skrivskyddad replik, oberoende av den `ApplicationIntent` egenskapen.  

> [!NOTE]
> Det är möjligt att aktivera Läs skalbar på en Standard- eller en generella databas, även om inte resulterar i Routning den skrivskyddade avsedd-session till en separat replik. Detta görs för att stödja befintliga program som skala upp eller ned mellan Standard/allmänt syfte och Premium/företag kritiska nivåer.  

Läs skalbar-funktionen stöder sessionskonsekvens nivå. Om den skrivskyddade sessionen återansluts när ett anslutningsfel orsaka av repliken inte finns, kan det omdirigeras till en annan replik. Medan osannolik, resultera det i att bearbeta den datamängd som är inaktuell. Likaså om ett program skriver data med hjälp av en skrivskyddad-session och läser den med hjälp av den skrivskyddade sessionen omedelbart, är det möjligt att nya data inte visas direkt.

## <a name="conclusion"></a>Sammanfattning
Azure SQL Database är djupt integrerad med Azure-plattformen och är mycket beroende av Service Fabric för identifiering och återställning på Azure Storage Blobs för dataskydd och tillgänglighet zoner för högre feltolerans. På samma gång utnyttjar Azure SQL database fullständigt alltid på Tillgänglighetsgruppen teknik från SQL Server-rutan produkt för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att helt dra nytta av en modell med blandad lagring och stöd för de flesta krävande SLA: er. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure tillgänglighet zoner](../availability-zones/az-overview.md)
- Lär dig mer om [Service Fabric](../service-fabric/service-fabric-overview.md)
- Lär dig mer om [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
