---
title: Redundans grupper och aktiv geo-replikering – Azure SQL Database | Microsoft Docs
description: Använda grupper för automatisk redundans med aktiv geo-replikering och aktivera automatisk redundans vid ett eventuellt strömavbrott.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: c951791031b6304a26aadd434fa7336a9c7c474f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963155"
---
# <a name="overview-active-geo-replication-and-auto-failover-groups"></a>Översikt: Active geo-replikering och automatisk redundans-grupper

Aktiv geo-replikering är Azure SQL Database-funktion som låter dig skapa läsbara repliker av enskilda databaser i samma eller olika datacenter (region).

![Geo-replikering](./media/sql-database-geo-replication-failover-portal/geo-replication.png )

Aktiv geo-replikering är utformad som en företagslösning för verksamhetskontinuitet som tillåter programmet att utföra snabb haveriberedskap för enskilda databaser vid ett regionalt haveri eller avbrott i stor skala. Om geo-replikering är aktiverad, kan programmet initiera redundans till en sekundär databas i en annan Azure-region. Upp till fyra sekundära databaser stöds i samma eller olika regioner och de sekundära databaser kan också användas för skrivskyddad åtkomst frågor. Redundansväxlingen måste initieras manuellt av programmet eller användaren. Efter redundansväxlingen har den nya primärt slutpunkt för en annan anslutning.

> [!NOTE]
> Aktiv geo-replikering är tillgänglig för alla databaser på alla tjänstnivåer i alla regioner.
> Aktiv geo-replikering stöds inte för databaser som skapats i en hanterad instans. Överväg att använda [redundansgrupper](#auto-failover-group-capabilities) med hanterade instanser.

Automatisk redundans grupper är en utökning av aktiv geo-replikering. Den är utformad att hantera redundans för flera geo-replikerade databaser samtidigt med hjälp av program-initierad växling vid fel eller genom att delegera redundansväxling görs av tjänsten SQL Database baserat på användardefinierade kriterier. Det senare kan du återställa flera relaterade databaser i en sekundär region automatiskt efter ett oåterkalleligt fel eller oplanerad händelse som leder till fullständig eller partiell förlust av tillgänglighet för SQL Database-tjänsten i den primära regionen. Du kan dessutom använda läsbara sekundära databaser för att avlasta skrivskyddad frågearbetsbelastningar. Eftersom automatisk redundans grupper omfatta flera databaser, måste databaserna konfigureras på den primära servern. Både primära och sekundära servrar för databaser i redundansgruppen måste vara i samma prenumeration. Automatisk redundans grupper stöd för replikering av alla databaser i gruppen att endast en sekundär server i en annan region.

> [!NOTE]
> Använda aktiv geo-replikering om flera sekundära databaser krävs.
> [!IMPORTANT]
> Stöd för automatisk redundans för hanterade instanser i är allmänt tillgänglig förhandsversion.

Om du använder aktiv geo-replikering och för någon anledning din primära databasen misslyckas, eller bara behöver kopplas från, kan du initiera redundans till någon av de sekundära databaserna. När redundans har aktiverats för en av de sekundära databaserna kan länkas automatiskt alla sekundära till den nya primärt. Om du använder automatisk redundans grupper för att hantera databasåterställning och eventuella driftstopp som påverkar en eller flera databaser i gruppen resultaten i automatisk växling vid fel. Du kan konfigurera principen för automatisk redundans som bäst uppfyller dina programbehov eller du kan avanmäla dig och Använd manuell aktivering. Dessutom automatisk redundans grupper ger Läs-och och skrivskyddade lyssnare slutpunkter som förblir oförändrade under redundans. Oavsett om du använder manuell eller automatisk redundans aktivering växlar redundans alla sekundära databaser i gruppen till primär. När databasen redundansen är klar, uppdateras automatiskt DNS-posten för att omdirigera slutpunkterna till det nya området.

Du kan hantera replikering och redundans för en individuell databas eller en uppsättning databaser på en server eller i en elastisk pool med aktiv geo-replikering. Du kan göra det med:

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: Enkel databas](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Elastisk pool](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell: Redundansgrupp](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL: Enkel databas eller elastisk pool](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Enkel databas](https://docs.microsoft.com/rest/api/sql/replicationlinks)
- [REST API: Redundansgruppen](https://docs.microsoft.com/rest/api/sql/failovergroups).

Kontrollera autentiseringskrav för din server och databas har konfigurerats på den nya primärt efter redundansväxlingen. Mer information finns i [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md). Detta gäller både active geo-replikering och automatisk redundans-grupper.

Aktiv geo-replikering utnyttjar den [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) teknik för SQL Server för att replikera asynkront genomförda transaktioner på den primära databasen till en sekundär databas med ögonblicksbildisolering. Automatisk redundans grupper ger gruppen semantik ovanpå aktiv geo-replikering, men samma asynkron replikeringsmekanism används. Den sekundära databasen inte är efter den primära databasen på en given tidpunkt, sekundära data är säkert att aldrig ha partiella transaktioner. Redundans över regioner gör det möjligt för program att snabbt återställa en förlust av ett helt datacenter eller delar av ett datacenter på grund av naturkatastrofer, oåterkalleligt mänskliga faktorn eller skadliga åtgärder. Specifika RPO data finns på [översikt över affärskontinuitet](sql-database-business-continuity.md).

Följande bild visar ett exempel på aktiv geo-replikering konfigurerade med en primär i regionen USA, norra centrala och sekundära i regionen södra centrala USA.

![GEO-replikeringsrelation](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Eftersom den sekundära databasen är läsbar, kan de användas för att avlasta skrivskyddade arbetsbelastningar som rapportering av jobb. Om du använder aktiv geo-replikering, det är möjligt att skapa den sekundära databasen i samma region med primärt, men det ökar inte programmets återhämtning till kritiska fel. Om du använder automatisk redundans grupper skapas alltid din sekundära databas i en annan region.

Förutom disaster kan recovery aktiv geo-replikering användas i följande scenarier:

- **Databasmigrering**: du kan använda aktiv geo-replikering för att migrera en databas från en server till en annan online med minimal stilleståndstid.
- **Programuppgraderingar**: du kan skapa en extra sekundär som backend kopia misslyckas under programuppgraderingar.

För att uppnå verkliga affärskontinuitet, att lägga till databasredundans mellan Datacenter är bara en del av lösningen. Återställa ett program (tjänst) slutpunkt till slutpunkt efter att ett oåterkalleligt fel kräver återställning av alla komponenter som utgör tjänsten och alla beroende tjänster. Exempel på dessa komponenter är klientprogrammet (till exempel en webbläsare med en anpassad JavaScript), webbservergrupper, lagring och DNS. Det är viktigt att alla komponenter är elastisk vid samma fel och blir tillgängliga inom återställningstid (RTO) för ditt program. Därför måste du identifiera alla beroende tjänster och lära dig om garantier och funktioner som de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att se till att dina servicefunktioner under en redundansväxling av de tjänster som den är beroende av. Läs mer om utforma lösningar för haveriberedskap, [designa Molnlösningar för Disaster Recovery med aktiv geo-replikering](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Funktioner för aktiv geo-replikering

Aktiv geo-replikering-funktionen ger följande viktiga funktioner:

- **Automatisk asynkron replikering**

 Du kan bara skapa en sekundär databas genom att lägga till en befintlig databas. Sekundärt kan skapas i en Azure SQL Database-server. När du skapat fylls den sekundära databasen med de data som kopieras från den primära databasen. Den här processen kallas seeding. När sekundära databasen har skapats och angetts, replikeras uppdateringar till den primära databasen asynkront till den sekundära databasen automatiskt. Asynkron replikering innebär att transaktionerna sparas på den primära databasen innan de replikeras till den sekundära databasen.

- **Läsbara sekundära databaser**

  Ett program kan komma åt en sekundär databas för skrivskyddade åtgärder med hjälp av samma eller olika säkerhetsobjekt används för åtkomst till den primära databasen. Den sekundära databasen fungera i ögonblicksbilden isoleringsläge för att säkerställa att replikeringen av uppdateringar av primärt (log repetitionsattacker) inte försenas av frågor som körs på sekundärt.

  > [!NOTE]
  > Log-repetitionsattacker är försenad på den sekundära databasen om det finns schemauppdateringar på primärt. Det senare kräver ett schemalås på den sekundära databasen.

- **Flera läsbara sekundära databaser**

  Två eller flera sekundära databaser öka redundans och nivå av skydd för den primära databasen och programmet. Om det finns flera sekundära databaser, förblir programmet skyddat även om en av de sekundära databaserna misslyckas. Om det finns bara en sekundär databas och misslyckas, visas programmet högre risk tills en ny sekundär databas skapas.

  > [!NOTE]
  > Om du använder aktiv geo-replikering och skapa ett globalt distribuerade program som du kan behöva ange skrivskyddad åtkomst till data i fler än fyra regioner, kan du skapa sekundära av en sekundär (en process som kallas länkning). På så sätt kan du uppnå praktiskt taget obegränsade skala för databasreplikering. Dessutom minskar länkning kostnaderna för replikeringen från den primära databasen. En kompromiss är ökad replikeringsfördröjning för löv – de flesta sekundära databaser.

- **Stöd för databaser för elastisk pool**

  Varje replik kan separat delta i en elastisk Pool eller inte i någon elastisk pool alls. Pool-alternativ för varje replik är separat och är inte beroende av konfigurationen för andra replikeringar (antingen primär eller sekundär). Varje elastisk Pool ingår i en enda region, flera repliker i samma topologi kan därför aldrig dela en elastisk Pool.

- **Konfigurerbara beräkningsstorleken för den sekundära databasen**

  Både den primära och sekundära databaser måste ha samma tjänstenivå. Det rekommenderas också starkt den sekundära databasen skapas med samma beräkningsstorleken (dtu: er eller v-kärnor) som primär. En sekundär med lägre beräkningsstorleken finns det risk för en ökad replikeringsfördröjning potentiella otillgängliga för sekundärt och därmed riskerar betydande dataförlust efter en redundansväxling. Resultatet blir publicerade Återställningspunktmålet = 5 SEK kan inte garanteras. Andra risken är att efter en redundansväxling programmets prestanda påverkas på grund av brist på beräkningskapaciteten för den nya primärt förrän den har uppgraderats till en högre beräkningsstorleken. Tiden för uppgraderingen beror på databasens storlek. Dessutom kan för närvarande sådana uppgradering kräver att både den primära och sekundära databaser är online och därför kan inte slutföras förrän driftstörningarna har minimerats. Om du vill skapa sekundärt med lägre beräkningsstorleken ger på logg-i/o procent diagrammet i Azure-portalen en bra sätt att beräkna minimal beräkningsstorleken för den sekundära som krävs för att upprätthålla belastningen för replikering. Exempel: om din primära databas är P6 (1000 DTU) och dess loggen IO-procent är 50% sekundär måste vara minst P4 (500 DTU). Du kan också hämta log-i/o-data med hjälp av [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) eller [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) databasen vyer.  Mer information om SQL Database-storlekar finns i [vad är SQL Database-servicenivåerna](sql-database-service-tiers.md).

- **Användarstyrd redundans och återställning efter fel**

  En sekundär databas kan uttryckligen växlas till den primära rollen när som helst av programmet eller användaren. Under ett avbrott på verkliga ska alternativet ”oplanerad” användas som främjar omedelbart en sekundär ska vara primärt. När den misslyckade primärt återställs och är tillgänglig igen, markerar den återställda primärt som en sekundär automatiskt i systemet och ta med det uppdaterade med den nya primärt. På grund av den asynkrona natur replikering, kan en liten mängd data förloras under oplanerad redundans om en primär misslyckas innan de senaste ändringarna replikeras till sekundärt. När en primär med flera sekundära databaser växlar, konfigurerar om replikeringens relationer automatiskt i systemet och länkar de återstående sekundära databaser till den nyligen uppgraderade primärt utan användarens ingripande. När avbrott som orsakade redundansen har mildras, kan det vara önskvärt att återställa programmet till den primära regionen. Om du vill göra det, bör redundanskommandot anropas med alternativet ”planerad”.

- **Synkronisera autentiseringsuppgifterna och brandväggsregler**

  Vi rekommenderar att du använder [databasen brandväggsregler](sql-database-firewall-configure.md) för geo-replikerade databaser så att de här reglerna kan replikeras med databasen för att se till att alla sekundära databaser har samma brandväggsregler som primär. Den här metoden eliminerar behovet av att manuellt konfigurera och underhålla brandväggsregler på servrar som är värd för både de primära och sekundära databaserna. På samma sätt med hjälp av [innehöll databasanvändare](sql-database-manage-logins.md) för data access ser du till både primära och sekundära databaser har alltid samma autentiseringsuppgifter så att det finns inga avbrott på grund av överensstämmer inte med inloggningsnamn och lösenord under en redundansväxling. Med hjälp av [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), kunder kan hantera användarnas åtkomst till både primära och sekundära databaser och vilket eliminerar behovet av hantering av autentiseringsuppgifter i databaser helt och hållet.

## <a name="auto-failover-group-capabilities"></a>Funktioner för automatisk redundans-grupp

Automatisk redundans med funktionen ger en kraftfull abstraktion av aktiv geo-replikering genom att stödja grupp på replikering och automatisk redundans. Dessutom tas bort behovet av att ändra SQL-anslutningssträng efter redundans genom att tillhandahålla ytterligare lyssnare-slutpunkter.

> [!IMPORTANT]
> Hanterade instanser supportgrupper för automatisk redundans som en offentlig förhandsversion-funktioner. Se [bästa praxis att använda grupper för växling vid fel med hanterade instanser](#best-practices-of-using-failover-groups-with-managed-instances)

- **Redundansgrupp**

  En eller flera redundansgrupper kan skapas mellan två servrar i olika regioner (primära och sekundära servrar). Varje grupp kan innehålla en eller flera databaser som återställs som en enhet om alla eller vissa primära databaser blir otillgänglig på grund av ett avbrott i den primära regionen.  

- **Primär server**

  En server som är värd för primära databaser i redundansgruppen.

- **Sekundär server**

  En server som är värd för de sekundära databaserna i redundansgruppen. Den sekundära servern kan inte vara i samma region som den primära servern.

- **Lägga till databaser till redundansgrupp**

  Du kan placera flera databaser inom en server eller en elastisk pool i samma redundansgruppen. Om du lägger till en enskild databas i gruppen skapas automatiskt en sekundär databas med hjälp av samma version och beräkning storlek. Om den primära databasen är i en elastisk pool, skapas automatiskt sekundärt i den elastiska poolen med samma namn. Om du lägger till en databas som redan har en sekundär databas i den sekundära servern ärvs att geo-replikering av gruppen.

  > [!NOTE]
  > När du lägger till en databas som redan har en sekundär databas på en server som inte är en del av redundansgruppen, skapas en ny sekundär i den sekundära servern.
  > [!IMPORTANT]
  > I en hanterad instans replikeras alla användardatabaser. Du kan inte välja en delmängd av användardatabaser för replikering i redundansgruppen.

- **Redundans skrivskyddad lyssnare**

  En DNS CNAME-post formaterad som  **&lt;redundansgruppsnamnet-&gt;. database.windows.net** som pekar på den aktuella primära server-URL. Tillåter Läs-och SQL-program kan transparent återansluta till den primära databasen när primärt ändras efter redundans.

- **Redundans skrivskyddad lyssnare**

  En DNS CNAME-post formaterad som  **&lt;redundansgruppsnamnet-&gt;. secondary.database.windows.net** som pekar på den sekundära servern URL: en. Det gör att skrivskyddade SQL-programmen för transparent anslutning till den sekundära databasen med hjälp av de angivna reglerna för belastningsutjämning.

  > [!IMPORTANT]
  > När en redundansgrupp skapas på en hanterad instans, DNS CNAME poster för lyssnarna är **&lt;redundansgruppsnamnet-&gt;.&lt; zone_id&gt;. database.windows.net och &lt;redundansgruppsnamnet-&gt;.secondary.&lt; zone_id&gt;. database.windows.net. Se [bästa praxis för redundansgrupper med hanterade instanser](#best-practices-of-using-failover-groups-for-business-continuity-with-managed-instances) för hur du använder zone_id med hanterad instans.

- **Princip för automatisk redundans**

  Som standard har i redundansgruppen konfigurerats med en princip för automatisk redundans. Systemet utlöser redundans när felet upptäcks och respittiden har upphört att gälla. Systemet måste kontrollera att driftstörningarna inte kan hanteras av infrastrukturen inbyggd hög tillgänglighet i SQL Database-tjänsten på grund av skalan för effekten. Om du vill styra redundans arbetsflödet från programmet kan du inaktivera automatisk växling vid fel.

- **Skrivskyddad redundansprincip**

  Redundans för den skrivskyddade lyssnaren är inaktiverad som standard. Det innebär att prestandan för primärt inte påverkas när sekundärt är offline. Men innebär det också skrivskyddade sessioner inte kommer att kunna ansluta förrän sekundärt återställs. Om du inte tolererar driftstopp för readonly-sessioner och är OK att tillfälligt använda primärt för både skrivskyddad och Skriv-trafik på bekostnad av potentiellt prestandaförsämring på primärt, kan du aktivera redundans för den skrivskyddade lyssnaren. I så fall omdirigeras skrivskyddad trafiken automatiskt till den primära servern om den sekundära servern inte är tillgänglig.  

- **Manuell redundans**

  Du kan initiera redundans manuellt när som helst oavsett automatisk redundans-konfiguration. Om automatisk redundans principen inte är konfigurerad, krävs manuell redundans för att återställa databaser i redundansgruppen. Du kan initiera framtvingad eller eget redundans (med fullständig synkronisering). Det senare kan användas för att flytta den aktiva servern till den primära regionen. När redundansväxlingen är klar, uppdateras automatiskt DNS-poster för att kontrollera nätverksanslutningen till rätt server.

- **Respitperiod med dataförlust**

  Eftersom de primära och sekundära databaserna synkroniseras med asynkron replikering, kan redundans resultera i dataförlust. Du kan anpassa principen för automatisk redundans så att den återspeglar ditt programs tolerans för dataförlust. Genom att konfigurera **lade**, du kan styra hur länge systemet ska vänta innan du påbörjar redundans som troligen kommer att resultatet data går förlorade.

  > [!NOTE]
  > När systemet identifierar att databaserna i gruppen är fortfarande online (till exempel driftstörningarna endast påverkas kontrollplanet service), redundans med Fullständig datasynkronisering (eget redundans) aktiveras omedelbart oavsett vilket värde som angetts av  **Lade**. Det här beteendet garanteras att det finns inga data går förlorade under återställningen. Respittiden gäller endast när ett eget redundans inte är möjligt. Om avbrottet har mildras innan respittiden upphör att gälla, aktiveras inte växling vid fel.

- **Flera redundansgrupper**

  Du kan konfigurera flera redundansgrupper för samma par av servrar för att styra skalan för redundans. Varje grupp växlas över oberoende av varandra. Om ditt program med flera innehavare använder elastiska pooler, kan du använda den här funktionen för att blanda primära och sekundära databaser i varje pool. Det här sättet du kan minska effekten av ett avbrott till endast hälften av klienterna.

  > [!IMPORTANT]
  > Hanterad instans stöder inte flera grupper för växling vid fel.

## <a name="best-practices-of-using-failover-groups-with-single-and-pooled-databases"></a>Bästa praxis att använda grupper för växling vid fel med enkel och delade databaser

Följ dessa allmänna riktlinjer när du utformar en tjänst med verksamhetskontinuitet i åtanke:

- **Använda en eller flera grupper för växling vid fel för att hantera redundans för flera databaser**

  En eller flera redundansgrupper kan skapas mellan två servrar i olika regioner (primära och sekundära servrar). Varje grupp kan innehålla en eller flera databaser som återställs som en enhet om alla eller vissa primära databaser blir otillgänglig på grund av ett avbrott i den primära regionen. Redundansgruppen skapar geo-sekundär databas med samma tjänstmålet som primär. Om du lägger till en befintlig relation för geo-replikering i redundansgruppen, kontrollera att geo-secondary konfigureras med samma tjänstenivå och beräkningsstorleken som primär.

- **Använd Skriv-lyssnare för OLTP-arbetsbelastning**

  När du utför åtgärder för OLTP, använda  **&lt;redundansgruppsnamnet-&gt;. database.windows.net** som servern URL och anslutningarna automatiskt dirigeras till primärt. Denna URL ändras inte efter redundansen. Obs växling vid fel innebär att uppdatera DNS-posten så klientanslutningarna omdirigeras till den nya primärt först när klienten DNS-cacheminnet har uppdaterats.

- **Använd skrivskyddade lyssnare för skrivskyddade arbetsbelastningar**

  Om du har en logiskt avskild skrivskyddad arbetsbelastning som är tolerant för vissa föråldring av data kan använda du den sekundära databasen i programmet. Skrivskyddad sessioner, använda  **&lt;redundansgruppsnamnet-&gt;. secondary.database.windows.net** som servern URL och anslutningen automatiskt dirigeras till sekundärt. Vi rekommenderar också att du anger i anslutningssträngen läsa avsikten med hjälp av **ApplicationIntent = ReadOnly**.

- **Förberedas för perf försämring**

  SQL redundans beslut är fristående från resten av programmet eller andra tjänster som används. Programmet får ”blandas” med vissa komponenter i en region och vissa i en annan. Om du vill undvika försämringen, se till att redundanta programdistributionen i regionen för Haveriberedskap och följer du dessa [network säkerhetsriktlinjer](#failover-groups-and-network-security).

  > [!NOTE]
  > Program i regionen för Haveriberedskap behöver inte använda en annan anslutningssträng.  

- **Förbereda för förlust av data**

  Om ett avbrott identifieras SQL väntar tills den period som du angett med **lade**. Standardvärdet är 1 timme. Om du inte har råd förlust av data, se till att ange **lade** till tillräckligt många, till exempel 24 timmar. Använd manuell redundansväxling för att återställa från sekundärt till primärt.

> [!IMPORTANT]
> Elastiska pooler med 800 eller färre dtu: er och fler än 250 databaser med hjälp av geo-replikering kan stöta på problem och längre planerade redundansväxlingar och sämre prestanda.  Dessa problem är mer sannolikt kan förekomma för skrivning krävande arbetsbelastningar ger när geo-replikering slutpunkter är långt från varandra efter geografi, eller när flera sekundära slutpunkter som används för varje databas.  Symtomen för de här problemen anges när geo-replikeringsfördröjning ökar med tiden.  Den här fördröjningen kan övervakas med [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Om de här problemen uppstår är åtgärder ökar antalet pool-dtu: er eller minska antalet geo-replikerade databaser i samma pool.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Bästa praxis att använda grupper för växling vid fel med hanterade instanser

Om ditt program använder hanterade instansen som datanivån, följer du dessa allmänna riktlinjer när du designar för kontinuitet för företag:

- **Skapa sekundär-instans i samma DNS-zon som den primära instansen**

  När en ny instans skapas, är ett unikt id automatiskt genereras som DNS-zonen och ingår i DNS-namnet på instansen. En flera domäner (SAN)-certifikatet för den här instansen har etablerats med SAN-fältet i form av &lt;zone_id&gt;. database.windows.net. Det här certifikatet kan användas för att autentisera klientanslutningar till en instans i samma DNS-zonen. För att säkerställa att icke-avbryts anslutningen till den primära instansen efter en redundansväxling både de primära och sekundära måste instanser vara i samma DNS-zonen. När programmet är redo för Produktionsdistribution, skapa en sekundär instans i en annan region och kontrollera att den delar DNS-zon med den primära instansen. Detta görs genom att ange en `DNS Zone Partner` valfri parameter med hjälp av Azure portal, PowerShell eller REST API.

- **Aktivera replikeringstrafik mellan två instanser**

  Eftersom varje instans isoleras i ett eget virtuellt nätverk, måste två enkelriktad trafik mellan de här virtuella nätverken tillåtas. Se [Azure VPN-gatewayen](../vpn-gateway/vpn-gateway-about-vpngateways.md).

- **Konfigurera en redundansgrupp för att hantera redundans för hela instans**

  Redundansgruppen att hantera redundans för alla databaser i instansen. När en grupp skapas kommer varje databas i instansen att geo-replikeras automatiskt till den sekundära instansen. Du kan inte använda redundansgrupper för att initiera en partiell redundans för en delmängd av databaserna.

  > [!IMPORTANT]
  > Om en databas tas bort från den primära instansen, tas den också bort automatiskt på den sekundära geo-instansen.

- **Använd Skriv-lyssnare för OLTP-arbetsbelastning**

  När du utför åtgärder för OLTP, använda &lt;redundansgruppsnamnet-&gt;.&lt; zone_id&gt;. database.windows.net URL: Serveradressen och anslutningar dirigeras automatiskt till primärt. Denna URL ändras inte efter redundansen. Växling vid fel innebär att uppdatera DNS-posten så klientanslutningarna omdirigeras till den nya primärt först när klienten DNS-cacheminnet har uppdaterats. Eftersom den sekundära instansen delar DNS-zon med primärt, kommer klientprogrammet att kunna återansluta till den med samma SAN-certifikat.

- **Ansluta direkt till geo-replikerad sekundär för skrivskyddade frågor**

  Om du har en logiskt avskild skrivskyddad arbetsbelastning som är tolerant för vissa föråldring av data kan använda du den sekundära databasen i programmet. Om du vill ansluta direkt till geo-replikerad sekundär &lt;server&gt;.secondary.&lt; zone_id&gt;. database.windows.net URL: Serveradressen och anslutningen görs direkt till geo-replikerad sekundär.

  > [!NOTE]
  > I vissa tjänstnivåer, Azure SQL Database stöder användning av [skrivskyddade repliker](sql-database-read-scale-out.md) att läsa in saldo skrivskyddat arbetsbelastningar med hjälp av kapaciteten för en skrivskyddad replik och använder den `ApplicationIntent=ReadOnly` parameter i anslutningen sträng. När du har konfigurerat en geo-replikerad sekundär, använder du den här funktionen för att ansluta till antingen en skrivskyddad replik på den primära platsen eller i geo-replikerade platsen.
  > - Om du vill ansluta till en skrivskyddad replik på den primära platsen, &lt;redundansgruppsnamnet-&gt;.&lt; zone_id&gt;. database.windows.net.
  > - Om du vill ansluta till en skrivskyddad replik på den primära platsen, &lt;redundansgruppsnamnet-&gt;.secondary.&lt; zone_id&gt;. database.windows.net.
- **Förberedas för perf försämring**

  SQL redundans beslut är fristående från resten av programmet eller andra tjänster som används. Programmet får ”blandas” med vissa komponenter i en region och vissa i en annan. Om du vill undvika försämringen, se till att redundanta programdistributionen i regionen för Haveriberedskap och följa riktlinjerna för nätverk i den här artikeln <link>.

- **Förbereda för förlust av data**

  Om ett avbrott identifieras utlöser SQL automatiskt läs-och växling vid fel om det finns ingen dataförlust till vår kännedom. I annat fall ska vänta tills den tid som du angav med lade. Om du har angett lade förberedas för förlust av data. I allmänhet prioriterar Azure under avbrott, tillgänglighet. Om du inte har råd förlust av data, se till att ange lade till tillräckligt många, till exempel 24 timmar.

> [!IMPORTANT]
> Använda manuell redundansväxling för att flytta USA: s presidentval tillbaka till den ursprungliga platsen: när avbrottet som orsakade redundansen har mildras, kan du flytta dina primära databaser till den ursprungliga platsen. För att göra det måste du initiera manuell växling vid fel i gruppen.

> [!IMPORTANT]
> DNS-uppdatering för Läs-och lyssnare sker omedelbart efter att redundansen initieras. Den här åtgärden ska inte resultera i dataförlust. Processen för att växla databasroller kan dock ta upp till 5 minuter under normala förhållanden. Tills den är klar, kan fortfarande vissa databaser i den nya primära instansen skrivskyddad. Om redundansväxling initieras med hjälp av PowerShell är hela åtgärden synkron. Om den har initierats med Azure-portalen visar Användargränssnittet slutförandestatus. Om den har initierats med hjälp av REST-API, kan du använda standard ARM avsökningsmekanism för att övervaka för slutförande.

## <a name="failover-groups-and-network-security"></a>Redundansgrupper och nätverkssäkerhet

För vissa program säkerhetsreglerna kräver att nätverksåtkomsten till datanivån är begränsad till en viss komponent eller komponenter, till exempel en virtuell dator, webbtjänsten osv. Det här kravet visas några utmaningar för företag affärskontinuitet design och användning av redundansgrupperna. När du implementerar sådana begränsad åtkomst bör du överväga följande alternativ.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Med redundansgrupper och virtuella Nätverksregler

Om du använder [tjänstslutpunkter i virtuella nätverk och regler](sql-database-vnet-service-endpoint-rule-overview.md) för att begränsa åtkomst till din SQL-databas, Tänk på att varje virtuellt nätverk tjänstslutpunkt gäller för endast en Azure-region. Slutpunkten kan inte andra regioner att godta kommunikation från undernätet. Därför kan bara de klientprogram som distribuerats i samma region ansluta till den primära databasen. Eftersom växling vid fel leder till SQL-klientsessioner som dirigeras om till en server i en annan (sekundär) region, misslyckas sessionerna om kommer från en klient utanför den regionen. Därför kan inte automatisk redundans principen aktiveras om deltagande servrar som ingår i de virtuella nätverksreglerna. Följ dessa steg för att stödja manuell växling vid fel:

1. Etablera redundanta kopior av frontend-komponenterna av ditt program (web service, VM etc.) i den sekundära regionen
2. Konfigurera den [virtuella Nätverksregler](sql-database-vnet-service-endpoint-rule-overview.md) individuellt för primära och sekundära servern
3. Aktivera den [frontend redundans med hjälp av en Traffic manager-konfigurationen](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Initiera manuell växling vid fel när avbrottet har identifierats. Det här alternativet är optimerad för program som kräver konsekvent fördröjning mellan klient och datanivå och har stöd för återställning när klientdel, datanivå eller båda som påverkas av felet.

> [!NOTE]
> Om du använder den **skrivskyddad lyssnare** att belastningsutjämna en skrivskyddad arbetsbelastning, se till att den här arbetsbelastningen körs i en virtuell dator eller annan resurs i den sekundära regionen så att den kan ansluta till den sekundära databasen.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Med redundansgrupper och SQL database-brandväggsregler

Om din affärskontinuitetsplan kräver redundans med hjälp av grupper med automatisk redundans, kan du begränsa åtkomsten till din SQL-databas med hjälp av de traditionella brandväggsreglerna.  Följ dessa steg för att stödja automatisk växling vid fel:

1. [Skapa en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Skapa en offentlig belastningsutjämnare](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) och tilldela offentliga IP-Adressen till den.
3. [Skapa ett virtuellt nätverk och de virtuella datorerna](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) för frontend-komponenterna
4. [Skapa nätverkssäkerhetsgrupp](../virtual-network/security-overview.md) och konfigurera inkommande anslutningar.
5. Se till att de utgående anslutningarna är öppen för Azure SQL-databas med hjälp av 'Sql' [servicetagg](../virtual-network/security-overview.md#service-tags).
6. Skapa en [brandväggsregel för SQL database](sql-database-firewall-configure.md) som tillåter inkommande trafik från den offentliga IP-adressen som du skapar i steg 1.

Mer information om hur du konfigurerar utgående åtkomst och vilka IP-adress till att använda i brandväggsreglerna Se [läsa in belastningsutjämnare utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md).

Konfigurationen ovan säkerställer att automatisk redundans inte blockerar anslutningar från frontend-komponenterna och förutsätter att programmet kan tolerera fel längre fördröjning mellan klienten och datanivå.

> [!IMPORTANT]
> För att garantera driftskontinuitet för regionala avbrott måste du kontrollera geografisk redundans för både frontend-komponenterna och databaserna.

## <a name="enabling-replication-between-managed-instances-and-their-vnets"></a>Att aktivera replikering mellan hanterade instanser och sina virtuella nätverk

När du ställer in en redundansgrupper mellan primära och sekundära hanterade instanser i två olika regioner är varje instans isolerad med hjälp av en oberoende VNET. Om du vill tillåta replikering trafik mellan dessa virtuella nätverk så här:

1. Ansluter dem via antingen Express Route-krets (https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings) eller en VPN-Gateway (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
2. Konfigurera varje NSG så att portarna 5022 och intervallet 11000 12000 är öppna för inkommande anslutningar från det andra virtuella nätverket.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uppgradera eller nedgradera en primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräkningsstorleken (inom samma tjänstenivå, inte mellan generell användning och affärskritisk) utan att koppla från alla sekundära databaser. När du uppgraderar, rekommenderar vi att du först uppgraderar den sekundära databasen och sedan uppgradera primärt. När du nedgraderar, i omvänd ordning: Nedgradera primärt först och sedan nedgraderar sekundär. När du uppgraderar eller nedgraderar databasen till en annan tjänstnivå, tillämpas den här rekommendationen.

> [!NOTE]
> Om du har skapat sekundär databas som en del av konfigurationen av redundans-grupp inte rekommenderas att nedgradera den sekundära databasen. Det här är att säkerställa att din datanivå har tillräckligt med kapacitet för att bearbeta din vanliga arbetsbelastning när redundans har aktiverats.

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av viktiga data

Kontinuerlig kopiering använder en asynkron replikeringsmekanism på grund av den lång svarstiden för WAN-nätverk. Asynkron replikering kan data gå förlorade oundvikligt med om ett fel inträffar. Vissa program kan dock kräva utan dataförlust. För att skydda viktiga uppdateringar, en programutvecklare kan anropa den [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) system proceduren omedelbart efter att transaktionen genomförs snabbare. Anropa **sp_wait_for_database_copy_sync** blockerar anropstråden tills den senast allokerade transaktionen har skickats till den sekundära databasen. Men väntar den inte överförda transaktionerna till återupprepas och verkställs på sekundärt. **sp_wait_for_database_copy_sync** är begränsad till en specifik kontinuerlig kopiering-länk. Alla användare med behörighet för anslutning till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> **sp_wait_for_database_copy_sync** förhindrar att data går förlorade efter växling vid fel, men garanterar inte fullständig synkronisering för läsåtkomst. Fördröjningen orsakas av en **sp_wait_for_database_copy_sync** proceduranropet kan vara betydande och beror på storleken på transaktionsloggen vid tidpunkten för anropet.

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Programmässigt hantera redundansgrupper och aktiv geo-replikering

Vilket beskrivs ovan, automatisk redundans grupper och aktiv kan geo-replikering också hanteras via programmering med hjälp av Azure PowerShell och REST-API. I följande tabeller beskrivs uppsättningen kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning API: er för Azure Resource Manager för hantering, inklusive den [Azure SQL Database REST API: et](https://docs.microsoft.com/rest/api/sql/) och [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview). Dessa API: er kräver användning av resursgrupper och stöder rollbaserad säkerhet (RBAC). Mer information om hur du implementerar åtkomst roller finns i [rollbaserad åtkomstkontroll i](../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover-using-transact-sql"></a>Hantera SQL database-redundans med hjälp av Transact-SQL

> [!IMPORTANT]
> De här Transact-SQL-kommandon bara gäller aktiv geo-replikering och gäller inte för redundansgrupper. Därför måste gäller de även inte för hanterade instanser eftersom de endast stöder redundansgrupper.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Använd Lägg till sekundär på SERVER-argumentet för att skapa en sekundär databas för en befintlig databas och startar datareplikeringen |
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Använd växling vid fel eller FORCE_FAILOVER_ALLOW_DATA_LOSS för att växla en sekundär databas till att vara primär för att initiera redundans |
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Använd ta bort sekundära på servern för att avsluta en replikering av data mellan en SQL-databas och den angivna sekundära databasen. |
| [sys.geo_replication_links (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Returnerar information om alla befintliga replikeringslänkar för varje databas på den logiska Azure SQL Database-servern. |
| [sys.dm_geo_replication_link_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Hämtar senaste replikeringstid, senaste replikeringsfördröjning och annan information om replikeringslänken för en viss SQL-databas. |
| [sys.dm_operation_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Visar status för alla databasoperationer, inklusive statusen för länkar för databasreplikering. |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |Gör så att programmet ska vänta tills alla genomförda transaktioner replikeras och bekräftas av den aktiva sekundära databasen. |
|  | |

### <a name="manage-sql-database-failover-using-powershell"></a>Hantera SQL database-redundans med hjälp av PowerShell

#### <a name="managing-failover-with-single-and-pooled-databases"></a>Hantera redundans med enkel och delade databaser

| Cmdlet | Beskrivning |
| --- | --- |
| [Lägg till AzureRmSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/add-azurermsqldatabasetofailovergroup)|Lägger till en eller flera databaser i en Azure SQL Database-redundansgrupp|
| [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase) |Hämtar en eller flera databaser. |
| [New-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Skapar en sekundär databas för en befintlig databas och startar datareplikeringen. |
| [Set-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Växlar en sekundär databas till att vara primär för att initiera redundans. |
| [Remove-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Avslutar datareplikering mellan en SQL Database och den angivna sekundära databasen. |
| [Get-AzureRmSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Hämtar geo-replikeringslänkar mellan en Azure SQL Database och en resursgrupp eller SQL Server. |
| [New-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära servrar|
| [Remove-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Tar bort redundansgruppen från servern och tar bort alla sekundära databaser ingår i gruppen |
| [Get-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Hämtar redundanskonfiguration för grupp |
| [Set-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |Ändrar konfigurationen för redundansgruppen |
| [Switch-AzureRMSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Utlösare redundans för redundansgruppen till den sekundära servern |
|  | |

> [!IMPORTANT]
> Exempel på skript, se [konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [konfigurera och redundansväxla en databas i pool med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md), och [ Konfigurera och gruppering av redundans redundans för en enskild databas](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

#### <a name="managing-failover-groups-with-managed-instances-preview"></a>Hantera redundansgrupper med hanterade instanser (förhandsversion)

##### <a name="install-the-newest-pre-release-version-of-powershell"></a>Installera den senaste versionen för tidig lansering av Powershell

1. Uppdatera modulen powershellget till 1.6.5 (eller senaste förhandsversionen). Se [plats för PowerShell-förhandsversion](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

   ```Powershell
      install-module powershellget -MinimumVersion 1.6.5 -force
   ```

2. Kör följande kommandon i ett nytt powershell-fönster:

   ```Powershell
      import-module powershellget
      get-module powershellget #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

##### <a name="prerequisites-before-setting-up"></a>Krav innan du konfigurerar

- Två hanterade instanser måste vara i olika geografiska områden.
- Din sekundära måste vara tom (inga användardatabaser).
- Den primära och sekundära hanterade instanser måste vara i samma resursgrupp.
- Virtuella nätverk som den hanterade instanser är en del av måste vara [är anslutna via en VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). VNet-Peering stöds inte.
- De två undernäten för hanterad instans kan inte ha överlappande IP-adresser.
- Du behöver konfigurera dina Nätverkssäkerhetsgrupper (NSG) sådana som portarna 5022 och intervallet 11000 ~ 12000 är öppna för anslutningar från det andra virtuella nätverket. Detta är att tillåta replikeringstrafik mellan instanser.
- Du måste konfigurera en DNS-zon och DNS-zon partner. En DNS-zon är en egenskap för en hanterad instans. Representerar en del av värdnamn som följer Managed Instance name och föregår det. database.windows.net suffix. Det genereras som slumpmässig sträng under genereringen av den första hanterade instansen i varje virtuellt nätverk. DNS-zon kan inte ändras efter skapandet av hanterade instansen och alla hanterade instanser i samma virtuella nätverk har samma värde för DNS-zon. För hanterade instans redundans inställning, måste den primära hanterade instansen och den sekundära hanterade instansen delar samma DBS zon. Det gör du genom att ange den `DnsZonePartner` parameter när du skapar den sekundära hanterade instansen. Egenskapen DNS-zon partner definierar den hanterade instansen om du vill dela en redundansgrupp för instansen med. Genom att skicka till resurs-id för en annan hanterad instans som indata för DnsZonePartner ärver samma DNS-zon värde för partnern hanterad instans i den hanterade instansen håller på att skapas.

##### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>PowerShell-kommandon för att skapa en redundansgrupp för instans

| API | Beskrivning |
| --- | --- |
| Ny AzureRmSqlDatabaseInstanceFailoverGroup |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära servrar|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |Ändrar konfigurationen för redundansgruppen|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |Hämtar redundanskonfiguration för grupp|
| Växeln AzureRmSqlDatabaseInstanceFailoverGroup |Utlösare redundans för redundansgruppen till den sekundära servern|
| Ta bort AzureRmSqlDatabaseInstanceFailoverGroup | Tar bort en redundansgrupp|

### <a name="manage-sql-database-failover-using-the-rest-api"></a>Hantera SQL database-redundans med hjälp av REST-API

#### <a name="managing-failover-with-single-and-pooled-databases-using-rest-api"></a>Hantera redundans med enkel och delade databaser med hjälp av REST API

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera databas (createMode = återställer)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Skapar, uppdaterar eller återställer en primär eller sekundär databas. |
| [Hämta skapa eller uppdatera Database-Status](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Returnerar status under en åtgärd för att skapa. |
| [Ange Sekundär databas som primär (planerad redundans)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Anger vilka replikdatabasen är primär genom växling från den aktuella primära replik-databasen. **Det här alternativet stöds inte för hanterad instans.**|
| [Ange Sekundär databas som primär (oplanerad redundans)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Anger vilka replikdatabasen är primär genom växling från den aktuella primära replik-databasen. Den här åtgärden kan resultera i dataförlust. **Det här alternativet stöds inte för hanterad instans.**|
| [Hämta replikeringslänk](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Hämtar en replikeringslänk är specifika för en viss SQL-databas i en koppling för geo-replikering. Den hämtar information visas i katalogvyn sys.geo_replication_links. **Det här alternativet stöds inte för hanterad instans.**|
| [Replikeringslänkar - listan efter databas](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Hämtar alla länkar för databasreplikering för en viss SQL-databas i en koppling för geo-replikering. Den hämtar information visas i katalogvyn sys.geo_replication_links. |
| [Ta bort länk för Platsdatareplikering](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Tar bort en databasreplikeringslänk. Kan inte utföras under redundansväxlingen. |
| [Skapa eller uppdatera redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Skapar eller uppdaterar en redundansgrupp |
| [Ta bort redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Tar bort redundansgruppen från servern |
| [Redundansväxling (planerad)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Flyttas över från den primära servern till den här servern. |
| [Påtvingad redundans tillåta förlust av Data](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails över från den primära servern till den här servern. Den här åtgärden kan resultera i dataförlust. |
| [Get-redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Hämtar en redundansgrupp. |
| [Lista Redundansgrupper av servern](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Listar redundansgrupperna på en server. |
| [Uppdatera redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Uppdaterar en redundansgrupp. |
|  | |

#### <a name="managing-failover-groups-with-managed-instances-using-rest-api-preview"></a>Hantera redundansgrupper med instanser som hanteras med hjälp av REST API (förhandsversion)

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundansgrupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Skapar eller uppdaterar en redundansgrupp |
| [Ta bort redundansgrupp](https://docs.microsoft.com/rest/api/instancefailovergroups/delete) | Tar bort redundansgruppen från servern |
| [Redundansväxling (planerad)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Flyttas över från den primära servern till den här servern. |
| [Påtvingad redundans tillåta förlust av Data](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |ails över från den primära servern till den här servern. Den här åtgärden kan resultera i dataförlust. |
| [Get-redundansgrupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Hämtar en redundansgrupp. |
| [Lista Redundansgrupper - lista efter plats](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Listar redundansgrupperna på en plats. |

## <a name="next-steps"></a>Nästa steg

- Exempel på skript, finns här:
  - [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurera och redundansväxla en databas i pool med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Konfigurera och redundansväxla en redundansgrupp för en enskild databas](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md)
- Vill veta mer om Azure SQL Database automatiska säkerhetskopior, se [SQL Database automatiska säkerhetskopior](sql-database-automated-backups.md).
- Läs om hur du använder automatiska säkerhetskopieringar för återställning i [återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md).
- Mer information om autentiseringskrav för en ny primär server och databas, finns [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).
