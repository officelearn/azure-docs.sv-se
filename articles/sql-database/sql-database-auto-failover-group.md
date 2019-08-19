---
title: Failover-grupper – Azure SQL Database | Microsoft Docs
description: Grupper med automatisk redundans är en SQL Database funktion som gör att du kan hantera replikering och automatisk/samordnad redundansväxling av en grupp databaser på en SQL Database-Server eller alla databaser i en hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 07/18/2019
ms.openlocfilehash: 05e16a67e6b01ce3bd1f03f0649baa1358414ea7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035068"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Använd grupper för automatisk redundans för att aktivera transparent och samordnad redundansväxling av flera databaser

Grupper med automatisk redundans är en SQL Database funktion som gör att du kan hantera replikering och redundans för en grupp databaser på en SQL Database-Server eller alla databaser i en hanterad instans till en annan region. Det är en deklarativ abstraktion ovanpå den befintliga funktionen [aktiv geo-replikering](sql-database-active-geo-replication.md) som är utformad för att förenkla distribution och hantering av geo-replikerade databaser i stor skala. Du kan initiera redundans manuellt eller så kan du delegera den till SQL Database tjänst baserat på en användardefinierad princip. Med det senare alternativet kan du automatiskt återställa flera relaterade databaser i en sekundär region efter ett oåterkalleligt fel eller annan oplanerad händelse som resulterar i fullständig eller partiell förlust av SQL Database tjänstens tillgänglighet i den primära regionen. En failover-grupp kan innehålla en eller flera databaser, som vanligt vis används av samma program. Dessutom kan du använda de läsbara sekundära databaserna för att avlasta skrivskyddade arbets belastningar. Eftersom grupper med automatisk redundans omfattar flera databaser, måste dessa databaser konfigureras på den primära servern. Både primära och sekundära servrar för databaserna i gruppen redundans måste vara i samma prenumeration. Grupper för automatisk redundans stöder replikering av alla databaser i gruppen till endast en sekundär server i en annan region.

> [!NOTE]
> När du arbetar med enskilda databaser eller databaser på en SQL Database-Server och vill ha flera sekundära servrar i samma eller olika regioner, använder du [aktiv geo-replikering](sql-database-active-geo-replication.md). 

När du använder grupper för automatisk redundans med automatisk redundansväxling, resulterar alla avbrott som påverkar en eller flera av databaserna i gruppen i automatisk redundans. Dessutom tillhandahåller grupper för automatisk redundans skrivskyddade och skrivskyddade lyssnar slut punkter som förblir oförändrade under redundansväxling. Oavsett om du använder manuell eller automatisk redundans växlar redundans alla sekundära databaser i gruppen till primär. När databasen har redundans har slutförts uppdateras DNS-posten automatiskt för att omdirigera slut punkterna till den nya regionen. För specifika återställnings-och RTO-data, se [Översikt över affärs kontinuitet](sql-database-business-continuity.md).

När du använder grupper för automatisk redundans med automatisk redundansväxling, resulterar alla avbrott som påverkar databaser i SQL Database-servern eller hanterade instanser i automatisk redundans. Du kan hantera gruppen för automatisk redundans med:

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [PowerShell Redundans grupp](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: Failover-](https://docs.microsoft.com/rest/api/sql/failovergroups)grupp.

Efter redundansväxlingen kontrollerar du att autentiseringskrav för servern och databasen har kon figurer ATS på den nya primära servern. Mer information finns i [SQL Database säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md).

För att uppnå verklig affärs kontinuitet är det bara en del av lösningen att lägga till databas redundans mellan data Center. Att återställa ett program (tjänst) från slut punkt till slut punkt efter ett oåterkalleligt haveri kräver återställning av alla komponenter som utgör tjänsten och eventuella beroende tjänster. Exempel på dessa komponenter är klient program varan (till exempel en webbläsare med ett anpassat java script), webb server delar, lagring och DNS. Det är viktigt att alla komponenter är elastiska för samma problem och blir tillgängliga inom RTO (Recovery Time mål) för ditt program. Därför måste du identifiera alla beroende tjänster och förstå de garantier och funktioner som de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att säkerställa att tjänsten fungerar under redundansväxlingen av de tjänster som den är beroende av. Mer information om hur du skapar lösningar för haveri beredskap finns i [utforma moln lösningar för haveri beredskap med hjälp av aktiv geo-replikering](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Grupp terminologi och funktioner för automatisk redundans

- **Failover-grupp (dim)**

  En grupp för växling vid fel är en namngiven grupp databaser som hanteras av en enda SQL Database Server eller inom en enda hanterad instans som kan redundansväxla som en enhet till en annan region, om alla eller vissa primära databaser blir otillgängliga på grund av ett avbrott i den primära regionen. När det skapas för hanterade instanser innehåller en redundans grupp alla användar databaser i instansen och därför kan bara en failover-grupp konfigureras på en instans.
  
  > [!IMPORTANT]
  > Namnet på gruppen för redundans måste vara globalt unikt inom `.database.windows.net` domänen.

- **SQL Database servrar**

     Med SQL Database-servrar kan vissa eller alla användar databaser på en enda SQL Database-Server placeras i en grupp för växling vid fel. Dessutom stöder en SQL Database-Server flera grupper för växling vid fel på en enda SQL Database-Server.

- **Huvud**

  Den SQL Database Server eller hanterad instans som är värd för de primära databaserna i gruppen för växling vid fel.

- **Alternativ**

  Den SQL Database Server eller hanterad instans som är värd för de sekundära databaserna i gruppen för växling vid fel. Den sekundära kan inte finnas i samma region som den primära.

- **Lägga till enskilda databaser i redundans gruppen**

  Du kan skicka flera databaser på samma SQL Database-Server till samma failover-grupp. Om du lägger till en enda databas i gruppen redundans skapas automatiskt en sekundär databas med samma version och beräknings storlek på den sekundära servern.  Du har angett att servern när gruppen för växling vid fel skapades. Om du lägger till en databas som redan har en sekundär databas på den sekundära servern ärvs den geo-replikeringslänken av gruppen. När du lägger till en databas som redan har en sekundär databas i en server som inte är en del av gruppen redundans, skapas en ny sekundär på den sekundära servern.
  
  > [!IMPORTANT]
  > I en hanterad instans replikeras alla användar databaser. Du kan inte välja en delmängd av användar databaser för replikering i gruppen redundans.

- **Lägga till databaser i elastisk pool i redundans gruppen**

  Du kan publicera alla eller flera databaser i en elastisk pool i samma grupp för redundans. Om den primära databasen finns i en elastisk pool skapas den sekundära automatiskt i den elastiska poolen med samma namn (sekundär pool). Du måste se till att den sekundära servern innehåller en elastisk pool med samma exakta namn och tillräckligt med ledigt utrymme som värd för de sekundära databaserna som ska skapas av gruppen för växling vid fel. Om du lägger till en databas i poolen som redan har en sekundär databas i den sekundära poolen ärvs den geo-replikeringslänken av gruppen. När du lägger till en databas som redan har en sekundär databas i en server som inte är en del av gruppen redundans, skapas en ny sekundär i den sekundära poolen.
  
- **DNS-zon**

  Ett unikt ID som genereras automatiskt när en ny instans skapas. Ett certifikat för flera domäner (SAN) för den här instansen har allokerats för att autentisera klient anslutningarna till en instans i samma DNS-zon. De två hanterade instanserna i samma failover-grupp måste dela DNS-zonen. 
  
  > [!NOTE]
  > Det krävs inget ID för DNS-zon för grupper som skapats för SQL Database-servrar.

- **Läs-och skriv lyssnare för redundans**

  En DNS CNAME-post som pekar på den aktuella primära URL: en. Den skapas automatiskt när gruppen för växling vid fel skapas och tillåter att den skrivskyddade SQL-arbetsbelastningen transparent återansluter till den primära databasen när den primära ändringen sker efter redundansväxlingen. När gruppen för växling vid fel skapas på en SQL Database-Server skapas DNS CNAME-posten för lyssnar-URL: `<fog-name>.database.windows.net`en. När gruppen redundans skapas på en hanterad instans, skapas DNS CNAME-posten för lyssnar-URL: `<fog-name>.zone_id.database.windows.net`en.

- **Skrivskyddad lyssnare för redundans grupp**

  En DNS CNAME-post som pekar på den skrivskyddade lyssnare som pekar på den sekundära URL: en. Den skapas automatiskt när gruppen för växling vid fel skapas och tillåter skrivskyddad SQL-arbetsbelastning att transparent ansluta till den sekundära med de angivna reglerna för belastnings utjämning. När gruppen för växling vid fel skapas på en SQL Database-Server skapas DNS CNAME-posten för lyssnar-URL: `<fog-name>.secondary.database.windows.net`en. När gruppen redundans skapas på en hanterad instans, skapas DNS CNAME-posten för lyssnar-URL: `<fog-name>.zone_id.secondary.database.windows.net`en.

- **Princip för automatisk redundansväxling**

  Som standard konfigureras en failover-grupp med en princip för automatisk redundansväxling. Den SQL Database tjänsten utlöser redundans när ett fel har upptäckts och Grace-perioden har löpt ut. Systemet måste kontrol lera att avbrottet inte kan begränsas av den inbyggda [infrastrukturen för hög tillgänglighet i SQL Databases tjänsten](sql-database-high-availability.md) på grund av effektens omfattning. Om du vill kontrol lera arbets flödet för redundans från programmet kan du inaktivera automatisk redundans.

- **Princip för skrivskyddad redundans**

  Som standard är redundansväxlingen av den skrivskyddade lyssnaren inaktive rad. Det garanterar att den primära prestandan inte påverkas när den sekundära är offline. Det innebär dock också att skrivskyddade sessioner inte kan ansluta förrän den sekundära återställningen har återställts. Om du inte kan tolerera nedtid för de skrivskyddade sessionerna och är OK för att tillfälligt använda den primära för både skrivskyddad och Läs-och Skriv trafik på kostnaderna för den potentiella prestanda försämringen av den primära, kan du aktivera redundans för den skrivskyddade lyssnaren. I så fall omdirigeras den skrivskyddade trafiken automatiskt till den primära om den sekundära inte är tillgänglig.

- **Planerad redundans**

   Planerad redundans utför fullständig synkronisering mellan primära och sekundära databaser innan sekundära växlar till den primära rollen. Detta garanterar ingen data förlust. Planerad redundans används i följande scenarier:

  - Utföra haveri beredskap (DR) i produktion om data förlust inte är acceptabel
  - Flytta databaserna till en annan region
  - Returnera databaserna till den primära regionen efter att avbrottet har begränsats (failback).

- **Oplanerad redundansväxling**

   Oplanerad eller framtvingad redundansväxling växlar omedelbart den sekundära till den primära rollen utan någon synkronisering med den primära. Den här åtgärden leder till data förlust. Oplanerad redundansväxling används som en återställnings metod under avbrott när den primära inte är tillgänglig. När den ursprungliga primära servern är online igen återansluter den automatiskt utan att synkronisera och bli en ny sekundär.

- **Manuell redundans**

  Du kan starta redundans manuellt när som helst oavsett konfigurationen för automatisk redundans. Om principen för automatisk redundansväxling inte har kon figurer ATS krävs manuell redundans för att återställa databaser i gruppen redundans till den sekundära. Du kan initiera Tvingad eller läsvänlig redundans (med fullständig datasynkronisering). Den senare kan användas för att flytta den primära till den sekundära regionen. När redundansväxlingen är klar uppdateras DNS-posterna automatiskt för att säkerställa anslutning till den nya primära

- **Respitperiod med data förlust**

  Eftersom de primära och sekundära databaserna synkroniseras med hjälp av asynkron replikering kan redundansväxlingen leda till data förlust. Du kan anpassa principen för automatisk redundans så att den återspeglar programmets tolerans för data förlust. Genom att konfigurera **GracePeriodWithDataLossHours**kan du styra hur lång tid systemet väntar innan redundansväxlingen initieras som kan leda till data förlust.

- **Flera failover-grupper**

  Du kan konfigurera flera grupper för växling vid fel för samma server par för att kontrol lera skalningen av redundans. Varje grupp växlar över oberoende. Om ditt program för flera klienter använder elastiska pooler kan du använda den här funktionen för att blanda primära och sekundära databaser i varje pool. På så sätt kan du minska effekten av ett avbrott till endast hälften av klienterna.

  > [!NOTE]
  > Den hanterade instansen stöder inte flera grupper för växling vid fel.
  
## <a name="permissions"></a>Behörigheter
Behörigheter för en failover-grupp hanteras via [rollbaserad åtkomst kontroll (RBAC)](../role-based-access-control/overview.md). Rollen [SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) har alla behörigheter som krävs för att hantera grupper med fel. 

### <a name="create-failover-group"></a>Skapa redundans grupp
Om du vill skapa en grupp för växling vid fel behöver du RBAC-skriv åtkomst till både den primära och sekundära servern och till alla databaser i gruppen redundans. För en hanterad instans behöver du RBAC-skriv åtkomst till både den primära och sekundära hanterade instansen, men behörigheterna för enskilda databaser är inte relevanta eftersom det inte går att lägga till eller ta bort enskilda hanterade instans databaser i eller tas bort från en redundansrelation. 

### <a name="update-a-failover-group"></a>Uppdatera en failover-grupp
Om du vill uppdatera en failover-grupp behöver du RBAC-skriv åtkomst till gruppen redundans och alla databaser på den aktuella primära servern eller den hanterade instansen.  

### <a name="failover-a-failover-group"></a>Redundansväxla en failover-grupp
Om du vill redundansväxla en failover-grupp behöver du RBAC-skriv åtkomst till gruppen redundans på den nya primära servern eller den hanterade instansen. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Bästa metoder för att använda failover-grupper med enkla databaser och elastiska pooler

Gruppen för automatisk redundans måste konfigureras på den primära SQL Database-servern och ansluta den till den sekundära SQL Database servern i en annan Azure-region. Grupperna kan innehålla alla eller vissa databaser på dessa servrar. Följande diagram illustrerar en typisk konfiguration av ett Geo-redundant moln program med hjälp av flera databaser och gruppen för automatisk redundans.

![automatisk redundans](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Se [lägga till en enkel databas i en failover-grupp](sql-database-single-database-failover-group-tutorial.md) för en detaljerad steg-för-steg-guide som lägger till en enskild databas i en failover-grupp. 


När du utformar en tjänst med affärs kontinuitet i åtanke följer du dessa allmänna rikt linjer:

- **Använd en eller flera grupper för redundans för att hantera redundans för flera databaser**

  En eller flera failover-grupper kan skapas mellan två servrar i olika regioner (primära och sekundära servrar). Varje grupp kan innehålla en eller flera databaser som återställs som en enhet om alla eller vissa primära databaser blir otillgängliga på grund av ett avbrott i den primära regionen. Gruppen redundans skapar geo-Secondary-databas med samma tjänst mål som den primära. Om du lägger till en befintlig Geo-replikeringsrelation i gruppen redundans kontrollerar du att geo-Secondary är konfigurerat med samma tjänste nivå och beräknings storlek som den primära.

- **Använd Läs-och skriv lyssnare för OLTP-arbetsbelastning**

  När du utför OLTP-åtgärder `<fog-name>.database.windows.net` ska du använda som server-URL och anslutningarna dirigeras automatiskt till den primära. URL: en ändras inte efter redundansväxlingen. Observera att redundansväxlingen innebär att du uppdaterar DNS-posten så att klient anslutningarna omdirigeras till den nya primära primären först efter det att klient-DNS-cachen har uppdaterats.

- **Använd skrivskyddad lyssnare för skrivskyddad arbets belastning**

  Om du har en logiskt isolerad skrivskyddad arbets belastning som är tolerant till viss föråldrade data kan du använda den sekundära databasen i programmet. För skrivskyddade sessioner använder `<fog-name>.secondary.database.windows.net` du som server-URL och anslutningen dirigeras automatiskt till den sekundära. Vi rekommenderar också att du anger i anslutnings strängens läsnings avsikt med hjälp av **ApplicationIntent = ReadOnly**.

- **Förbered dig för prestanda försämring**

  SQL-redundansprotokollmeddelandet är oberoende av resten av programmet eller andra tjänster som används. Programmet kan vara "blandat" med vissa komponenter i en region och vissa i en annan. Undvik försämringen genom att se till att den redundanta program distributionen i DR-regionen och följer dessa [rikt linjer för nätverks säkerhet](#failover-groups-and-network-security).

  > [!NOTE]
  > Programmet i DR-regionen behöver inte använda en annan anslutnings sträng.  

- **Förbered för data förlust**

  Om ett avbrott upptäcks väntar SQL på den period som du har angett av **GracePeriodWithDataLossHours**. Standardvärdet är 1 timme. Om du inte kan erbjuda data förlust, se till att ange **GracePeriodWithDataLossHours** till ett tillräckligt stort antal, till exempel 24 timmar. Använd manuell grupp växling vid fel för att återställa från den sekundära till den primära.

  > [!IMPORTANT]
  > Elastiska pooler med 800 eller färre DTU: er och fler än 250 databaser som använder geo-replikering kan stöta på problem, inklusive längre planerade redundanser och försämrade prestanda.  De här problemen är mer sannolika för Skriv intensiva arbets belastningar när geo-replikeringens slut punkter är mycket åtskilda med geografi eller när flera sekundära slut punkter används för varje databas.  Symptom på de här problemen anges när fördröjningen för geo-replikering ökar med tiden.  Den här fördröjningen kan övervakas med hjälp av [sys. DM _geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Om dessa problem inträffar, kan du öka antalet DTU: er eller minska antalet geo-replikerade databaser i samma pool.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Metod tips för att använda failover-grupper med hanterade instanser

> [!IMPORTANT]
> Grupper för automatisk redundans för hanterade instanser är i offentlig för hands version.

Gruppen för automatisk redundans måste konfigureras på den primära instansen och ansluter den till den sekundära instansen i en annan Azure-region.  Alla databaser i instansen kommer att replikeras till den sekundära instansen. 

Följande diagram illustrerar en typisk konfiguration av ett Geo-redundant moln program med hjälp av en hanterad instans och en grupp för automatisk redundans.

![automatisk redundans](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Se [Lägg till hanterad instans till en failover-grupp](sql-database-managed-instance-failover-group-tutorial.md) för en detaljerad steg-för-steg-guide som lägger till en hanterad instans för att använda redundans-gruppen. 

Om programmet använder hanterad instans som datanivå, följer du dessa allmänna rikt linjer när du utformar för affärs kontinuitet:

- **Skapa den sekundära instansen i samma DNS-zon som den primära instansen**

  För att säkerställa icke-avbruten anslutning till den primära instansen efter redundans måste båda de primära och sekundära instanserna finnas i samma DNS-zon. Det garanterar att samma certifikat för flera domäner (SAN) kan användas för att autentisera klient anslutningarna till någon av de två instanserna i gruppen redundans. När programmet är redo för produktions distribution skapar du en sekundär instans i en annan region och kontrollerar att den delar DNS-zonen med den primära instansen. Du kan göra det genom att ange `DNS Zone Partner` en valfri parameter med hjälp av Azure Portal, PowerShell eller REST API. 

  Mer information om hur du skapar den sekundära instansen i samma DNS-zon som den primära instansen finns i [Hantera redundanskluster med hanterade instanser (för hands version)](#powershell-managing-failover-groups-with-managed-instances-preview).

- **Aktivera replikeringstrafik mellan två instanser**

  Eftersom varje instans är isolerad i sitt eget VNet måste dubbelriktad trafik mellan dessa virtuella nätverk tillåtas. Se [Azure VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Konfigurera en failover-grupp för att hantera redundans för hela instansen**

  Gruppen redundans hanterar redundansväxlingen för alla databaser i instansen. När en grupp skapas blir varje databas i instansen automatiskt geo-replikerad till den sekundära instansen. Du kan inte använda grupper för växling vid fel för att initiera en delvis redundansväxling av en delmängd av databaserna.

  > [!IMPORTANT]
  > Om en databas tas bort från den primära instansen kommer den också att släppas automatiskt på den geo-sekundära instansen.

- **Använd Läs-och skriv lyssnare för OLTP-arbetsbelastning**

  När du utför OLTP-åtgärder `<fog-name>.zone_id.database.windows.net` ska du använda som server-URL och anslutningarna dirigeras automatiskt till den primära. URL: en ändras inte efter redundansväxlingen. Redundansväxlingen innebär att du uppdaterar DNS-posten, så att klient anslutningarna omdirigeras till den nya primära primären först efter det att klient-DNS-cachen har uppdaterats. Eftersom den sekundära instansen delar DNS-zonen med den primära, kommer klient programmet att kunna återansluta till den med samma SAN-certifikat.

- **Anslut direkt till geo-replikerad sekundär för skrivskyddade frågor**

  Om du har en logiskt isolerad skrivskyddad arbets belastning som är tolerant till viss föråldrade data kan du använda den sekundära databasen i programmet. Om du vill ansluta direkt till den geo-replikerade sekundära `server.secondary.zone_id.database.windows.net` använder du som server-URL och anslutningen görs direkt till den geo-replikerade sekundära.

  > [!NOTE]
  > I vissa tjänst nivåer Azure SQL Database stöder användning av [skrivskyddade repliker](sql-database-read-scale-out.md) för att belastningsutjämna skrivskyddade arbets belastningar med en skrivskyddad repliks kapacitet och med hjälp `ApplicationIntent=ReadOnly` av parametern i anslutnings strängen. När du har konfigurerat en geo-replikerad sekundär kan du använda den här funktionen för att ansluta till antingen en skrivskyddad replik på den primära platsen eller på den geo-replikerade platsen.
  > - Använd `<fog-name>.zone_id.database.windows.net`om du vill ansluta till en skrivskyddad replik på den primära platsen.
  > - Använd `<fog-name>.secondary.zone_id.database.windows.net`om du vill ansluta till en skrivskyddad replik på den sekundära platsen.

- **Förbered dig för prestanda försämring**

  SQL-redundansprotokollmeddelandet är oberoende av resten av programmet eller andra tjänster som används. Programmet kan vara "blandat" med vissa komponenter i en region och vissa i en annan. Undvik försämringen genom att se till att den redundanta program distributionen i DR-regionen och följer dessa [rikt linjer för nätverks säkerhet](#failover-groups-and-network-security).

- **Förbered för data förlust**

  Om ett avbrott upptäcks utlöser SQL automatiskt Read-Write-redundans om det inte finns någon data förlust till det bästa av vår kunskap. Annars väntar den på den period som du har angett `GracePeriodWithDataLossHours`. Om du har `GracePeriodWithDataLossHours`angett ska du förbereda för data förlust. I allmänhet prioriterar Azure tillgänglighet under drifts störningar. Om du inte kan erbjuda data förlust, se till att ange GracePeriodWithDataLossHours till ett tillräckligt stort antal, till exempel 24 timmar.

  DNS-uppdateringen av Läs-och skriv lyssnaren sker omedelbart efter det att redundansväxlingen initierats. Den här åtgärden kommer inte att resultera i data förlust. Processen med att växla databas roller kan dock ta upp till 5 minuter under normala förhållanden. När den är klar är vissa databaser i den nya primära instansen fortfarande skrivskyddade. Om redundansväxlingen initieras med hjälp av PowerShell, är hela åtgärden synkron. Om den initieras med hjälp av Azure Portal, visar användar gränssnittet slut för ande status. Om den är initierad med REST API använder du standard Azure Resource Managers avsöknings funktion för att övervaka slut för ande.

  > [!IMPORTANT]
  > Använd manuell gruppredundans för att flytta presidentval tillbaka till den ursprungliga platsen. När det avbrott som orsakade redundansväxlingen minskas kan du flytta dina primära databaser till den ursprungliga platsen. Om du vill göra det ska du initiera den manuella redundansväxlingen av gruppen.

## <a name="failover-groups-and-network-security"></a>Failover-grupper och nätverks säkerhet

För vissa program kräver säkerhets reglerna att nätverks åtkomst till data nivån är begränsad till en viss komponent eller komponenter, till exempel en virtuell dator, webb tjänst osv. Detta krav medför vissa utmaningar för utformning av affärs kontinuitet och användning av failover-grupper. Överväg följande alternativ när du implementerar sådan begränsad åtkomst.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Använda failover-grupper och regler för virtuella nätverk

Om du använder [Virtual Network tjänst slut punkter och regler](sql-database-vnet-service-endpoint-rule-overview.md) för att begränsa åtkomsten till din SQL-databas bör du vara medveten om att varje Virtual Network tjänst slut punkt endast gäller en Azure-region. Slut punkten tillåter inte att andra regioner accepterar kommunikation från under nätet. Därför kan bara de klient program som distribueras i samma region ansluta till den primära databasen. Eftersom redundansväxlingen resulterar i att SQL-klientsessioner dirigeras om till en server i en annan (sekundär) region, kommer dessa sessioner att Miss kopie ras om de härstammar från en klient utanför regionen. Därför går det inte att aktivera den automatiska redundansväxlingen om de deltagande servrarna ingår i Virtual Networks reglerna. Följ dessa steg för att stödja manuell redundans:

1. Etablera de redundanta kopiorna av klient dels komponenterna för ditt program (webb tjänst, virtuella datorer osv.) i den sekundära regionen
2. Konfigurera de [virtuella nätverks reglerna](sql-database-vnet-service-endpoint-rule-overview.md) individuellt för primär och sekundär server
3. Aktivera [redundansväxlingen på klient sidan med en Traffic Manager-konfiguration](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Starta manuell redundansväxling när avbrottet upptäcks. Det här alternativet är optimerat för de program som kräver konsekvent svars tid mellan frontend-och data nivån och som stöder återställning när det gäller antingen klient delen, data nivån eller båda påverkas av avbrottet.

> [!NOTE]
> Om du använder en **skrivskyddad lyssnare** för att belastningsutjämna en skrivskyddad arbets belastning kontrollerar du att arbets belastningen körs i en virtuell dator eller en annan resurs i den sekundära regionen så att den kan ansluta till den sekundära databasen.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Använda failover-grupper och brand Väggs regler för SQL Database

Om din affärs kontinuitets plan kräver redundans med hjälp av grupper med automatisk redundans, kan du begränsa åtkomsten till din SQL-databas med hjälp av traditionella brand Väggs regler.  Följ dessa steg för att stödja automatisk redundans:

1. [Skapa en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Skapa en offentlig belastningsutjämnare](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) och tilldela den till den offentliga IP-adressen.
3. [Skapa ett virtuellt nätverk och de virtuella datorerna](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) för dina klient dels komponenter
4. [Skapa en nätverks säkerhets grupp](../virtual-network/security-overview.md) och konfigurera inkommande anslutningar.
5. Se till att de utgående anslutningarna är öppna för Azure SQL Database med hjälp av SQL [-tjänst tag gen](../virtual-network/security-overview.md#service-tags).
6. Skapa en [brand Väggs regel för SQL Database](sql-database-firewall-configure.md) för att tillåta inkommande trafik från den offentliga IP-adressen som du skapar i steg 1.

Mer information om hur du konfigurerar utgående åtkomst och vilken IP-adress som ska användas i brand Väggs reglerna finns i [utgående anslutningar för belastningsutjämnare](../load-balancer/load-balancer-outbound-connections.md).

Konfigurationen ovan ser till att den automatiska redundansväxlingen inte blockerar anslutningar från klient dels komponenterna och förutsätter att programmet kan tolerera den längre fördröjningen mellan klient delen och data nivån.

> [!IMPORTANT]
> För att garantera affärs kontinuitet för regionala avbrott måste du säkerställa geografisk redundans för både klient dels komponenter och databaserna.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Aktivera geo-replikering mellan hanterade instanser och deras virtuella nätverk

När du ställer in en redundans grupp mellan primära och sekundära hanterade instanser i två olika regioner, isoleras varje instans med hjälp av ett oberoende virtuellt nätverk. För att tillåta replikeringstrafik mellan dessa virtuella nätverk, se till att följande krav uppfylls:

1. De två hanterade instanserna måste finnas i olika Azure-regioner.
1. De två hanterade instanserna måste vara samma tjänst nivå och ha samma lagrings storlek. 
1. Din sekundära hanterade instans måste vara tom (inga användar databaser).
1. De virtuella nätverk som används av de hanterade instanserna måste anslutas via en [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller Express Route. Se till att det inte finns någon brand Väggs regel som blockerar portarna 5022 och 11000-11999 när två virtuella nätverk ansluter till ett lokalt nätverk. Global VNet-peering stöds inte.
1. De två hanterade instans virtuella nätverk kan inte ha överlappande IP-adresser.
1. Du måste konfigurera dina nätverks säkerhets grupper (NSG) så att portarna 5022 och intervallet 11000 ~ 12000 är öppna inkommande och utgående för anslutningar från det andra hanterade instansen under nät. Detta är att tillåta replikeringstrafik mellan instanserna

   > [!IMPORTANT]
   > Felkonfigurerade NSG säkerhets regler leder till låsta databas kopierings åtgärder.

7. Den sekundära instansen är konfigurerad med rätt DNS-zon-ID. DNS-zon är en egenskap hos en hanterad instans och dess ID ingår i värd namns adressen. Zon-ID: t genereras som en slumpmässig sträng när den första hanterade instansen skapas i varje VNet och samma ID tilldelas till alla andra instanser i samma undernät. DNS-zonen kan inte ändras när den har tilldelats. Hanterade instanser som ingår i samma redundanskonfiguration måste dela DNS-zonen. Du gör detta genom att skicka den primära instansens zon-ID som värdet för parametern DnsZonePartner när du skapar den sekundära instansen. 

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uppgradera eller nedgradera en primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräknings storlek (inom samma tjänst nivå, inte mellan Generell användning och Affärskritisk) utan att koppla från några sekundära databaser. När du uppgraderar rekommenderar vi att du uppgraderar alla sekundära databaser först och sedan uppgraderar du den primära. Vid nedgradering ändrar du ordningen: nedgradera först den primära databasen och nedgradera sedan alla sekundära databaser. När du uppgraderar eller nedgradera databasen till en annan tjänst nivå tillämpas den här rekommendationen.

Den här sekvensen rekommenderas särskilt för att undvika problemet där den sekundära vid en lägre SKU blir överbelastad och måste omdirigerings under en uppgraderings-eller degraderings process. Du kan också undvika problemet genom att göra den primära skrivskyddade kostnaden för att påverka alla Läs-och skriv arbets belastningar mot den primära. 

> [!NOTE]
> Om du har skapat en sekundär databas som en del av konfigurationen av redundanskonfiguration bör du inte nedgradera den sekundära databasen. Detta är för att säkerställa att data nivån har tillräckligt med kapacitet för att bearbeta din normala arbets belastning När redundansväxlingen har Aktiver ATS.

> [!IMPORTANT]
> Det finns för närvarande inte stöd för att uppgradera eller nedgradera en hanterad instans som är medlem i en failover-grupp.

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av kritiska data

På grund av den höga svars tiden för Wide Area Networks använder kontinuerlig kopiering en metod för asynkron replikering. Asynkron replikering gör att data kan gå förlorade om ett fel uppstår. Vissa program kan dock kräva ingen data förlust. För att skydda dessa viktiga uppdateringar kan en programutvecklare anropa system proceduren [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) direkt efter att transaktionen har genomförts. Anrop av **sp_wait_for_database_copy_sync** blockerar anrops tråden tills den senaste genomförda transaktionen har överförts till den sekundära databasen. Det väntar dock inte på att överförda transaktioner ska spelas upp och allokeras på den sekundära. **sp_wait_for_database_copy_sync** är begränsad till en speciell kontinuerlig kopierings länk. Alla användare med anslutnings behörighet till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> **sp_wait_for_database_copy_sync** förhindrar data förlust efter redundansväxlingen, men garanterar inte fullständig synkronisering för Läs behörighet. Fördröjningen som orsakas av ett **sp_wait_for_database_copy_sync** procedur anrop kan vara betydande och beror på storleken på transaktions loggen vid tidpunkten för anropet.

## <a name="failover-groups-and-point-in-time-restore"></a>Redundansväxla grupper och återställning av tidpunkt

Information om hur du använder återställning av punkt-till-tid med failover-grupper finns i [punkt i tids återställning (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Hantera failover-grupper program mässigt

Som tidigare nämnts kan grupper för automatisk redundans och aktiv geo-replikering också hanteras program mässigt med hjälp av Azure PowerShell och REST API. I följande tabeller beskrivs en uppsättning kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning Azure Resource Manager-API: er för hantering, inklusive [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) och [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)-cmdletar. Dessa API: er kräver användning av resurs grupper och stöd för rollbaserad säkerhet (RBAC). Mer information om hur du implementerar åtkomst roller finns i [Azure Role-Based Access Control](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: Hantera SQL Database-redundans med enkla databaser och elastiska pooler

| Cmdlet: | Beskrivning |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Det här kommandot skapar en redundans grupp och registrerar den på både primära och sekundära servrar|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Tar bort gruppen för redundans från servern och tar bort alla sekundära databaser som ingår i gruppen |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar konfigurationen för redundans gruppen |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ändrar konfigurationen för redundans gruppen |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Utlöser redundans för gruppen redundans till den sekundära servern |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Lägger till en eller flera databaser i en Azure SQL Database redundans grupp|
|  | |

> [!IMPORTANT]
> Ett exempel skript finns i [Konfigurera och redundansväxla en failover-grupp för en enskild databas](scripts/sql-database-add-single-db-to-failover-group-powershell.md).
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell: Hantera grupper med hanterade instanser (förhands granskning)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>Installera den nyaste för hands versionen av PowerShell

1. Uppdatera PowerShellGet-modulen till 1.6.5 (eller den nyaste för hands versionen). Se [PowerShell-webbplatsen för för hands versionen](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

   ```powershell
      install-module PowerShellGet -MinimumVersion 1.6.5 -force
   ```

2. Kör följande kommandon i ett nytt PowerShell-fönster:

   ```powershell
      import-module PowerShellGet
      get-module PowerShellGet #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>PowerShell-cmdletarna för att skapa en grupp för redundans

| API | Beskrivning |
| --- | --- |
| New-AzureRmSqlDatabaseInstanceFailoverGroup |Det här kommandot skapar en redundans grupp och registrerar den på både primära och sekundära servrar|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |Ändrar konfigurationen för redundans gruppen|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |Hämtar konfigurationen för redundans gruppen|
| Switch-AzureRmSqlDatabaseInstanceFailoverGroup |Utlöser redundans för gruppen redundans till den sekundära servern|
| Remove-AzureRmSqlDatabaseInstanceFailoverGroup | Tar bort en failover-grupp|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Hantera failover-grupper för SQL-databas med enkla databaser och databaser i pooler

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundans grupp](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Skapar eller uppdaterar en failover-grupp |
| [Ta bort redundans grupp](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Tar bort redundans gruppen från servern |
| [Redundansväxling (planerad)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Växlar över från den aktuella primära servern till den här servern. |
| [Framtvinga redundans Tillåt data förlust](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Ails över från den aktuella primära servern till den här servern. Den här åtgärden kan leda till data förlust. |
| [Hämta redundans grupp](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Hämtar en redundans grupp. |
| [Visa lista över grupper efter fel per server](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Visar en lista över failover-grupper på en server. |
| [Uppdatera redundans grupp](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Uppdaterar en failover-grupp. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>REST API: Hantera redundanskluster med hanterade instanser (förhands granskning)

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundans grupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Skapar eller uppdaterar en failover-grupp |
| [Ta bort redundans grupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Tar bort redundans gruppen från servern |
| [Redundansväxling (planerad)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Växlar över från den aktuella primära servern till den här servern. |
| [Framtvinga redundans Tillåt data förlust](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |Ails över från den aktuella primära servern till den här servern. Den här åtgärden kan leda till data förlust. |
| [Hämta redundans grupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Hämtar en redundans grupp. |
| [Visa lista över redundanskluster – lista efter plats](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Visar en lista över failover-grupper på en plats. |

## <a name="next-steps"></a>Nästa steg

- Detaljerade självstudier finns i
    - [Lägga till en enkel databas i en failover-grupp](sql-database-single-database-failover-group-tutorial.md)
    - [Lägga till elastisk pool i en redundans grupp](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Lägga till en hanterad instans i en grupp för redundans](sql-database-managed-instance-failover-group-tutorial.md)
- Exempel skript finns i:
  - [Använd PowerShell för att konfigurera aktiv geo-replikering för en enskild databas i Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Använd PowerShell för att konfigurera aktiv geo-replikering för en poolad databas i Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Använd PowerShell för att lägga till en Azure SQL Database enkel databas i en failover-grupp](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- För en översikt över verksamhets kontinuitet och scenarier, se [Översikt över verksamhets kontinuitet](sql-database-business-continuity.md)
- Mer information om hur du Azure SQL Database automatiserade säkerhets kopieringar finns [SQL Database automatiska säkerhets kopieringar](sql-database-automated-backups.md).
- Information om hur du använder automatiska säkerhets kopieringar för återställning finns i [återställa en databas från de säkerhets kopior som initieras av tjänsten](sql-database-recovery-using-backups.md).
- Information om autentiseringskrav för en ny primär server och databas finns i [SQL Database säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md).
