---
title: Aktiv geo-replikering – Azure SQL Database
description: Använd aktiv geo-replikering för att skapa läsbara sekundära databaser för enskilda databaser i samma eller olika data Center (region).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 07/09/2019
ms.openlocfilehash: 74cbb9fa5a00b287746afd92fe74f50bfa19110b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691316"
---
# <a name="creating-and-using-active-geo-replication"></a>Skapa och använda aktiv geo-replikering

Aktiv geo-replikering är Azure SQL Database funktion som gör att du kan skapa läsbara sekundära databaser för enskilda databaser på en SQL Database-Server i samma eller olika data Center (region).

> [!NOTE]
> Aktiv geo-replikering stöds inte av hanterade instanser. Använd [grupper för automatisk redundans](sql-database-auto-failover-group.md)för geografisk redundansväxling av hanterade instanser.

Aktiv geo-replikering är utformad som en verksamhets kontinuitets lösning som gör att programmet kan utföra snabb haveri beredskap för enskilda databaser i händelse av en regional katastrof eller ett storskaligt avbrott. Om geo-replikering är aktiverat kan programmet initiera redundansväxling till en sekundär databas i en annan Azure-region. Upp till fyra sekundära servrar stöds i samma eller olika regioner, och sekundärerna kan också användas för skrivskyddade åtkomst frågor. Redundansväxlingen måste initieras manuellt av programmet eller användaren. Efter redundansväxlingen har den nya primära servern en annan slut punkt för anslutningen. Följande diagram illustrerar en typisk konfiguration av ett Geo-redundant moln program som använder aktiv geo-replikering.

![aktiv geo-replikering](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database stöder även grupper för automatisk redundans. Mer information finns i använda [grupper för automatisk redundans](sql-database-auto-failover-group.md). Aktiv geo-replikering stöds inte heller för databaser som skapats inom en hanterad instans. Överväg att använda [failover-grupper](sql-database-auto-failover-group.md) med hanterade instanser.

Om den primära databasen Miss lyckas eller om du behöver bli offline kan du initiera redundansväxlingen till någon av dina sekundära databaser. När redundans har Aktiver ATS till en av de sekundära databaserna, länkas alla andra sekundära sekundära automatiskt till den nya primära.

Du kan hantera replikering och redundans för en enskild databas eller en uppsättning databaser på en server eller i en elastisk pool med hjälp av aktiv geo-replikering. Du kan göra det med:

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: enkel databas](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: elastisk pool](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: enkel databas eller elastisk pool](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: enkel databas](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Aktiv geo-replikering utnyttjar [alltid](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) teknikerna i SQL Server att asynkront replikera genomförda transaktioner i den primära databasen till en sekundär databas med ögonblicks bild isolering. Grupper för automatisk redundans ger gruppsemantiken ovanpå aktiv geo-replikering men samma mekanism för asynkron replikering används. När som helst kan den sekundära databasen vara något bakom den primära databasen, men sekundära data är garanterat att aldrig ha partiella transaktioner. Redundans i flera regioner gör det möjligt för program att snabbt återställa från en permanent förlust av ett helt data Center eller delar av ett Data Center som orsakas av natur katastrofer, katastrof fel eller skadliga åtgärder. Specifika återställnings data finns i [Översikt över affärs kontinuitet](sql-database-business-continuity.md).

> [!NOTE]
> Om det uppstår ett nätverks fel mellan två regioner försöker vi igen var 10: e sekund för att återupprätta anslutningarna.
> [!IMPORTANT]
> För att garantera att en kritisk ändring av den primära databasen replikeras till en sekundär innan du redundansväxlas, kan du framtvinga synkronisering för att säkerställa replikering av kritiska ändringar (till exempel lösen ords uppdateringar). Tvingad synkronisering påverkar prestanda eftersom den blockerar anrops tråden tills alla allokerade transaktioner har repliker ATS. Mer information finns i [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Information om hur du övervakar replikeringsfördröjning mellan den primära databasen och geo-Secondary finns i [sys. DM _geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Följande bild visar ett exempel på aktiv geo-replikering som kon figurer ATS med en primär i regionen Norra centrala USA och sekundär i regionen Södra centrala USA.

![relation mellan geo-replikering](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Eftersom de sekundära databaserna är läsbara kan de användas för att avlasta skrivskyddade arbets belastningar som rapporterings jobb. Om du använder aktiv geo-replikering är det möjligt att skapa den sekundära databasen i samma region med den primära, men det ökar inte programmets återhämtnings förmåga till oåterkalleliga problem. Om du använder grupper för automatisk redundans skapas alltid den sekundära databasen i en annan region.

Förutom haveri beredskap aktiv geo-replikering kan användas i följande scenarier:

- **Databas migrering**: du kan använda aktiv geo-replikering för att migrera en databas från en server till en annan online med minsta stillestånds tid.
- **Program uppgraderingar**: du kan skapa en extra sekundär kopia som en återställning efter en återställning under program uppgraderingar.

För att uppnå verklig affärs kontinuitet är det bara en del av lösningen att lägga till databas redundans mellan data Center. Att återställa ett program (tjänst) från slut punkt till slut punkt efter ett oåterkalleligt haveri kräver återställning av alla komponenter som utgör tjänsten och eventuella beroende tjänster. Exempel på dessa komponenter är klient program varan (till exempel en webbläsare med ett anpassat java script), webb server delar, lagring och DNS. Det är viktigt att alla komponenter är elastiska för samma problem och blir tillgängliga inom RTO (Recovery Time mål) för ditt program. Därför måste du identifiera alla beroende tjänster och förstå de garantier och funktioner som de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att säkerställa att tjänsten fungerar under redundansväxlingen av de tjänster som den är beroende av. Mer information om hur du skapar lösningar för haveri beredskap finns i [utforma moln lösningar för haveri beredskap med hjälp av aktiv geo-replikering](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Terminologi och funktioner för aktiv geo-replikering

- **Automatisk asynkron replikering**

  Du kan bara skapa en sekundär databas genom att lägga till den i en befintlig databas. Den sekundära kan skapas på valfri Azure SQL Database Server. När den väl har skapats fylls den sekundära databasen med data som kopieras från den primära databasen. Den här processen kallas för dirigering. När den sekundära databasen har skapats och dirigerats, replikeras uppdateringar till den primära databasen asynkront till den sekundära databasen automatiskt. Asynkron replikering innebär att transaktioner allokeras till den primära databasen innan de replikeras till den sekundära databasen.

- **Läsbara sekundära databaser**

  Ett program kan komma åt en sekundär databas för skrivskyddade åtgärder med samma eller olika säkerhets objekt som används för att komma åt den primära databasen. De sekundära databaserna används i isolerings läget för ögonblicks bilder för att säkerställa replikeringen av uppdateringar av den primära (logga repetition) inte fördröjs av frågor som körs på den sekundära.

> [!NOTE]
> Logg omuppspelningen fördröjs på den sekundära databasen om det finns schema uppdateringar på den primära. Den senare kräver ett schema lås på den sekundära databasen.
> [!IMPORTANT]
> Du kan använda geo-replikering för att skapa en sekundär databas i samma region som den primära. Du kan använda den här sekundära för att belastningsutjämna en skrivskyddad arbets belastning i samma region. En sekundär databas i samma region ger dock inte ytterligare fel återhämtning och är därför inte ett lämpligt failover-mål för haveri beredskap. Det kommer inte heller att garantera isolering av tillgänglighets zoner. Använd tjänst nivån verksamhets kritisk eller Premium med [Zone-redundant konfiguration](sql-database-high-availability.md#zone-redundant-configuration) för att uppnå isolering av tillgänglighets zoner.   
>

- **Planerad redundans**

  Planerad redundans växlar roller för primära och sekundära databaser när den fullständiga synkroniseringen har slutförts. Det är en online-åtgärd som inte leder till data förlust. Tiden för åtgärden beror på storleken på transaktions loggen på den primära som måste synkroniseras. Planerad redundans är utformat för följande scenarier: (a) för att utföra DR-övningar i produktion när data förlust inte är acceptabel. (b) för att flytta databasen till en annan region. och (c) för att returnera databasen till den primära regionen efter att avbrottet har begränsats (failback).

- **Oplanerad redundansväxling**

  Oplanerad eller framtvingad redundansväxling växlar omedelbart den sekundära till den primära rollen utan någon synkronisering med den primära. Alla transaktioner som allokeras till den primära men inte replikeras till den sekundära kommer att gå förlorade. Den här åtgärden är utformad som en återställnings metod under avbrott när den primära inte är tillgänglig, men databasens tillgänglighet måste återställas snabbt. När den ursprungliga primära servern är online igen, kommer den automatiskt att återanslutas och bli en ny sekundär. Alla osynkroniserade transaktioner innan redundansväxlingen bevaras i säkerhets kopian, men kommer inte att synkroniseras med den nya primära för att undvika konflikter. Dessa transaktioner måste slås samman manuellt med den senaste versionen av den primära databasen.
 
- **Flera läsbara sekundär servrar**

  Upp till 4 sekundära databaser kan skapas för varje primär. Om det bara finns en sekundär databas och den Miss lyckas, exponeras programmet för högre risk tills en ny sekundär databas skapas. Om det finns flera sekundära databaser förblir programmet skyddat även om någon av de sekundära databaserna Miss lyckas. Ytterligare sekundära servrar kan också användas för att skala ut de skrivskyddade arbets belastningarna

  > [!NOTE]
  > Om du använder aktiv geo-replikering för att skapa ett globalt distribuerat program och behöver tillhandahålla skrivskyddad åtkomst till data i mer än fyra regioner kan du skapa sekundärt av en sekundär (en process som kallas länkning). På så sätt kan du uppnå praktiskt taget obegränsad skalning av databasreplikering. Dessutom minskar länkningen av replikeringen från den primära databasen. Kompromissen är den ökade replikeringen för de sekundära databaserna på lövnivå.

- **Geo-replikering av databaser i en elastisk pool**

  Varje sekundär databas kan separat delta i en elastisk pool eller inte vara i någon elastisk pool alls. Valet av pool för varje sekundär databas är inte beroende av konfigurationen av någon annan sekundär databas (oavsett om den är primär eller sekundär). Varje elastisk pool finns i en enda region, och därför kan flera sekundära databaser i samma topologi aldrig dela en elastisk pool.


- **Användarspecifik redundans och återställning efter fel**

  En sekundär databas kan uttryckligen växlas till den primära rollen när som helst av programmet eller användaren. Under ett verkligt avbrott bör alternativet "oplanerat" användas, vilket omedelbart befordrar en sekundär till primär. När de misslyckade primära återställningarna och är tillgängliga igen, markerar systemet automatiskt återställd primär som sekundär och tar den uppdaterad med den nya primära. På grund av den asynkrona typen av replikering kan en liten mängd data gå förlorade under oplanerade växlingar om en primär Miss lyckas innan den replikerar de senaste ändringarna i den sekundära. När en primär med flera sekundära repliker växlar över konfigurerar systemet automatiskt om replikeringsrelationen och länkar de återstående sekundärerna till den nyligen framhävda primären utan att användaren behöver vidta några åtgärder. När det avbrott som orsakade redundansväxlingen har begränsats kan det vara önskvärt att returnera programmet till den primära regionen. För att göra det, ska redundans-kommandot anropas med alternativet "planerat".

## <a name="preparing-secondary-database-for-failover"></a>Förbereder sekundär databas för redundans

För att säkerställa att ditt program omedelbart kan komma åt den nya primära efter redundansväxlingen, kontrollerar du att autentiseringskrav för den sekundära servern och databasen är korrekt konfigurerade. Mer information finns i [SQL Database säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md). För att garantera efterlevnad efter redundansväxlingen, se till att säkerhets kopieringens bevarande princip på den sekundära databasen matchar den primära. De här inställningarna är inte en del av databasen och replikeras inte. Som standard kommer den sekundära att konfigureras med en standard period för PITR på sju dagar. Mer information finns i [SQL Database automatiska säkerhets kopieringar](sql-database-automated-backups.md).

> [!IMPORTANT]
> Om databasen är medlem i en failover-grupp kan du inte initiera dess redundans med hjälp av faiover-kommandot för geo-replikering. Överväg att använda kommandot redundans för gruppen. Om du behöver redundansväxla en enskild databas måste du först ta bort den från gruppen för redundans. Mer information finns i [failover-grupper](sql-database-auto-failover-group.md) . 


## <a name="configuring-secondary-database"></a>Konfigurerar sekundär databas

Både primära och sekundära databaser måste ha samma tjänst nivå. Vi rekommenderar också starkt att den sekundära databasen skapas med samma beräknings storlek (DTU: er eller virtuella kärnor) som primär. Om den primära databasen har en hög Skriv arbets belastning kanske en sekundär med lägre beräknings storlek inte kan hålla sig med den. Det leder till att göra om-fördröjningen på den sekundära och potentiella otillgängligheten. En sekundär databas som inte håller takten med den primära riskerar även att leda till en stor dataförlust om framtvingad redundans skulle krävas. För att minimera dessa risker begränsar den effektiva aktiva geo-replikeringen huvudets logg hastighet så att dess sekundära kan fångas upp. Den andra konsekvensen av en obalanserad sekundär konfiguration är att efter en redundansväxling påverkas programmets prestanda på grund av otillräcklig beräknings kapacitet för den nya primära. Det krävs för att uppgradera till en högre beräkning till den nödvändiga nivån, vilket inte är möjligt förrän störningen minskas. 


> [!IMPORTANT]
> Det går inte att garantera publicerad återställnings punkt = 5 SEK om inte den sekundära databasen har kon figurer ATS med samma beräknings storlek som den primära. 


Om du väljer att skapa den sekundära med lägre beräknings storlek ger loggen i/o-procent på Azure Portal ett bra sätt att uppskatta den minimala beräknings storleken för den sekundära som krävs för att upprätthålla belastningen på replikeringen. Om din primära databas till exempel är P6 (1000 DTU) och dess logg-i/o procent är 50% måste de sekundära måste vara minst P4 (500 DTU). Du kan också hämta logg IO-data med hjälp av [sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) -eller [sys. DM _db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) -databasvyer.  Begränsningen rapporteras som ett HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO vänte läge i vyerna [sys. DM _exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) och [sys. DM _os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) . 

Mer information om SQL Database beräknings storlekarna finns i [SQL Database Service nivåer](sql-database-purchase-models.md).

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Bevara autentiseringsuppgifter och brand Väggs regler i synkronisering

Vi rekommenderar att du använder [IP brand Väggs regler på databas nivå](sql-database-firewall-configure.md) för geo-replikerade databaser så att dessa regler kan replikeras med databasen för att säkerställa att alla sekundära databaser har samma IP-brandvägg som primär. Den här metoden eliminerar behovet av att kunderna manuellt ska konfigurera och underhålla brand Väggs regler på servrar som är värdar för både de primära och sekundära databaserna. Om du använder [inneslutna databas användare](sql-database-manage-logins.md) för data åtkomst säkerställer både primära och sekundära databaser alltid samma autentiseringsuppgifter för användaren, vilket innebär att det inte finns några avbrott på grund av fel matchningar med inloggningar och lösen ord. Med att lägga till [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)kan kunderna hantera användar åtkomst till både primära och sekundära databaser och eliminera behovet av att hantera autentiseringsuppgifter i databaserna helt och hållet.

## <a name="upgrading-or-downgrading-primary-database"></a>Uppgradera eller nedgradera primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräknings storlek (inom samma tjänst nivå, inte mellan Generell användning och Affärskritisk) utan att koppla från några sekundära databaser. Vid uppgraderingen rekommenderar vi att du uppgraderar den sekundära databasen först och sedan uppgraderar den primära. Vid nedgradering, ändra ordning: nedgradera den primära först och nedgradera sedan den sekundära. När du uppgraderar eller nedgradera databasen till en annan tjänst nivå tillämpas den här rekommendationen.

> [!NOTE]
> Om du har skapat en sekundär databas som en del av konfigurationen av redundanskonfiguration bör du inte nedgradera den sekundära databasen. Detta är för att säkerställa att data nivån har tillräckligt med kapacitet för att bearbeta din normala arbets belastning När redundansväxlingen har Aktiver ATS.

> [!IMPORTANT]
> Den primära databasen i en failover-grupp kan inte skalas till en högre nivå om inte den sekundära databasen först skalas till den högre nivån. Om du försöker skala den primära databasen innan den sekundära databasen skalas, kan följande fel meddelande visas:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av kritiska data

På grund av den höga svars tiden för Wide Area Networks använder kontinuerlig kopiering en metod för asynkron replikering. Asynkron replikering gör att data kan gå förlorade om ett fel uppstår. Vissa program kan dock kräva ingen data förlust. För att skydda dessa viktiga uppdateringar kan en programutvecklare anropa system proceduren [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) direkt efter att transaktionen har genomförts. Anrop av **sp_wait_for_database_copy_sync** blockerar anrops tråden tills den senaste genomförda transaktionen har överförts till den sekundära databasen. Det väntar dock inte på att överförda transaktioner ska spelas upp och allokeras på den sekundära. **sp_wait_for_database_copy_sync** är begränsad till en speciell kontinuerlig kopierings länk. Alla användare med anslutnings behörighet till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> **sp_wait_for_database_copy_sync** förhindrar data förlust efter redundansväxlingen, men garanterar inte fullständig synkronisering för Läs behörighet. Fördröjningen som orsakas av ett **sp_wait_for_database_copy_sync** procedur anrop kan vara betydande och beror på storleken på transaktions loggen vid tidpunkten för anropet.

## <a name="monitoring-geo-replication-lag"></a>Övervakning av geo-replikering – fördröjning

Om du vill övervaka fördröjningen med avseende på återställnings punkt använder du *replication_lag_sec* -kolumnen i [sys. DM _geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) på den primära databasen. Det visar fördröjning i sekunder mellan de transaktioner som har allokerats på den primära och behålls på den sekundära. T.ex. Om värdet för fördröjningen är 1 sekund, innebär det att om den primära påverkas av ett avbrott vid detta tillfälle och redundansväxlingen initieras, kommer 1 sekund av de senaste över gångarna inte att sparas. 

För att mäta en fördröjning med avseende på ändringar i den primära databasen som har tillämpats på den sekundära, som är tillgänglig att läsa från den sekundära, jämför *last_commit* tid på den sekundära databasen med samma värde i den primära databasen.

> [!NOTE]
> Ibland har *replication_lag_sec* på den primära databasen ett null-värde, vilket innebär att den primära inte för närvarande vet hur långt den sekundära är.   Detta inträffar vanligt vis när processen har startats om och bör vara ett tillfälligt tillstånd. Överväg att avisera programmet om *replication_lag_sec* returnerar null under en längre tids period. Det indikerar att den sekundära databasen inte kan kommunicera med den primära databasen på grund av ett permanent anslutnings problem. Det finns också villkor som kan orsaka skillnaden mellan *last_commit* -tiden på den sekundära och den primära databasen som blir stor. T.ex. om ett genomförande görs på den primära efter en lång period utan ändringar, kommer skillnaden att gå upp till ett stort värde innan den snabbt returneras till 0. Betrakta det som ett fel tillstånd när skillnaden mellan dessa två värden förblir stor under en längre tid.


## <a name="programmatically-managing-active-geo-replication"></a>Hantera aktiv geo-replikering program mässigt

Som tidigare nämnts kan aktiv geo-replikering även hanteras via programmering med hjälp av Azure PowerShell och REST API. I följande tabeller beskrivs en uppsättning kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning Azure Resource Manager-API: er för hantering, inklusive [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) och [Azure PowerShell-cmdletar](https://docs.microsoft.com/powershell/azure/overview). Dessa API: er kräver användning av resurs grupper och stöd för rollbaserad säkerhet (RBAC). Mer information om hur du implementerar åtkomst roller finns i [Azure Role-Based Access Control](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: hantera redundans för enkla databaser och databaser i pooler

> [!IMPORTANT]
> De här Transact-SQL-kommandona gäller endast för aktiv geo-replikering och gäller inte för failover-grupper. De gäller inte heller för hanterade instanser, eftersom de bara har stöd för failover-grupper.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Använd Lägg till SEKUNDÄRt på SERVER argument för att skapa en sekundär databas för en befintlig databas och starta datareplikering |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Använd REDUNDANS eller FORCE_FAILOVER_ALLOW_DATA_LOSS för att växla en sekundär databas till att vara primär för att initiera redundans |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Använd ta bort sekundär på servern om du vill avsluta en datareplikering mellan en SQL Database och den angivna sekundära databasen. |
| [sys. geo-_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Returnerar information om alla befintliga replikeringsinställningar för varje databas på Azure SQL Database-servern. |
| [sys. DM-_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Hämtar den senaste replikerings tiden, den senaste replikeringsfördröjning och annan information om replikeringslänken för en specifik SQL-databas. |
| [sys. DM-_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Visar status för alla databas åtgärder inklusive status för replikeringslänken. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |gör att programmet väntar tills alla genomförda transaktioner replikeras och bekräftas av den aktiva sekundära databasen. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: hantera redundans för enkla databaser och databaser i pooler

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

| Cmdlet | Beskrivning |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Hämtar en eller flera databaser. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Skapar en sekundär databas för en befintlig databas och startar datareplikeringen. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Växlar en sekundär databas till att vara primär för att initiera redundans. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Avslutar datareplikering mellan en SQL Database och den angivna sekundära databasen. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Hämtar geo-replikeringslänkar mellan en Azure SQL-databas och en resursgrupp eller SQL Server. |
|  | |

> [!IMPORTANT]
> Exempel skript finns i [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) och [Konfigurera och redundansväxla en poolad databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: hantera redundans för enkla databaser och databaser i pooler

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera databas (createMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Skapar, uppdaterar eller återställer en primär eller sekundär databas. |
| [Hämta databas status för att skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Returnerar status under en åtgärd för att skapa. |
| [Ange sekundär databas som primär (planerad redundansväxling)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Anger vilken sekundär databas som är primär genom att redundansväxla från den aktuella primära databasen. **Det här alternativet stöds inte för hanterade instanser.**|
| [Ange sekundär databas som primär (oplanerad redundansväxling)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Anger vilken sekundär databas som är primär genom att redundansväxla från den aktuella primära databasen. Den här åtgärden kan leda till data förlust. **Det här alternativet stöds inte för hanterade instanser.**|
| [Hämta replikeringslänk](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Hämtar en specifik replikeringslänk för en angiven SQL-databas i ett partnerskap med geo-replikering. Den hämtar informationen som visas i vyn sys. geo _replication_links. **Det här alternativet stöds inte för hanterade instanser.**|
| [Länkar för replikering – lista efter databas](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Hämtar alla länkar för replikering för en specifik SQL-databas i en partnerskap för geo-replikering. Den hämtar informationen som visas i vyn sys. geo _replication_links. |
| [Ta bort replikeringslänk](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Tar bort en länk till databasreplikering. Kan inte utföras under redundansväxling. |
|  | |

## <a name="next-steps"></a>Nästa steg

- Exempel skript finns i:
  - [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurera och redundansväxla en pooldatabas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database stöder även grupper för automatisk redundans. Mer information finns i använda [grupper för automatisk redundans](sql-database-auto-failover-group.md).
- För en översikt över verksamhets kontinuitet och scenarier, se [Översikt över verksamhets kontinuitet](sql-database-business-continuity.md)
- Mer information om hur du Azure SQL Database automatiserade säkerhets kopieringar finns [SQL Database automatiska säkerhets kopieringar](sql-database-automated-backups.md).
- Information om hur du använder automatiska säkerhets kopieringar för återställning finns i [återställa en databas från de säkerhets kopior som initieras av tjänsten](sql-database-recovery-using-backups.md).
- Information om autentiseringskrav för en ny primär server och databas finns i [SQL Database säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md).
