---
title: Aktiv geo-replikering
description: Använd aktiv georeplikering för att skapa läsbara sekundära databaser med enskilda databaser i samma eller olika datacenter (region).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/06/2020
ms.openlocfilehash: cc9d129894cefaf2fab853d2099d754d68238e5f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887358"
---
# <a name="creating-and-using-active-geo-replication"></a>Skapa och använda aktiv geo-replikering

Aktiv georeplikering är en Azure SQL Database-funktion som gör att du kan skapa läsbara sekundära databaser med enskilda databaser på en SQL Database-server i samma eller olika datacenter (region).

> [!NOTE]
> Aktiv geo-replikering stöds inte av hanterad instans. För geografisk redundans för hanterade instanser använder du [Grupper för automatisk redundans](sql-database-auto-failover-group.md).

Aktiv geo-replikering är utformad som en lösning för affärskontinuitet som gör att programmet kan utföra snabb haveriberedskap av enskilda databaser i händelse av en regional katastrof eller storskaligt avbrott. Om geo-replikering är aktiverat kan programmet initiera redundans till en sekundär databas i en annan Azure-region. Upp till fyra sekundärer stöds i samma eller olika regioner, och sekundärerna kan också användas för skrivskyddade åtkomstfrågor. Redundansen måste initieras manuellt av programmet eller användaren. Efter redundans har den nya primärt en annan anslutningsslutpunkt. Följande diagram illustrerar en typisk konfiguration av ett geoupptundant molnprogram med Active geo-replication.

![aktiv geo-replikering](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database stöder också grupper för automatisk redundans. Mer information finns i använda [grupper för automatisk redundans](sql-database-auto-failover-group.md). Aktiv geo-replikering stöds inte heller för databaser som skapats i en hanterad instans. Överväg att använda [redundansklar](sql-database-auto-failover-group.md) med hanterade instanser.

Om din primära databas av någon anledning misslyckas, eller helt enkelt behöver tas offline, kan du initiera redundans till någon av dina sekundära databaser. När redundans aktiveras till en av de sekundära databaserna länkas alla andra sekundärfiler automatiskt till den nya primär.

Du kan hantera replikering och redundans för en enskild databas eller en uppsättning databaser på en server eller i en elastisk pool med hjälp av aktiv georeplikering. Du kan göra det med hjälp av:

- [Azure-portalen](sql-database-geo-replication-portal.md)
- [PowerShell: En databas](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Elastisk pool](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Enkel databas eller elastisk pool](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Enkel databas](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Aktiv georeplikering utnyttjar [Alltid på-tekniken för](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) SQL Server för att asynkront replikera genomförda transaktioner på den primära databasen till en sekundär databas med hjälp av ögonblicksbildisolering. Grupper med automatisk redundans ger gruppsenmantiken ovanpå aktiv georeplikering, men samma asynkrona replikeringsmekanism används. Även vid en viss punkt, den sekundära databasen kan vara något bakom den primära databasen, är de sekundära data garanterat att aldrig ha partiella transaktioner. Redundans över flera regioner gör det möjligt för program att snabbt återställa från en permanent förlust av ett helt datacenter eller delar av ett datacenter som orsakas av naturkatastrofer, katastrofala mänskliga fel eller skadliga handlingar. De specifika RPO-data finns på [Översikt över affärskontinuitet](sql-database-business-continuity.md).

> [!NOTE]
> Om det finns ett nätverksfel mellan två regioner försöker vi igen var tionde sekund för att återupprätta anslutningar.
> [!IMPORTANT]
> Om du vill garantera att en kritisk ändring i den primära databasen replikeras till en sekundär innan du redundans kan du tvinga synkroniseringen för att säkerställa replikering av kritiska ändringar (till exempel lösenordsuppdateringar). Tvingad synkronisering påverkar prestanda eftersom den blockerar anropande tråden tills alla genomförda transaktioner replikeras. Mer information finns [i sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Information om hur du övervakar fördröjningen mellan den primära databasen och den geografiska sekundären finns [i sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Följande bild visar ett exempel på aktiv geo-replikering konfigurerad med en primär i regionen Norra centrala USA och sekundär i regionen Södra centrala USA.

![geo-replikeringsrelation](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Eftersom de sekundära databaserna är läsbara kan de användas för att avlasta skrivskyddade arbetsbelastningar, till exempel rapportera jobb. Om du använder aktiv geo-replikering är det möjligt att skapa den sekundära databasen i samma region med den primära, men det ökar inte programmets motståndskraft mot katastrofala fel. Om du använder grupper för automatisk redundans skapas alltid den sekundära databasen i en annan region.

Förutom haveriberedskap aktiv geo-replikering kan användas i följande scenarier:

- **Databasmigrering:** Du kan använda aktiv geo-replikering för att migrera en databas från en server till en annan online med minsta möjliga stilleståndstid.
- **Programuppgraderingar:** Du kan skapa en extra sekundär som en återställningskopia under programuppgraderingar.

För att uppnå verklig affärskontinuitet är det bara en del av lösningen att lägga till databasredundans mellan datacenter. Återställa ett program (tjänst) end-to-end efter ett oåterkalleligt fel kräver återställning av alla komponenter som utgör tjänsten och eventuella beroende tjänster. Exempel på dessa komponenter är klientprogramvaran (till exempel en webbläsare med ett anpassat JavaScript), webbklientänd, lagring och DNS. Det är viktigt att alla komponenter är motståndskraftiga mot samma fel och blir tillgängliga inom återställningstidsmålet (RTO) för ditt program. Därför måste du identifiera alla beroende tjänster och förstå de garantier och funktioner de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att säkerställa att din tjänst fungerar under redundansen av de tjänster som den är beroende av. Mer information om hur du utformar lösningar för haveriberedskap finns i [Utforma molnlösningar för haveriberedskap Med aktiv georeplikering](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktiv terminologi och funktioner för georeplikering

- **Automatisk asynkron replikering**

  Du kan bara skapa en sekundär databas genom att lägga till i en befintlig databas. Den sekundära kan skapas i alla Azure SQL Database-servrar. När den sekundära databasen har skapats fylls den med data som kopierats från den primära databasen. Denna process kallas sådd. När sekundär databas har skapats och dirigerats replikeras uppdateringar till den primära databasen asynkront till den sekundära databasen automatiskt. Asynkron replikering innebär att transaktioner genomförs i den primära databasen innan de replikeras till den sekundära databasen.

- **Läsbara sekundära databaser**

  Ett program kan komma åt en sekundär databas för skrivskyddade åtgärder med samma eller olika säkerhetsobjekt som används för åtkomst till den primära databasen. De sekundära databaserna fungerar i isoleringsläge för ögonblicksbilder för att säkerställa att replikering av uppdateringarna av den primära (logguppspelningen) inte försenas av frågor som körs på sekundärt.

> [!NOTE]
> Logguppspelningen fördröjs i den sekundära databasen om det finns schemauppdateringar på primärt. Det senare kräver ett schemalås på den sekundära databasen.
> [!IMPORTANT]
> Du kan använda georeplikering för att skapa en sekundär databas i samma region som den primära. Du kan använda den här sekundära för att läsa upp en skrivskyddad arbetsbelastning i samma region. En sekundär databas i samma region ger dock inte ytterligare felåterhämtning och är därför inte ett lämpligt redundansmål för haveriberedskap. Det kommer inte heller att garantera isolering av tillgänglighetszoner. Använd Affärskritisk eller Premium-tjänstnivå med [zon redundant konfiguration](sql-database-high-availability.md#zone-redundant-configuration) för att uppnå isolering av tillgänglighetszon.   
>

- **Planerad redundans**

  Planerad redundans växlar rollerna för primära och sekundära databaser när den fullständiga synkroniseringen har slutförts. Det är en online-åtgärd som inte leder till dataförlust. Tiden för åtgärden beror på storleken på transaktionsloggen på den primära som måste synkroniseras. Planerad redundans är utformad för följande scenarier: (a) för att utföra DR-borrar i produktion när dataförlusten inte är acceptabel; b) att flytta databasen till en annan region, och (c) för att returnera databasen till den primära regionen efter att avbrottet har mildrats (återställning av fel).

- **Oplanerad redundans**

  Oplanerad eller påtvingad redundans växlar omedelbart sekundärt till den primära rollen utan synkronisering med den primära. Alla transaktioner som har gjorts till den primära men inte replikerade till den sekundära kommer att gå förlorade. Den här åtgärden är utformad som en återställningsmetod vid avbrott när den primära inte är tillgänglig, men databastillgängligheten måste snabbt återställas. När den ursprungliga primär är online igen kommer den automatiskt att åter anslutas och bli en ny sekundär. Alla osynkroniserade transaktioner innan redundansen bevaras i säkerhetskopian men synkroniseras inte med den nya primärfilen för att undvika konflikter. Dessa transaktioner måste sammanfogas manuellt med den senaste versionen av den primära databasen.
 
- **Flera läsbara sekundärer**

  Upp till 4 sekundära databaser kan skapas för varje primär. Om det bara finns en sekundär databas och den misslyckas, exponeras programmet för högre risk tills en ny sekundär databas skapas. Om det finns flera sekundära databaser förblir programmet skyddat även om en av de sekundära databaserna misslyckas. De extra sekundärerna kan också användas för att skala ut skrivskyddade arbetsbelastningar

  > [!NOTE]
  > Om du använder aktiv geo-replikering för att skapa ett globalt distribuerat program och behöver ge skrivskyddad åtkomst till data i fler än fyra regioner, kan du skapa sekundärt av en sekundär (en process som kallas kedja). På så sätt kan du uppnå praktiskt taget obegränsad skala av databasreplikering. Dessutom minskar kedjekopplingen omkostnaderna för replikeringen från den primära databasen. Avvägningen är den ökade replikeringsfördröjningen på de mest sekundära databaserna.

- **Georeplikering av databaser i en elastisk pool**

  Varje sekundär databas kan separat delta i en elastisk pool eller inte vara i någon elastisk pool alls. Poolvalet för varje sekundär databas är separat och beror inte på konfigurationen av någon annan sekundär databas (vare sig den är primär eller sekundär). Varje elastisk pool finns i en enda region, därför kan flera sekundära databaser i samma topologi aldrig dela en elastisk pool.


- **Användarstyrd redundans och redundans**

  En sekundär databas kan uttryckligen växlas till den primära rollen när som helst av programmet eller användaren. Under ett verkligt avbrott bör alternativet "oplanerat" användas, vilket omedelbart främjar en sekundär för att vara den primära. När den misslyckade primära återställs och är tillgänglig igen, markerar systemet automatiskt den återställda primära som en sekundär och föra den uppdaterad med den nya primära. På grund av replikeringens asynkrona karaktär kan en liten mängd data gå förlorade under oplanerade redundans om en primär misslyckas innan den replikerar de senaste ändringarna i den sekundära. När en primär med flera sekundärer växlar över, konfigurerar systemet automatiskt om replikeringsrelationerna och länkar de återstående sekundärerna till den nyligen befordrade primärt utan att användaren behöver ingripa. När avbrottet som orsakade redundansen har mildrats kan det vara önskvärt att returnera programmet till den primära regionen. För att göra det bör redundanskommandot anropas med alternativet "planerad".

## <a name="preparing-secondary-database-for-failover"></a>Förbereda sekundär databas för redundans

Se till att programmet omedelbart kan komma åt den nya primära efter redundansen och kontrollera att autentiseringskraven för den sekundära servern och databasen är korrekt konfigurerade. Mer information finns i [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md). För att garantera efterlevnad efter redundans, se till att principen för lagring av säkerhetskopiering på den sekundära databasen matchar den primära. Dessa inställningar är inte en del av databasen och replikeras inte. Som standard konfigureras den sekundära med en standardbevarande period på sju dagar. Mer information finns i [automatiska säkerhetskopior i SQL Database](sql-database-automated-backups.md).

> [!IMPORTANT]
> Om databasen är medlem i en redundansgrupp kan du inte initiera redundansen med kommandot geo-replication faiover. Överväg att använda redundanskommandot för gruppen. Om du behöver redundans en enskild databas måste du först ta bort den från redundansgruppen. Mer information finns i [redundansgrupper.](sql-database-auto-failover-group.md) 


## <a name="configuring-secondary-database"></a>Konfigurera sekundär databas

Både primära och sekundära databaser måste ha samma tjänstnivå. Det rekommenderas också starkt att sekundär databas skapas med samma beräkningsstorlek (DTUs eller virtuella kärnor) som den primära. Om den primära databasen har en tung skrivarbetsbelastning kanske en sekundär med lägre beräkningsstorlek inte kan hålla jämna steg med den. Det kommer att orsaka redo eftersläpning på den sekundära, och potentiella otillgänglighet av den sekundära. För att minska dessa risker begränsar aktiv geo-replikering primärts transaktionslogghastighet om det behövs så att dess sekundärer kan komma ikapp. 

En annan konsekvens av en sekundär konfiguration med obalans är att programprestanda kan uppstå efter redundans på grund av otillräcklig beräkningskapacitet för den nya primärt. I så fall kommer det att bli nödvändigt att skala upp databastjänstmålet till den nödvändiga nivån, vilket kan ta betydande tid och beräkningsresurser, och kommer att kräva en redundans vid [hög tillgänglighet](sql-database-high-availability.md) i slutet av uppskalningsprocessen.

Om du bestämmer dig för att skapa den sekundära med lägre beräkningsstorlek, är logg-I/O-procentdiagrammet i Azure-portalen ett bra sätt att uppskatta den minimala beräkningsstorleken för den sekundära som krävs för att upprätthålla replikeringsbelastningen. Om din primära databas till exempel är P6 (1000 DTU) och dess loggskrivningsprocent är 50 %, måste den sekundära vara minst P4 (500 DTU). Om du vill hämta historiska logg-I/O-data använder du [vyn sys.resource_stats.](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) Om du vill hämta senaste loggskrivningsdata med högre granularitet som bättre återspeglar kortsiktiga toppar i logghastigheten använder [du vyn sys.dm_db_resource_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Begränsning av transaktionslogghastighet på primärt på grund av lägre beräkningsstorlek på en sekundär rapporteras med hjälp av HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO väntetyp, som visas i [databasvyerna sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) och [sys.dm_os_wait_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 

> [!NOTE]
> Transaktionslogghastigheten på den primära kan begränsas av skäl som inte är relaterade till lägre beräkningsstorlek på en sekundär. Den här typen av begränsning kan uppstå även om den sekundära har samma eller högre beräkningsstorlek än den primära. Mer information, inklusive väntetyper för olika typer av logghastighetsbegränsning, finns i Styrning av [transaktionsloggar.](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)

Mer information om beräkningsstorlekarna för SQL Database finns i [Vad är SQL Database Service-nivåer](sql-database-purchase-models.md).

## <a name="cross-subscription-geo-replication"></a>Georeplikering mellan prenumerationer

Om du vill konfigurera aktiv geo-replikering mellan två databaser som tillhör olika prenumerationer (oavsett om de är under samma klient eller inte) måste du följa den särskilda proceduren som beskrivs i det här avsnittet.  Proceduren är baserad på SQL-kommandon och kräver: 

- Skapa en privilegierad inloggning på båda servrarna
- Lägga till IP-adressen i listan över klienter som utför ändringen på båda servrarna (t.ex. IP-adressen för värden som kör SQL Server Management Studio). 

Klienten som utför ändringarna måste ha nätverksåtkomst till den primära servern. Även om samma IP-adress för klienten måste läggas till i listan över tillåt på den sekundära servern, krävs inte nätverksanslutning till den sekundära servern. 

### <a name="on-the-master-of-the-primary-server"></a>På huvudserverns huvudserver

1. Lägg till IP-adressen i listan över tillåten för klienten som utför ändringarna (mer information finns i [Konfigurera brandvägg](sql-database-firewall-configure.md)). 
1. Skapa en inloggning som är dedikerad för att konfigurera aktiv geo-replikering (och justera autentiseringsuppgifterna efter behov):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Skapa en motsvarande användare och tilldela den till dbmanager-rollen: 

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Notera SID för den nya inloggningen med den här frågan: 

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>I källdatabasen på den primära servern

1. Skapa en användare för samma inloggning:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Lägg till användaren i rollen db_owner:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>På befälhavaren på den sekundära servern 

1. Lägg till IP-adressen i listan över tillåtande av klienten som utför ändringarna. Det måste exakt samma IP-adress för den primära servern. 
1. Skapa samma inloggning som på den primära servern, med samma användarnamn lösenord, och SID: 

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Skapa en motsvarande användare och tilldela den till dbmanager-rollen:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>På huvudserverns huvudserver

1. Logga in på huvudservern med den nya inloggningen. 
1. Skapa en sekundär replik av källdatabasen på den sekundära servern (justera databasnamn och servernamn efter behov):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Efter den första installationen kan användare, inloggningar och brandväggsregler tas bort. 


## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Synkronisera autentiseringsuppgifter och brandväggsregler

Vi rekommenderar att du använder [IP-brandväggsregler på databasnivå](sql-database-firewall-configure.md) för geore replikerade databaser så att dessa regler kan replikeras med databasen för att säkerställa att alla sekundära databaser har samma IP-brandväggsregler som den primära. Den här metoden eliminerar behovet av kunder att manuellt konfigurera och underhålla brandväggsregler på servrar som är värdar för både primära och sekundära databaser. På samma sätt säkerställer att använda [innehållna databasanvändare](sql-database-manage-logins.md) för dataåtkomst både primära och sekundära databaser alltid har samma användaruppgifter, så under en redundans, finns det inga störningar på grund av felaktig matchning med inloggningar och lösenord. Med tillägg av [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)kan kunder hantera användaråtkomst till både primära och sekundära databaser och eliminera behovet av att hantera autentiseringsuppgifter i databaser helt och hållet.

## <a name="upgrading-or-downgrading-primary-database"></a>Uppgradera eller nedgradera primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräkningsstorlek (inom samma tjänstnivå, inte mellan allmänt syfte och affärskritisk) utan att koppla från sekundära databaser. Vid uppgradering rekommenderar vi att du uppgraderar den sekundära databasen först och sedan uppgraderar den primära. När du nedgraderar, återför ordningen: nedgradera den primära först och nedgradera sedan den sekundära. När du uppgraderar eller nedgraderar databasen till en annan tjänstnivå tillämpas den här rekommendationen.

> [!NOTE]
> Om du skapade sekundär databas som en del av redundansgruppskonfigurationen rekommenderas det inte att nedgradera den sekundära databasen. Detta för att säkerställa att din datanivå har tillräcklig kapacitet för att bearbeta din vanliga arbetsbelastning när redundans har aktiverats.

> [!IMPORTANT]
> Den primära databasen i en redundansgrupp kan inte skalas till en högre nivå om inte den sekundära databasen först skalas till den högre nivån. Om du försöker skala den primära databasen innan den sekundära databasen skalas kan följande felmeddelande visas:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av kritiska data

På grund av den höga svarstiden för stora nätverk använder kontinuerlig kopia en asynkron replikeringsmekanism. Asynkron replikering gör vissa dataförlust oundvikliga om ett fel inträffar. Vissa program kan dock inte kräva någon dataförlust. För att skydda dessa viktiga uppdateringar kan en programutvecklare anropa [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systemproceduren omedelbart efter att transaktionen har begåtts. Anrop **sp_wait_for_database_copy_sync** blockerar anropande tråden tills den senast genomförda transaktionen har överförts till den sekundära databasen. Det väntar dock inte på att de överförda transaktionerna ska spelas upp och genomföras på den sekundära. **sp_wait_for_database_copy_sync** begränsas till en specifik kontinuerlig kopieringslänk. Alla användare med anslutningsrättigheter till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> **sp_wait_for_database_copy_sync** förhindrar dataförlust efter redundans, men garanterar inte fullständig synkronisering för läsåtkomst. Fördröjningen som orsakas av ett **sp_wait_for_database_copy_sync** proceduranrop kan vara betydande och beror på transaktionsloggens storlek vid tidpunkten för samtalet.

## <a name="monitoring-geo-replication-lag"></a>Övervakning av fördröjning för georeplikation

Om du vill övervaka fördröjning med avseende på RPO använder *du replication_lag_sec* kolumn [i sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) i den primära databasen. Den visar fördröjning i sekunder mellan de transaktioner som genomförts på den primära och beständiga på den sekundära. T.ex. Om värdet på fördröjningen är 1 sekund betyder det att om den primära påverkas av ett avbrott just nu och redundans initieras, sparas inte en sekund av de senaste övergångarna. 

Om du vill mäta fördröjning med avseende på ändringar i den primära databasen som har tillämpats på den sekundära, dvs *last_commit.*

> [!NOTE]
> Ibland *har replication_lag_sec* på den primära databasen ett NULL-värde, vilket innebär att den primära för närvarande inte vet hur långt sekundärt är.   Detta händer vanligtvis efter omstart av processen och bör vara ett tillfälligt tillstånd. Överväg att meddela programmet om *replication_lag_sec* returnerar NULL under en längre tid. Det skulle tyda på att den sekundära databasen inte kan kommunicera med den primära på grund av ett permanent anslutningsfel. Det finns också villkor som kan orsaka skillnaden mellan *last_commit* tid på den sekundära och på den primära databasen att bli stor. T.ex. Om ett åtagande görs på den primära efter en lång period utan ändringar, kommer skillnaden att hoppa upp till ett stort värde innan du snabbt återvänder till 0. Se det som ett feltillstånd när skillnaden mellan dessa två värden fortfarande är stor under lång tid.


## <a name="programmatically-managing-active-geo-replication"></a>Programmässigt hantera aktiv geo-replikering

Som tidigare diskuterats kan aktiv geo-replikering också hanteras programmässigt med Azure PowerShell och REST API. I följande tabeller beskrivs den uppsättning kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning Azure Resource Manager API:er för hantering, inklusive [AZURE SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) och Azure [PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview). Dessa API:er kräver användning av resursgrupper och stöd för rollbaserad säkerhet (RBAC). Mer information om hur du implementerar åtkomstroller finns i [Azure Role-Based Access Control](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Hantera redundans för enstaka och poolade databaser

> [!IMPORTANT]
> Dessa Transact-SQL-kommandon gäller endast för aktiv geo-replikering och gäller inte för redundanskationsgrupper. De gäller därför inte heller hanterade instanser, eftersom de bara stöder redundansklar.

| Kommando | Beskrivning |
| --- | --- |
| [ÄNDRA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Använd argumentet ADD SECONDARY ON SERVER för att skapa en sekundär databas för en befintlig databas och startar datareplikering |
| [ÄNDRA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Använd REDUNDANS ELLER FORCE_FAILOVER_ALLOW_DATA_LOSS för att växla en sekundär databas till primär för att initiera redundans |
| [ÄNDRA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Använd REMOVE SECONDARY ON SERVER för att avsluta en datareplikering mellan en SQL-databas och den angivna sekundära databasen. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Returnerar information om alla befintliga replikeringslänkar för varje databas på Azure SQL Database-servern. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Hämtar den senaste replikeringstiden, den senaste replikeringsfördröjningen och annan information om replikeringslänken för en viss SQL-databas. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Visar status för alla databasåtgärder, inklusive replikeringslänkarnas status. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |gör att programmet väntar tills alla genomförda transaktioner replikeras och bekräftas av den aktiva sekundära databasen. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Hantera redundans för enstaka och poolade databaser

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

| Cmdlet | Beskrivning |
| --- | --- |
| [Hämta AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Hämtar en eller flera databaser. |
| [Ny-AzSqlDatabaseKonsekyr](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Skapar en sekundär databas för en befintlig databas och startar datareplikeringen. |
| [Set-AzSqlDatabaseSeksekary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Växlar en sekundär databas till att vara primär för att initiera redundans. |
| [Ta bort-AzSqlDatabaseSekyskondär](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Avslutar datareplikering mellan en SQL Database och den angivna sekundära databasen. |
| [Hämta-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Hämtar geo-replikeringslänkar mellan en Azure SQL-databas och en resursgrupp eller SQL Server. |
|  | |

> [!IMPORTANT]
> Exempelskript finns i [Konfigurera och redundans en enskild databas med aktiv georeplikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) och [Konfigurera och redundans en poolad databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: Hantera redundans för enskilda och poolade databaser

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera databas (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Skapar, uppdaterar eller återställer en primär eller en sekundär databas. |
| [Hämta status för Skapa eller uppdatera databas](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Returnerar statusen under en create-åtgärd. |
| [Ange sekundär databas som primär (planerad redundans)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Anger vilken sekundär databas som är primär genom att inte gå över från den aktuella primära databasen. **Det här alternativet stöds inte för hanterad instans.**|
| [Ange sekundär databas som primär (oplanerad redundans)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Anger vilken sekundär databas som är primär genom att inte gå över från den aktuella primära databasen. Den här åtgärden kan leda till dataförlust. **Det här alternativet stöds inte för hanterad instans.**|
| [Hämta replikeringslänk](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Hämtar en specifik replikeringslänk för en viss SQL-databas i ett partnerskap för georeplikering. Informationen som visas i katalogvyn sys.geo_replication_links hämtas. **Det här alternativet stöds inte för hanterad instans.**|
| [Replikeringslänkar - lista efter databas](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Hämtar alla replikeringslänkar för en viss SQL-databas i ett partnerskap för georeplikering. Informationen som visas i katalogvyn sys.geo_replication_links hämtas. |
| [Ta bort replikeringslänk](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Tar bort en databasreplikeringslänk. Det går inte att göra under redundans. |
|  | |

## <a name="next-steps"></a>Nästa steg

- Exempel på skript finns i:
  - [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurera och redundansväxla en pooldatabas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database stöder också grupper för automatisk redundans. Mer information finns i använda [grupper för automatisk redundans](sql-database-auto-failover-group.md).
- En översikt över affärskontinuitet och scenarier finns i [Översikt över affärskontinuitet](sql-database-business-continuity.md)
- Mer information om automatiska säkerhetskopior i Azure SQL Database finns i [automatiska säkerhetskopieringar i SQL Database](sql-database-automated-backups.md).
- Mer information om hur du använder automatiska säkerhetskopior för återställning finns i [Återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md).
- Mer information om autentiseringskrav för en ny primär server och databas finns i [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).
