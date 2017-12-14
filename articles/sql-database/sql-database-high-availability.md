---
title: "Hög tillgänglighet – Azure SQL Database-tjänsten | Microsoft Docs"
description: "Lär dig mer om Azure SQL Database-funktioner för hög tillgänglighet och funktioner"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
ms.assetid: 
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c0a140c959f14c2e8ceaddad5d323f0900be5d2f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>Hög tillgänglighet och Azure SQL-databas
Sedan start för Azure SQL Database PaaS-erbjudande har Microsoft gjort vad våra kunder som hög tillgänglighet är inbyggd i tjänsten och kunderna är inte krävas att fungera, lägga till särskilda logik för att eller fatta beslut runt hög tillgänglighet. Microsoft ger kunderna ett SLA och underhåll för full kontroll över systemkonfiguration för hög tillgänglighet och åtgärden. Hög tillgänglighet SLA gäller för en SQL-databas i en region och ger inte skydd vid ett totalt region-fel som beror på faktorer som ligger utanför vårt rimliga kontroll (till exempel naturkatastrof war, fungerar terrorism, upplopp, government åtgärd eller ett nätverk eller enhetsfel utanför vårt datacenter, inklusive hos kunden eller mellan kunden och vårt datacenter).

För att förenkla problemet utrymme för hög tillgänglighet, använder Microsoft följande antaganden:
1.  Fel för maskinvara och programvara är ofrånkomligt
2.  Använd personal gör fel som leda till fel
3.  Planerad underhållsåtgärder orsaka avbrott 

När sådana enskilda händelser är ovanligt i molnskala de varje vecka om inte varje dag. 

## <a name="fault-tolerant-sql-databases"></a>Feltoleranta SQL-databaser
Kunder som är mest intresserad återhämtning av sina egna databaser och är mindre intresserad återhämtning för SQL Database-tjänsten som helhet. 99,99% drifttid för en tjänst är meningslösa om ”databasen” är en del av 0,01% av databaser som är nere. Varje databas måste vara feltolerant och fel-lösning bör aldrig leda till förlust av en allokerad transaktion. 

För data som använder SQL-databas både lokal lagring (LS) baserat på direkt anslutna diskar/virtuella hårddiskar och Fjärrlagring (RS) baserat på Azure Premium Storage sidblobar. 
- Lokal lagring används i Premium-databaser och pooler som har utformats för OLTP-program med höga krav på IOPS. 
- Fjärrlagring används för Basic och Standard servicenivåer, avsedd för små, kalla eller stora databaser som behöver lagring och beräkna ström för att skala oberoende av varandra. De kan använda en enda sidblob för databasen och loggfilerna och inbyggda lagring mekanismer för replikering och redundans.

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

Lösning för hög tillgänglighet i SQL-databas är baserad på [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) teknik från SQL Server och gör det fungerar för både LS och RS databaser med minimal skillnader. I LS configuration AlwaysON används för beständiga i RS används den för tillgänglighet (låga RTO). 

## <a name="local-storage"></a>Lokal lagring

I fallet med LS måste är varje databas online av management-tjänsten (MS) i ringen för kontrollen. En primär replik och minst två sekundära repliker (kvorum anges) finns i en klient ring som omfattar tre oberoende fysiska delsystem inom samma datacenter. Alla läsningar och skrivningar skickas av gateway (GW) till den primära repliken och skrivningar replikeras asynkront till de sekundära replikerna. SQL-databasen använder ett kvorum-baserade commit-schema där data skrivs till den primära servern och minst en sekundär replik innan genomförda transaktioner.

Den [Service Fabric](/azure/service-fabric/service-fabric-overview.md) redundans system automatiskt återskapar repliker som noder upphör att fungera och underhåller kvorum-medlemskap som noder avvika och delta i systemet. Planerat underhåll är noggrant samordnas för att förhindra att den kvorum set underskrider ett minsta antal (normalt 2). Den här modellen fungerar bra för Premium-databaser, men den kräver redundans i både beräknings- och lagringskomponenter och resulterar i en högre kostnad.

## <a name="remote-storage"></a>Fjärrlagring

Exakt en kopia av databasen bevaras i remote blob storage, utnyttja system lagringskapacitet för hållbarhet, redundans och bitars ringröta identifiering för Fjärrlagring konfigurationer (nivåerna Basic och Standard). 

Följande diagram visas arkitektur för hög tillgänglighet:
 
![arkitektur för hög tillgänglighet](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>Felidentifiering och återställning 
Ett stora distribuerade system måste ett mycket pålitlig fel identifiering system som kan identifiera fel på ett tillförlitligt sätt, snabbt och så nära som möjligt till kunden. Det här systemet är baserad på Azure Service Fabric för SQL-databas. 

Med den primära repliken är det omedelbart uppenbart om och när den primära repliken har misslyckats och arbete kan inte fortsätta eftersom alla läsning och skrivning utförs på den primära repliken först. Den här processen att uppgradera en sekundär replik till statusen för primära har återställning tid mål för Återställningstid = 30 sekunder och återställningspunktmål (RPO) = 0. För att minimera effekten av 30 sekunder RTO är det bästa sättet att försöka återansluta flera gånger med en mindre tid för misslyckade anslutningsförsök.

När en sekundär replik inte är databasen till en minimal kvorum-mängd med några av snabbersättningar. Service fabric initierar omkonfiguration processen liknar den process som följer efter fel på den primära repliken, så efter en kort vänta att avgöra om felet är permanent, en annan sekundär replik skapas. I händelse av tillfälliga out-of-service tillstånd, till exempel ett operativsystemfel eller en uppgradering, är en ny replik inte inbyggd direkt om du vill tillåta noden gick inte att starta om i stället. 

För Fjärrlagring-konfigurationer SQL-databas som använder AlwaysON-funktioner för redundans databaser under uppgraderingen. För att göra det, en ny SQL-instans är roterade i förväg som en del av den planerade uppgraderingen händelsen och den kopplar och återställer databasfilen från Fjärrlagring. Vid egna processer som kraschat eller andra oplanerade händelser Windows Fabric hanterar instans tillgänglighet och bifogar filen fjärrdatabasen som ett sista steg för återställning.

## <a name="conclusion"></a>Slutsats
Azure SQL DB är djupt integrerad med Azure-plattformen och är mycket beroende på Service Fabric för identifiering och återställning och Azure Storage Blobs för dataskydd. På samma gång använder Azure SQL database AlwaysOn från rutan SQL Server-produkt för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att helt dra nytta av en modell med blandad lagring och stöd för de flesta krävande SLA: er. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Service Fabric](/azure/service-fabric/service-fabric-overview.md)
- Lär dig mer om [Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md) 
