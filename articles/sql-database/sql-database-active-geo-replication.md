---
title: Aktiv geo-replikering – Azure SQL Database | Microsoft Docs
description: Använda aktiv geo-replikering för att skapa läsbara sekundära databaser för enskilda databaser i samma eller olika datacenter (region).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/26/2019
ms.openlocfilehash: ca53f4bfa80d6fdead24dc7d562c2240bb3fa86d
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498493"
---
# <a name="creating-and-using-active-geo-replication"></a>Skapa och använda aktiv geo-replikering

Aktiv geo-replikering är Azure SQL Database-funktion som låter dig skapa läsbara sekundära databaser för enskilda databaser på en SQL Database-server i samma eller olika datacenter (region).

> [!NOTE]
> Aktiv geo-replikering stöds inte av hanterad instans. Geografisk redundans för hanterade instanser använder [automatisk redundans grupper](sql-database-auto-failover-group.md).

Aktiv geo-replikering är utformad som en företagslösning för verksamhetskontinuitet som tillåter programmet att utföra snabb haveriberedskap för enskilda databaser vid ett regionalt haveri eller avbrott i stor skala. Om geo-replikering är aktiverad, kan programmet initiera redundans till en sekundär databas i en annan Azure-region. Upp till fyra sekundära databaser stöds i samma eller olika regioner och de sekundära databaser kan också användas för skrivskyddad åtkomst frågor. Redundansväxlingen måste initieras manuellt av programmet eller användaren. Efter redundansväxlingen har den nya primärt slutpunkt för en annan anslutning. Följande diagram illustrerar en typisk konfiguration av ett geo-redundant molnprogram med aktiv geo-replikering.

![Aktiv geo-replikering](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database stöder även automatisk redundans grupper. Mer information finns i använda [automatisk redundans grupper](sql-database-auto-failover-group.md). Aktiv geo-replikering finns inte stöd för databaser som skapats i en hanterad instans. Överväg att använda [redundansgrupper](sql-database-auto-failover-group.md) med hanterade instanser.

Om du av någon anledning din primära databasen misslyckas, eller bara behöver kopplas från, kan du initiera redundans till någon av de sekundära databaserna. När redundans har aktiverats för en av de sekundära databaserna kan länkas automatiskt alla sekundära till den nya primärt.

Du kan hantera replikering och redundans för en individuell databas eller en uppsättning databaser på en server eller i en elastisk pool med aktiv geo-replikering. Du kan göra det med:

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: Enkel databas](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Elastisk pool](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Enkel databas eller elastisk pool](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST-API: Enkel databas](https://docs.microsoft.com/rest/api/sql/replicationlinks)

Kontrollera autentiseringskrav för din server och databas har konfigurerats på den nya primärt efter redundansväxlingen. Mer information finns i [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).

Aktiv geo-replikering utnyttjar den [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) teknik för SQL Server för att replikera asynkront genomförda transaktioner på den primära databasen till en sekundär databas med ögonblicksbildisolering. Automatisk redundans grupper ger gruppen semantik ovanpå aktiv geo-replikering, men samma asynkron replikeringsmekanism används. Den sekundära databasen inte är efter den primära databasen på en given tidpunkt, sekundära data är säkert att aldrig ha partiella transaktioner. Redundans över regioner gör det möjligt för program att snabbt återställa en förlust av ett helt datacenter eller delar av ett datacenter på grund av naturkatastrofer, oåterkalleligt mänskliga faktorn eller skadliga åtgärder. Specifika RPO data finns på [översikt över affärskontinuitet](sql-database-business-continuity.md).

> [!NOTE]
> Om det finns ett nätverksfel mellan två regioner, gör vi var tionde sekund för att återupprätta anslutningarna.
> [!IMPORTANT]
> Du kan tvinga synkroniseringen att se till att replikeringen av viktiga ändringar (till exempel, lösenordsuppdateringar) för att garantera att viktiga förändringar i den primära databasen replikeras till en sekundär före redundans. Framtvingad synkronisering påverkar prestanda eftersom blockeras anropstråden tills alla genomförda transaktioner replikeras. Mer information finns i [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Om du vill övervaka replikeringsfördröjning mellan den primära databasen och geo-secondary Se [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Följande bild visar ett exempel på aktiv geo-replikering konfigurerade med en primär i regionen USA, norra centrala och sekundära i regionen södra centrala USA.

![GEO-replikeringsrelation](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Eftersom den sekundära databasen är läsbar, kan de användas för att avlasta skrivskyddade arbetsbelastningar som rapportering av jobb. Om du använder aktiv geo-replikering, det är möjligt att skapa den sekundära databasen i samma region med primärt, men det ökar inte programmets återhämtning till kritiska fel. Om du använder automatisk redundans grupper skapas alltid din sekundära databas i en annan region.

Förutom disaster kan recovery aktiv geo-replikering användas i följande scenarier:

- **Databasmigrering**: Du kan använda aktiv geo-replikering för att migrera en databas från en server till en annan online med minimal stilleståndstid.
- **Programuppgraderingar**: Du kan skapa en extra sekundär som backend kopia misslyckas under programuppgraderingar.

För att uppnå verkliga affärskontinuitet, att lägga till databasredundans mellan Datacenter är bara en del av lösningen. Återställa ett program (tjänst) slutpunkt till slutpunkt efter att ett oåterkalleligt fel kräver återställning av alla komponenter som utgör tjänsten och alla beroende tjänster. Exempel på dessa komponenter är klientprogrammet (till exempel en webbläsare med en anpassad JavaScript), webbservergrupper, lagring och DNS. Det är viktigt att alla komponenter är elastisk vid samma fel och blir tillgängliga inom återställningstid (RTO) för ditt program. Därför måste du identifiera alla beroende tjänster och lära dig om garantier och funktioner som de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att se till att dina servicefunktioner under en redundansväxling av de tjänster som den är beroende av. Läs mer om utforma lösningar för haveriberedskap, [designa Molnlösningar för Disaster Recovery med aktiv geo-replikering](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktiv geo-replikering terminologi och funktioner

- **Automatisk asynkron replikering**

  Du kan bara skapa en sekundär databas genom att lägga till en befintlig databas. Sekundärt kan skapas i en Azure SQL Database-server. När du skapat fylls den sekundära databasen med de data som kopieras från den primära databasen. Den här processen kallas seeding. När sekundära databasen har skapats och angetts, replikeras uppdateringar till den primära databasen asynkront till den sekundära databasen automatiskt. Asynkron replikering innebär att transaktionerna sparas på den primära databasen innan de replikeras till den sekundära databasen.

- **Läsbara sekundära databaser**

  Ett program kan komma åt en sekundär databas för skrivskyddade åtgärder med hjälp av samma eller olika säkerhetsobjekt används för åtkomst till den primära databasen. Den sekundära databasen fungera i ögonblicksbilden isoleringsläge för att säkerställa att replikeringen av uppdateringar av primärt (log repetitionsattacker) inte försenas av frågor som körs på sekundärt.

> [!NOTE]
> Log-repetitionsattacker är försenad på den sekundära databasen om det finns schemauppdateringar på primärt. Det senare kräver ett schemalås på den sekundära databasen.
> [!IMPORTANT]
> Du kan använda geo-replikering för att skapa en sekundär databas i samma region som primärt. Du kan använda den här sekundära att belastningsutjämna en skrivskyddad arbetsbelastningar i samma region. En sekundär databas i samma region tillhandahåller inte ytterligare fel återhämtning och därför är inte en lämplig redundansmål för katastrofåterställning. Det garanterar även inte avaialability zon isolering. Använda affärskritisk eller Premium-tjänstnivån med [zonen redundant konfiguration](sql-database-high-availability.md#zone-redundant-configuration) att uppnå avaialability zon isolering.   
>

- **Planerad redundans**

  Planerad redundans utför fullständig synkronisering mellan primära och sekundära databaser innan de sekundära växlarna till den primära rollen. Detta garanterar att inga data går förlorade. Planerad redundans används följande scenarier: (a) du utför DR-tester i produktion när data går förlorade inte kan godkännas; (b) för att flytta databasen till en annan region; och (c) för att returnera databasen till den primära regionen när avbrottet har minimerats (återställning).

- **Oplanerad redundans**

  Oplanerad eller framtvingad redundansväxling växlar omedelbart sekundärt till den primära rollen utan någon synkronisering med primärt. Den här åtgärden leder till förlust av data. Oplanerad redundans används som en återställningsmetod för under avbrott när primärt inte är tillgänglig. När den ursprungliga primära databasen är online igen, kommer den automatiskt ansluta igen utan att synkronisera och bli en ny sekundär.

- **Flera läsbara sekundära databaser**

  Upp till 4 sekundära databaser kan skapas för varje primär. Om det finns bara en sekundär databas och misslyckas, visas programmet högre risk tills en ny sekundär databas skapas. Om det finns flera sekundära databaser, förblir programmet skyddat även om en av de sekundära databaserna misslyckas. Ytterligare sekundärservrar kan också användas för att skala ut skrivskyddade arbetsbelastningar

  > [!NOTE]
  > Om du använder aktiv geo-replikering och skapa ett globalt distribuerade program som du kan behöva ange skrivskyddad åtkomst till data i fler än fyra regioner, kan du skapa sekundära av en sekundär (en process som kallas länkning). På så sätt kan du uppnå praktiskt taget obegränsade skala för databasreplikering. Dessutom minskar länkning kostnaderna för replikeringen från den primära databasen. En kompromiss är ökad replikeringsfördröjning för löv – de flesta sekundära databaser.

- **GEO-replikering av databaser i en elastisk pool**

  Varje sekundär databas kan separat delta i en elastisk pool eller inte i någon elastisk pool alls. Pool-valmöjligheter för varje sekundär databas är separat och är inte beroende av konfigurationen av andra sekundära databasen (antingen primär eller sekundär). Varje elastisk pool ingår i en enda region, flera sekundära databaser i samma topologi kan därför aldrig delar en elastisk pool.

- **Konfigurerbara beräkningsstorleken för den sekundära databasen**

  Både den primära och sekundära databaser måste ha samma tjänstenivå. Det rekommenderas också starkt den sekundära databasen skapas med samma beräkningsstorleken (dtu: er eller v-kärnor) som primär. En sekundär med lägre beräkningsstorleken finns det risk för en ökad replikeringsfördröjning potentiella otillgängliga för sekundärt och därmed riskerar betydande dataförlust efter en redundansväxling. Resultatet blir publicerade Återställningspunktmålet = 5 SEK kan inte garanteras. Andra risken är att efter en redundansväxling programmets prestanda påverkas på grund av brist på beräkningskapaciteten för den nya primärt förrän den har uppgraderats till en högre beräkningsstorleken. Tiden för uppgraderingen beror på databasens storlek. Dessutom kan för närvarande sådana uppgradering kräver att både den primära och sekundära databaser är online och därför kan inte slutföras förrän driftstörningarna har minimerats. Om du vill skapa sekundärt med lägre beräkningsstorleken ger på logg-i/o procent diagrammet i Azure-portalen en bra sätt att beräkna minimal beräkningsstorleken för den sekundära som krävs för att upprätthålla belastningen för replikering. Exempel: om din primära databas är P6 (1000 DTU) och dess loggen IO-procent är 50% sekundär måste vara minst P4 (500 DTU). Du kan också hämta log-i/o-data med hjälp av [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) eller [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) databasen vyer.  Mer information om SQL Database-storlekar finns i [vad är SQL Database-servicenivåerna](sql-database-purchase-models.md).

- **Användarstyrd redundans och återställning efter fel**

  En sekundär databas kan uttryckligen växlas till den primära rollen när som helst av programmet eller användaren. Under ett avbrott på verkliga ska alternativet ”oplanerad” användas som främjar omedelbart en sekundär ska vara primärt. När den misslyckade primärt återställs och är tillgänglig igen, markerar den återställda primärt som en sekundär automatiskt i systemet och ta med det uppdaterade med den nya primärt. På grund av den asynkrona natur replikering, kan en liten mängd data förloras under oplanerad redundans om en primär misslyckas innan de senaste ändringarna replikeras till sekundärt. När en primär med flera sekundära databaser växlar, konfigurerar om replikeringens relationer automatiskt i systemet och länkar de återstående sekundära databaser till den nyligen uppgraderade primärt utan användarens ingripande. När avbrott som orsakade redundansen har mildras, kan det vara önskvärt att återställa programmet till den primära regionen. Om du vill göra det, bör redundanskommandot anropas med alternativet ”planerad”.

- **Synkronisera autentiseringsuppgifterna och brandväggsregler**

Vi rekommenderar att du använder [databasnivå IP-brandväggsregler](sql-database-firewall-configure.md) för geo-replikerade databaser så att de här reglerna kan replikeras med databasen för att se till att alla sekundära databaser har samma IP-brandväggsregler som primär. Den här metoden eliminerar behovet av att manuellt konfigurera och underhålla brandväggsregler på servrar som är värd för både de primära och sekundära databaserna. På samma sätt med hjälp av [innehöll databasanvändare](sql-database-manage-logins.md) för data access ser du till både primära och sekundära databaser har alltid samma autentiseringsuppgifter så att det finns inga avbrott på grund av överensstämmer inte med inloggningsnamn och lösenord under en redundansväxling. Med hjälp av [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), kunder kan hantera användarnas åtkomst till både primära och sekundära databaser och vilket eliminerar behovet av hantering av autentiseringsuppgifter i databaser helt och hållet.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uppgradera eller nedgradera en primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräkningsstorleken (inom samma tjänstenivå, inte mellan generell användning och affärskritisk) utan att koppla från alla sekundära databaser. När du uppgraderar, rekommenderar vi att du först uppgraderar den sekundära databasen och sedan uppgradera primärt. När du nedgraderar, i omvänd ordning: Nedgradera primärt först och sedan nedgraderar sekundär. När du uppgraderar eller nedgraderar databasen till en annan tjänstnivå, tillämpas den här rekommendationen.

> [!NOTE]
> Om du har skapat sekundär databas som en del av konfigurationen av redundans-grupp inte rekommenderas att nedgradera den sekundära databasen. Det här är att säkerställa att din datanivå har tillräckligt med kapacitet för att bearbeta din vanliga arbetsbelastning när redundans har aktiverats.

> [!IMPORTANT]
> Den primära databasen i en redundansgrupp skala inte till en högre nivå, såvida inte den sekundära databasen först desto högre nivå. Om du försöker skala den primära databasen innan den sekundära databasen skalas, kan du få följande fel:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av viktiga data

Kontinuerlig kopiering använder en asynkron replikeringsmekanism på grund av den lång svarstiden för WAN-nätverk. Asynkron replikering kan data gå förlorade oundvikligt med om ett fel inträffar. Vissa program kan dock kräva utan dataförlust. För att skydda viktiga uppdateringar, en programutvecklare kan anropa den [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) system proceduren omedelbart efter att transaktionen genomförs snabbare. Anropa **sp_wait_for_database_copy_sync** blockerar anropstråden tills den senast allokerade transaktionen har skickats till den sekundära databasen. Men väntar den inte överförda transaktionerna till återupprepas och verkställs på sekundärt. **sp_wait_for_database_copy_sync** är begränsad till en specifik kontinuerlig kopiering-länk. Alla användare med behörighet för anslutning till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> **sp_wait_for_database_copy_sync** förhindrar att data går förlorade efter växling vid fel, men garanterar inte fullständig synkronisering för läsåtkomst. Fördröjningen orsakas av en **sp_wait_for_database_copy_sync** proceduranropet kan vara betydande och beror på storleken på transaktionsloggen vid tidpunkten för anropet.

## <a name="monitoring-geo-replication-lag"></a>Övervakning av geo-replikeringsfördröjning

Du övervakar fördröjning med avseende på RPO med *replication_lag_sec* kolumn med [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) på den primära databasen. Den visar fördröjning i sekunder mellan transaktioner allokerats på primärt och sparats på sekundärt. T.ex. Om värdet för fördröjningen är 1 sekund, det innebär att om primärt påverkas av driftstörningar just nu och växling vid fel är intiated, 1 sekund av den senaste transtions sparas inte. 

För att mäta fördröjning med avseende på ändringar på den primära databasen som har tillämpats på sekundärt, d.v.s. som kan läsas från sekundärt jämför *last_commit* tiden på den sekundära databasen med samma värde på primärt -databasen.

> [!NOTE]
> Ibland *replication_lag_sec* har ett nullvärde, vilket innebär att primärt inte för närvarande vet hur långt sekundära har på den primära databasen.   Detta brukar inträffa efter processen startas om och bör vara ett övergående tillstånd. Överväg att aviseringar programmet om det *replication_lag_sec* returnerar NULL under en längre tid. Det visar att den sekundära databasen inte kan kommunicera med primärt på grund av ett permanent anslutningsfel. Det finns också villkor som kan orsaka skillnaden mellan *last_commit* tiden på sekundärt och på den primära databasen bli stor. T.ex. Om ett genomförande görs på primärt efter en lång tid utan ändringar, kommer skillnaden Hoppa upp till ett större värde innan snabbt returneras till 0. Överväg det ett feltillstånd när skillnaden mellan dessa två värden ligger stora under lång tid.


## <a name="programmatically-managing-active-geo-replication"></a>Programmässigt hantera aktiv geo-replikering

Enligt beskrivningen tidigare kan aktiv geo-replikering också hanteras via programmering med hjälp av Azure PowerShell och REST-API. I följande tabeller beskrivs uppsättningen kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning API: er för Azure Resource Manager för hantering, inklusive den [Azure SQL Database REST API: et](https://docs.microsoft.com/rest/api/sql/) och [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview). Dessa API: er kräver användning av resursgrupper och stöder rollbaserad säkerhet (RBAC). Mer information om hur du implementerar åtkomst roller finns i [rollbaserad åtkomstkontroll i](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Hantera redundans för enkel och delade databaser

> [!IMPORTANT]
> De här Transact-SQL-kommandon bara gäller aktiv geo-replikering och gäller inte för redundansgrupper. Därför måste gäller de även inte för hanterade instanser eftersom de endast stöder redundansgrupper.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Använd Lägg till sekundär på SERVER-argumentet för att skapa en sekundär databas för en befintlig databas och startar datareplikeringen |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Använd växling vid fel eller FORCE_FAILOVER_ALLOW_DATA_LOSS för att växla en sekundär databas till att vara primär för att initiera redundans |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Använd ta bort sekundära på servern för att avsluta en replikering av data mellan en SQL-databas och den angivna sekundära databasen. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Returnerar information om alla befintliga replikeringslänkar för varje databas på Azure SQL Database-servern. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Hämtar senaste replikeringstid, senaste replikeringsfördröjning och annan information om replikeringslänken för en viss SQL-databas. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Visar status för alla databasoperationer, inklusive statusen för länkar för databasreplikering. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |Gör så att programmet ska vänta tills alla genomförda transaktioner replikeras och bekräftas av den aktiva sekundära databasen. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Hantera redundans för enkel och delade databaser

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

| Cmdlet | Beskrivning |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Hämtar en eller flera databaser. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Skapar en sekundär databas för en befintlig databas och startar datareplikeringen. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Växlar en sekundär databas till att vara primär för att initiera redundans. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Avslutar datareplikering mellan en SQL Database och den angivna sekundära databasen. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Hämtar geo-replikeringslänkar mellan en Azure SQL Database och en resursgrupp eller SQL Server. |
|  | |

> [!IMPORTANT]
> Exempel på skript, se [konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) och [konfigurera och redundansväxla en databas i pool med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST-API: Hantera redundans för enkel och delade databaser

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera databas (createMode = återställer)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Skapar, uppdaterar eller återställer en primär eller sekundär databas. |
| [Hämta skapa eller uppdatera Database-Status](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Returnerar status under en åtgärd för att skapa. |
| [Ange Sekundär databas som primär (planerad redundans)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Anger vilka sekundära databasen är primär genom växling från den aktuella primära databasen. **Det här alternativet stöds inte för hanterad instans.**|
| [Ange Sekundär databas som primär (oplanerad redundans)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Anger vilka sekundära databasen är primär genom växling från den aktuella primära databasen. Den här åtgärden kan resultera i dataförlust. **Det här alternativet stöds inte för hanterad instans.**|
| [Hämta replikeringslänk](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Hämtar en replikeringslänk är specifika för en viss SQL-databas i en koppling för geo-replikering. Den hämtar information visas i katalogvyn sys.geo_replication_links. **Det här alternativet stöds inte för hanterad instans.**|
| [Replikeringslänkar - listan efter databas](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Hämtar alla länkar för databasreplikering för en viss SQL-databas i en koppling för geo-replikering. Den hämtar information visas i katalogvyn sys.geo_replication_links. |
| [Ta bort länk för Platsdatareplikering](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Tar bort en databasreplikeringslänk. Kan inte utföras under redundansväxlingen. |
|  | |

## <a name="next-steps"></a>Nästa steg

- Exempel på skript, finns här:
  - [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurera och redundansväxla en databas i pool med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database stöder även automatisk redundans grupper. Mer information finns i använda [automatisk redundans grupper](sql-database-auto-failover-group.md).
- En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md)
- Vill veta mer om Azure SQL Database automatiska säkerhetskopior, se [SQL Database automatiska säkerhetskopior](sql-database-automated-backups.md).
- Läs om hur du använder automatiska säkerhetskopieringar för återställning i [återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md).
- Mer information om autentiseringskrav för en ny primär server och databas, finns [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).
