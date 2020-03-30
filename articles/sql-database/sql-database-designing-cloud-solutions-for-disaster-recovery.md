---
title: Utforma globalt tillgängliga tjänster
description: Lär dig mer om programdesign för tjänster med högtillgänge med Azure SQL Database.
keywords: molnkatastrofåterställning,katastrofåterställningslösningar,säkerhetskopiering av appdata,geo-replikering,kontinuitetsplanering
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 348bd2b92801217a5aea2ef4d1426c020085e4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269073"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Utforma globalt tillgängliga tjänster med Azure SQL Database

När du skapar och distribuerar molntjänster med Azure SQL Database använder du [aktiva georeplikerings-](sql-database-active-geo-replication.md) eller [automatisk redundansgrupper](sql-database-auto-failover-group.md) för att ge motståndskraft mot regionala avbrott och katastrofala fel. Samma funktion gör att du kan skapa globalt distribuerade program som är optimerade för lokal åtkomst till data. I den här artikeln beskrivs vanliga programmönster, inklusive fördelarna och kompromisserna med varje alternativ.

> [!NOTE]
> Om du använder Premium- eller Affärskritiska databaser och elastiska pooler kan du göra dem motståndskraftiga mot regionala avbrott genom att konvertera dem till zon redundant distributionskonfiguration. Se [Zonundanta databaser](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenario 1: Använda två Azure-regioner för affärskontinuitet med minimal stilleståndstid

I det här fallet har programmen följande egenskaper:

* Programmet är aktivt i en Azure-region
* Alla databassessioner kräver läs- och skrivåtkomst (RW) till data
* Webbnivå och datanivå måste samlokaliseringsplats för att minska svarstiden och trafikkostnaden
* I grund och med detta är driftstopp en högre affärsrisk för dessa applikationer än dataförlust

I det här fallet är programdistributionstopologin optimerad för hantering av regionala katastrofer när alla programkomponenter behöver redundans tillsammans. Diagrammet nedan visar denna topologi. För geografisk redundans distribueras programmets resurser till region A och B. Resurserna i region B utnyttjas dock inte förrän region A misslyckas. En redundansgrupp har konfigurerats mellan de två regionerna för att hantera databasanslutning, replikering och redundans. Webbtjänsten i båda regionerna är konfigurerad för att komma åt databasen via ** &lt;&gt;redundansklassnamnet .database.windows.net** (1). Traffic manager har ställts in för att använda [prioritetsdirigeringsmetod](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) används endast i hela den här artikeln för illustrationsändamål. Du kan använda alla belastningsutjämningslösningar som stöder prioritetsroutningsmetod.

Följande diagram visar den här konfigurationen före ett avbrott:

![Scenario 1. Konfiguration före avbrottet.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Efter ett avbrott i den primära regionen upptäcker SQL Database-tjänsten att den primära databasen inte är tillgänglig och utlöser redundans till den sekundära regionen baserat på parametrarna för den automatiska redundansprincipen (1). Beroende på ditt program-serviceavtal kan du konfigurera en respitperiod som styr tiden mellan identifieringen av avbrottet och själva redundansen. Det är möjligt att traffic manager initierar redundans för slutpunkten innan redundansgruppen utlöser redundansen för databasen. I så fall kan webbprogrammet inte omedelbart återansluta till databasen. Men återanslutningarna kommer automatiskt att lyckas så snart databasen redundansen är klar. När den misslyckade regionen återställs och är online igen återansluts den gamla primärt automatiskt som en ny sekundär. Diagrammet nedan illustrerar konfigurationen efter redundans.

> [!NOTE]
> Alla transaktioner som har genomförts efter redundansen går förlorade under återanslutningen. När redundansen har slutförts kan programmet i region B återansluta och starta om bearbetningen av användarbegäranden. Både webbprogrammet och den primära databasen finns nu i region B och finns kvar i samlokalt.

![Scenario 1. Konfiguration efter redundans](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Om ett avbrott inträffar i region B pausas replikeringsprocessen mellan den primära och den sekundära databasen, men länken mellan de två förblir intakt (1). Trafikhanterad identifierar att anslutningen till region B är bruten och markerar slutpunktswebbapp 2 som Degraderad (2). Programmets prestanda påverkas inte i det här fallet, men databasen exponeras och löper därför högre risk för dataförlust om region A misslyckas i följd.

> [!NOTE]
> För haveriberedskap rekommenderar vi konfigurationen med programdistribution begränsad till två regioner. Detta beror på att de flesta azure-geografiska områden bara har två regioner. Den här konfigurationen skyddar inte ditt program från ett samtidigt oåterkalleligt fel i båda regionerna. I en osannolik händelse av ett sådant fel kan du återställa dina databaser i en tredje region med hjälp av [geoåterställning.](sql-database-disaster-recovery.md#recover-using-geo-restore)
>

 När avbrottet har mildrats synkroniseras den sekundära databasen automatiskt med den primära. Under synkroniseringen kan primärprestanda påverkas. Den specifika effekten beror på mängden data som den nya primärinvalen har fått sedan redundansen. Följande diagram illustrerar ett avbrott i den sekundära regionen:

![Scenario 1. Konfiguration efter ett avbrott i den sekundära regionen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

De viktigaste **fördelarna med** detta designmönster är:

* Samma webbprogram distribueras till båda regionerna utan regionspecifik konfiguration och kräver inte ytterligare logik för att hantera redundans.
* Programmets prestanda påverkas inte av redundans eftersom webbprogrammet och databasen alltid finns i samlokalisen.

Den huvudsakliga **kompromissen** är att programresurserna i region B underutnyttjas för det mesta.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenario 2: Azure-regioner för affärskontinuitet med maximal bevarande av data

Det här alternativet passar bäst för program med följande egenskaper:

* Alla dataförluster är hög affärsrisk. Databasen redundans kan endast användas som en sista utväg om avbrottet orsakas av ett katastrofalt fel.
* Programmet stöder skrivskyddade och skrivskyddade driftslägen och kan fungera i "skrivskyddat läge" under en viss tid.

I det här mönstret växlar programmet till skrivskyddat läge när läs-skriv-anslutningarna börjar få time-out-fel. Webbprogrammet distribueras till båda regionerna och innehåller en anslutning till slutpunkten för läs-och skrivavlyssningsavlyssningspunkten och annan anslutning till slutpunkten för skrivskyddad lyssnare (1). Traffic Manager-profilen bör använda [prioritetsroutning](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Slutpunktsövervakningen](../traffic-manager/traffic-manager-monitoring.md) bör aktiveras för programslutpunkten i varje region (2).

Följande diagram illustrerar den här konfigurationen före ett avbrott:

![Scenario 2. Konfiguration före avbrottet.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

När trafikhanteraren upptäcker ett anslutningsfel till region A växlar den automatiskt användartrafik till programinstansen i region B. Med det här mönstret är det viktigt att du ställer in respitperioden med dataförlust till ett tillräckligt högt värde, till exempel 24 timmar. Det säkerställer att dataförlust förhindras om avbrottet minskas inom den tiden. När webbprogrammet i region B aktiveras börjar läs-skrivåtgärderna misslyckas. Vid den tidpunkten bör den växla till skrivskyddat läge (1). I det här läget dirigeras begärandena automatiskt till den sekundära databasen. Om avbrottet orsakas av ett oåterkalleligt fel kan det troligen inte minskas inom respitperioden. När redundansgruppen upphör att gälla utlöses redundansen. Efter att läs-skriva lyssnaren blir tillgänglig och anslutningarna till den slutar misslyckas (2). Följande diagram illustrerar de två stegen i återställningsprocessen.

> [!NOTE]
> Om avbrottet i den primära regionen minskas inom respitperioden identifierar trafikhanteraren återställning av anslutningen i den primära regionen och växlar användartrafik tillbaka till programinstansen i region A. Programinstansen återupptas och fungerar i läs-skrivläge med hjälp av den primära databasen i region A enligt föregående diagram.

![Scenario 2. Katastrofåterställning stadier.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Om ett avbrott inträffar i region B upptäcker trafikhanteraren felet för slutpunkten web-app-2 i region B och markerar det försämrat (1). Under tiden växlar redundansgruppen den skrivskyddade lyssnaren till region A (2). Det här avbrottet påverkar inte slutanvändarens upplevelse, men den primära databasen exponeras under avbrottet. Följande diagram illustrerar ett fel i den sekundära regionen:

![Scenario 2. Avbrott i den sekundära regionen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

När avbrottet har mildrats synkroniseras den sekundära databasen omedelbart med den primära och den skrivskyddade lyssnaren växlas tillbaka till den sekundära databasen i region B. Under synkroniseringsprestanda för den primära kan påverkas något beroende på mängden data som behöver synkroniseras.

Detta designmönster har flera **fördelar:**

* Den undviker dataförlust under tillfälliga avbrott.
* Driftstopp beror bara på hur snabbt traffic manager upptäcker anslutningsfel, som kan konfigureras.

**Kompromissen** är att programmet måste kunna fungera i skrivskyddat läge.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenario 3: Omlokalisering av program till en annan geografi utan dataförlust och nära noll driftstopp

I det här fallet har programmet följande egenskaper:

* Slutanvändarna får tillgång till programmet från olika geografiska områden
* Programmet innehåller skrivskyddade arbetsbelastningar som inte är beroende av fullständig synkronisering med de senaste uppdateringarna
* Skrivåtkomst till data bör stödjas i samma geografi för majoriteten av användarna
* Läsfördröjning är avgörande för slutanvändarens upplevelse

För att uppfylla dessa krav måste du garantera att användarenheten **alltid** ansluter till programmet som distribueras i samma geografi för skrivskyddade åtgärder, såsom webbdata, analys etc. Oltp-åtgärderna bearbetas i samma geografi **för det mesta.** Till exempel under dagtid bearbetas OLTP-åtgärder i samma geografi, men under de lediga timmarna kan de bearbetas i en annan geografi. Om slutanvändarens aktivitet oftast sker under arbetstid kan du garantera optimal prestanda för de flesta användare för det mesta. Följande diagram visar den här topologin.

Programmets resurser bör distribueras i varje geografi där du har betydande användningsbehov. Om din ansökan till exempel används aktivt i USA, Eu ropeiska unionen och Sydostasien bör programmet distribueras till alla dessa geografiska områden. Den primära databasen bör växlas dynamiskt från en geografi till nästa i slutet av arbetstiden. Denna metod kallas "följ solen". OLTP-arbetsbelastningen ansluter alltid till databasen via ** &lt;&gt;redundansklassnamnet .database.windows.net** (1). Den skrivskyddade arbetsbelastningen ansluter direkt till den lokala databasen med hjälp av databasserverns ** &lt;slutpunktsservernamn&gt;.database.windows.net** (2). Traffic Manager är konfigurerad med [prestandaroutningsmetoden](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Det säkerställer att slutanvändarens enhet är ansluten till webbtjänsten i närmaste region. Trafikhanteraren bör ställas in med slutpunktsövervakning aktiverad för varje slutpunkt för webbtjänsten (3).

> [!NOTE]
> Redundansgruppskonfigurationen definierar vilken region som används för redundans. Eftersom den nya primären är i en annan geografi resulterar redundansen i längre svarstid för både OLTP- och skrivskyddade arbetsbelastningar tills den påverkade regionen är online igen.

![Scenario 3. Konfiguration med primärt i östra USA.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

När allt kommer omkring, till exempel klockan 23 lokal tid, bör de aktiva databaserna flyttas till nästa region (Nordeuropa). Den här uppgiften kan automatiseras helt med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Uppgiften omfattar följande steg:

* Växla primär server i redundansgruppen till Norra Europa med hjälp av en vänskaplig redundans (1)
* Ta bort redundansgruppen mellan östra USA och Norra Europa
* Skapa en ny redundansk grupp med samma namn men mellan Nordeuropa och Östasien (2).
* Lägg till den primära i Nordeuropa och sekundärt i Östasien i denna redundansgrupp (3).

Följande diagram illustrerar den nya konfigurationen efter den planerade redundansen:

![Scenario 3. Övergången till det primära till Nordeuropa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Om ett avbrott till exempel inträffar i norra Europa initieras den automatiska databasens redundans av redundansgruppen, vilket i praktiken leder till att programmet flyttas till nästa region före schemat (1).  I så fall är den amerikanska öst den enda återstående sekundära regionen tills Nordeuropa är online igen. De återstående två regionerna betjänar kunderna i alla tre geografiska områden genom att byta roller. Azure Logic Apps måste justeras i enlighet med detta. Eftersom de återstående regionerna får ytterligare användartrafik från Europa påverkas programmets prestanda inte bara av ytterligare svarstid utan också av ett ökat antal slutanvändaranslutningar. När avbrottet har mildrats i Norra Europa synkroniseras den sekundära databasen där omedelbart med den aktuella primär. Följande diagram illustrerar ett avbrott i Norra Europa:

![Scenario 3. Strömavbrott i Norra Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Du kan minska tiden när slutanvändarens upplevelse i Europa försämras av den långa latensen. För att göra det bör du proaktivt distribuera en programkopia och skapa den sekundära databasen(er) i en annan lokal region (Västeuropa) som en ersättning av offline-programinstansen i Norra Europa. När den senare är online igen kan du bestämma om du vill fortsätta använda Västeuropa eller ta bort kopian av programmet där och byta tillbaka till att använda Nordeuropa.

De viktigaste **fördelarna med** denna design är:

* Den skrivskyddade programarbetsbelastningen kommer åt data i garderobsregionen hela tiden.
* Läs-skriv-programmet arbetsbelastningen kommer åt data i närmaste region under perioden för den högsta aktiviteten i varje geografi
* Eftersom programmet distribueras till flera regioner kan det överleva en förlust av en av regionerna utan några större driftstopp.

Men det finns några **kompromisser:**

* Ett regionalt avbrott resulterar i att geografin påverkas av längre svarstid. Både läs-och skrivskyddade arbetsbelastningar betjänas av programmet i en annan geografi.
* Skrivskyddade arbetsbelastningar måste ansluta till en annan slutpunkt i varje region.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planering av affärskontinuitet: Välj en programdesign för molnkatastrofåterställning

Din specifika strategi för molnkatastrofåterställning kan kombinera eller utöka dessa designmönster så att de bäst uppfyller behoven i ditt program.  Som tidigare nämnts baseras den strategi du väljer på det serviceavtal som du vill erbjuda dina kunder och programdistributionstopologin. För att vägleda ditt beslut jämför följande tabell alternativen baserat på återställningspunktsmål (RPO) och beräknad återhämtningstid (ERT).

| Mönster | RPO | ERT (SENASTE6 |
|:--- |:--- |:--- |
| Aktiv-passiv distribution för haveriberedskap med samtidig databasåtkomst |Läs-skriv-åtkomst < 5 sek |Felidentifieringstid + DNS TTL |
| Aktiv aktiv distribution för programbelastningsutjämning |Läs-skriv-åtkomst < 5 sek |Felidentifieringstid + DNS TTL |
| Aktiv-passiv distribution för databevarande |Skrivskyddad åtkomst < 5 sek | Skrivskyddad åtkomst = 0 |
||Läs-skriv-åtkomst = noll | Läs-skrivåtkomst = Felidentifieringstid + respitperiod med dataförlust |
|||

## <a name="next-steps"></a>Nästa steg

* En översikt över affärskontinuitet och scenarier finns i [Översikt över affärskontinuitet](sql-database-business-continuity.md)
* Mer information om aktiv geo-replikering finns i [Aktiv geo-replikering](sql-database-active-geo-replication.md).
* Mer information om grupper för automatisk redundans finns i [Grupper för automatisk redundans](sql-database-auto-failover-group.md).
* Information om aktiv georeplikering med elastiska pooler finns i [Strategier för återställning av elastisk pool.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
