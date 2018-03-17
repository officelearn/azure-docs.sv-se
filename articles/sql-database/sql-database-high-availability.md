---
title: "Hög tillgänglighet – Azure SQL Database-tjänsten | Microsoft Docs"
description: "Lär dig mer om Azure SQL Database-funktioner för hög tillgänglighet och funktioner"
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 03/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 8deb78ba108aafc3297e6b96d6d88d0c56c60afd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="high-availability-and-azure-sql-database"></a>Hög tillgänglighet och Azure SQL-databas
Microsoft har gjort molnapparnas sina kunder som hög tillgänglighet är inbyggd i tjänsten och kunder behöver inte fungerar, lägga till särskilda logik för att eller fatta beslut runt HA sedan start för Azure SQL Database PaaS-erbjudande. Microsoft har fullständig kontroll över systemkonfigurationen för hög tillgänglighet och operation, erbjuda kunderna ett SERVICENIVÅAVTAL. Hög tillgänglighet SLA gäller för en SQL-databas i en region och ger inte skydd vid totala region fel som beror på faktorer utanför Microsofts rimliga kontroll (till exempel naturkatastrof, war, av terrorism, upplopp, government åtgärd eller en nätverks- eller enhetsfel som uppstår utanför Microsofts datacenter, inklusive på kundplatser eller mellan kundplatser och Microsofts datacenter).

För att förenkla problemet utrymme för hög tillgänglighet, använder Microsoft följande antaganden:
1.  Fel för maskinvara och programvara är ofrånkomligt
2.  Använd personal gör fel som leda till fel
3.  Planerad underhållsåtgärder orsaka avbrott 

När sådana enskilda händelser är ovanligt i molnskala de varje vecka om inte varje dag. 

## <a name="fault-tolerant-sql-databases"></a>Feltoleranta SQL-databaser
Kunder som är mest intresserad återhämtning av sina egna databaser och är mindre intresserad återhämtning för SQL Database-tjänsten som helhet. 99,99% drifttid för en tjänst är meningslösa om ”databasen” är en del av 0,01% av databaser som är nere. Varje databas måste vara feltolerant och fel-lösning bör aldrig leda till förlust av en allokerad transaktion. 

För data som använder SQL-databas både lokal lagring (LS) baserat på direkt anslutna diskar/virtuella hårddiskar och Fjärrlagring (RS) baserat på Azure Premium Storage sidblobar. 
- Lokal lagring används i Premium-databaser och pooler som är utformade för verksamhetskritiska OLTP program med höga krav på IOPS. 
- Fjärrlagring används för Basic och Standard servicenivåer, utformad för budget inriktade arbetsbelastningar som kräver lagring och beräkningskraft kan skalas för sig. De kan använda en enda sidblob för databasen och loggfilerna och inbyggda lagring mekanismer för replikering och redundans.

I båda fallen replikering, felidentifiering och mekanismer för växling vid fel för SQL-databas är helt automatiserad och fungerar utan mänsklig inblandning. Den här arkitekturen är utformat för att se till att spara data aldrig går förlorade och att data hållbarhet företräde framför allt annat.

Viktiga fördelar:
- Kunder få ut mesta möjliga av replikerade databaser utan att behöva konfigurera eller underhålla komplicerad maskinvara, programvara, operativsystem eller virtualiseringsmiljöer.
- Fullständig ACID-egenskaper hos relationsdatabaser underhålls av systemet.
- Växling vid fel är helt automatiskt utan att spara data går förlorade.
- Routning av anslutningar till den primära repliken hanteras dynamiskt av tjänsten med ingen programlogik som krävs.
- Automatisk redundans hög tillhandahålls utan extra kostnad.

> [!NOTE]
> Arkitekturen beskrivs hög tillgänglighet kan ändras utan föregående meddelande. 

## <a name="data-redundancy"></a>Dataredundans

Lösning för hög tillgänglighet i SQL-databas är baserad på [Always ON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) teknik från SQL Server och gör det fungerar för både LS och RS databaser med minimal skillnader. I LS är konfigurationen Always ON används för beständiga i RS den används för tillgänglighet (låga RTO). 

## <a name="local-storage-configuration"></a>Konfiguration av lokal lagring

I den här konfigurationen ansluts varje databas av management-tjänsten (MS) i ringen för kontrollen. En primär replik och minst två sekundära repliker (kvorum anges) finns i en klient ring som omfattar tre oberoende fysiska delsystem inom samma datacenter. Alla läsningar och skrivningar skickas av gateway (GW) till den primära repliken och skrivningar replikeras asynkront till de sekundära replikerna. SQL-databasen använder ett kvorum-baserade commit-schema där data skrivs till den primära servern och minst en sekundär replik innan genomförda transaktioner.

Den [Service Fabric](../service-fabric/service-fabric-overview.md) redundans system automatiskt återskapar repliker som noder upphör att fungera och underhåller kvorum-medlemskap som noder avvika och delta i systemet. Planerat underhåll är noggrant samordnas för att förhindra att den kvorum set underskrider ett minsta antal (normalt 2). Den här modellen fungerar bra för Premium-databaser, men den kräver redundans i både beräknings- och lagringskomponenter och resulterar i en högre kostnad.

## <a name="remote-storage-configuration"></a>Fjärrlagring konfiguration

Exakt en kopia underhålls i fjärranslutna blob storage, med hjälp av system lagringskapacitet för hållbarhet, redundans och bitars ringröta identifiering för Fjärrlagring konfigurationer (nivåerna Basic och Standard). 

Följande diagram visas arkitektur för hög tillgänglighet:
 
![arkitektur för hög tillgänglighet](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>Identifiering och återställning 
Ett stora distribuerade system måste ett mycket pålitlig fel identifiering system som kan identifiera fel på ett tillförlitligt sätt, snabbt och så nära som möjligt till kunden. Det här systemet är baserad på Azure Service Fabric för SQL-databas. 

Med den primära repliken är det omedelbart uppenbart om och när den primära repliken har misslyckats och arbete kan inte fortsätta eftersom alla läsning och skrivning utförs på den primära repliken först. Den här processen att uppgradera en sekundär replik till statusen för primära har återställning tid mål för Återställningstid = 30 sekunder och återställningspunktmål (RPO) = 0. För att minimera effekten av 30 sekunder RTO är det bästa sättet att försöka återansluta flera gånger med en mindre tid för misslyckade anslutningsförsök.

När en sekundär replik inte är databasen till en minimal kvorum-mängd med några av snabbersättningar. Service fabric initierar omkonfiguration processen liknar den process som följer efter fel på den primära repliken, så efter en kort vänta att avgöra om felet är permanent, en annan sekundär replik skapas. I händelse av tillfälliga out-of-service tillstånd, till exempel ett operativsystemfel eller en uppgradering, är en ny replik inte inbyggd direkt om du vill tillåta noden gick inte att starta om i stället. 

För Fjärrlagring-konfigurationer använder SQL-databas Always ON-funktioner för redundans-databaser under uppgraderingar. För att göra det, en ny SQL-instans är roterade i förväg som en del av den planerade uppgraderingen händelsen och den kopplar och återställer databasfilen från Fjärrlagring. Vid egna processer som kraschat eller andra oplanerade händelser Windows Fabric hanterar instans tillgänglighet och bifogar filen fjärrdatabasen som ett sista steg för återställning.

## <a name="zone-redundant-configuration-preview"></a>Redundant zonkonfiguration (förhandsgranskning)

Som standard skapas kvorum set-repliker för lokal lagringskonfigurationer i samma datacenter. Med introduktionen av [Azure tillgänglighet zoner](../availability-zones/az-overview.md), har du möjlighet att placera olika repliker i kvorum-aktiverar till olika tillgänglighet zoner i samma region. Om du vill ta bort en enskild felpunkt dupliceras också ringen kontroll över flera zoner som tre gateway ringar (GW). Routning till en specifik gateway ring styrs av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Eftersom zonen redundant konfiguration inte att skapa ytterligare databasredundans, användningen av tillgänglighet zoner i premiumnivån är tillgänglig utan extra kostnad. Genom att välja en zon redundant databas kan du Premium-databaser flexibel till ett mycket större fel, inklusive oåterkalleligt datacenteravbrott, utan några ändringar av programlogiken. Du kan också konvertera alla befintliga Premium-databaser och pool till zonen redundant konfiguration.

Eftersom zonen redundant kvorum-uppsättning har repliker i olika datacenter med några avståndet mellan dem, ökad Nätverksfördröjningen öka tid som genomförande och därmed påverka prestandan för vissa OLTP-arbetsbelastningar. Du kan alltid återgå till en zon konfigurationen genom att inaktivera inställningen zonen redundans. Den här processen liknar den vanliga tjänstuppdateringen mål för servicenivå (SLO) är en storlek på data igen. I slutet av processen har databasen eller poolen migrerats från en zon redundant ring till en enda zon ring eller vice versa.

> [!IMPORTANT]
> Zonen redundant databaser och elastiska pooler stöds endast i premiumnivån. Under förhandsversion, säkerhetskopior och granska poster lagras i RA-GRS lagring och kan därför inte automatiskt tillgänglig vid ett avbrott för hela zonen. 

I följande diagram visas zonen redundant version av arkitekturen för hög tillgänglighet:
 
![hög tillgänglighet arkitektur zonredundant](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="conclusion"></a>Sammanfattning
Azure SQL Database är djupt integrerad med Azure-plattformen och är mycket beroende av Service Fabric för identifiering och återställning på Azure Storage Blobs för dataskydd och tillgänglighet zoner för högre feltolerans. På samma gång utnyttjar Azure SQL database fullständigt Always On-teknik från SQL Server-rutan produkt för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att helt dra nytta av en modell med blandad lagring och stöd för de flesta krävande SLA: er. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure tillgänglighet zoner](../availability-zones/az-overview.md)
- Lär dig mer om [Service Fabric](../service-fabric/service-fabric-overview.md)
- Lär dig mer om [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
