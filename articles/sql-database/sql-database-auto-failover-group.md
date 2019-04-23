---
title: Redundansgrupper – Azure SQL Database | Microsoft Docs
description: Automatisk redundans grupper är en SQL Database-funktion som gör det möjligt att hantera replikering och automatisk / samordnad redundans för en grupp med databaser på en SQL Database-server eller alla databaser i hanterad instans.
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
ms.date: 04/19/2019
ms.openlocfilehash: f382cc547640969f934b94405b635c9e84f10791
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009080"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Använda grupper för automatisk redundans för att aktivera transparent och samordnad redundans för flera databaser

Automatisk redundans grupper är en SQL Database-funktion som låter dig hantera replikering och redundans för en grupp med databaser på en SQL Database-server eller alla databaser i en hanterad instans till en annan region (för närvarande i förhandsversion för hanterad instans). Den använder samma underliggande teknik som [aktiv geo-replikering](sql-database-active-geo-replication.md). Du kan initiera redundans manuellt eller kan du delegera den till SQL Database-tjänsten baserat på en användardefinierad princip. Det senare alternativet kan du återställa flera relaterade databaser i en sekundär region automatiskt efter ett oåterkalleligt fel eller oplanerad händelse som leder till fullständig eller partiell förlust av tillgänglighet för SQL Database-tjänsten i den primära regionen. Du kan dessutom använda läsbara sekundära databaser för att avlasta skrivskyddad frågearbetsbelastningar. Eftersom automatisk redundans grupper omfatta flera databaser, måste databaserna konfigureras på den primära servern. Både primära och sekundära servrar för databaser i redundansgruppen måste vara i samma prenumeration. Automatisk redundans grupper stöd för replikering av alla databaser i gruppen att endast en sekundär server i en annan region.

> [!NOTE]
> När du arbetar med enkel eller grupperade databaser på en SQL Database-server och du vill att flera sekundära databaser i samma eller olika regioner, använda [aktiv geo-replikering](sql-database-active-geo-replication.md).

När du använder automatisk redundans grupper med automatisk redundansprincip eventuella driftstopp som påverkar en eller flera databaser i gruppen resultaten i automatisk växling vid fel. Dessutom automatisk redundans grupper ger Läs-och och skrivskyddade lyssnare slutpunkter som förblir oförändrade under redundans. Oavsett om du använder manuell eller automatisk redundans aktivering växlar redundans alla sekundära databaser i gruppen till primär. När databasen redundansen är klar, uppdateras automatiskt DNS-posten för att omdirigera slutpunkterna till det nya området. Specifika RPO och RTO data, se [översikt över affärskontinuitet](sql-database-business-continuity.md).

När du använder automatisk redundans grupper med automatisk redundansprincip eventuella driftstopp som påverkar databaser i SQL Database-server eller hanterad instans leder till automatisk växling vid fel. Du kan hantera med automatisk redundans:

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [PowerShell: Redundansgrupp](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [REST-API: Redundansgruppen](https://docs.microsoft.com/rest/api/sql/failovergroups).

Kontrollera autentiseringskrav för din server och databas har konfigurerats på den nya primärt efter redundansväxlingen. Mer information finns i [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).

För att uppnå verkliga affärskontinuitet, att lägga till databasredundans mellan Datacenter är bara en del av lösningen. Återställa ett program (tjänst) slutpunkt till slutpunkt efter att ett oåterkalleligt fel kräver återställning av alla komponenter som utgör tjänsten och alla beroende tjänster. Exempel på dessa komponenter är klientprogrammet (till exempel en webbläsare med en anpassad JavaScript), webbservergrupper, lagring och DNS. Det är viktigt att alla komponenter är elastisk vid samma fel och blir tillgängliga inom återställningstid (RTO) för ditt program. Därför måste du identifiera alla beroende tjänster och lära dig om garantier och funktioner som de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att se till att dina servicefunktioner under en redundansväxling av de tjänster som den är beroende av. Läs mer om utforma lösningar för haveriberedskap, [designa Molnlösningar för Disaster Recovery med aktiv geo-replikering](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Automatisk redundans grupp terminologi och funktioner

- **Redundansgruppen (DIMMA)**

  Redundansgruppen är en grupp med databaser som hanteras av en enda SQL Database-server eller i en hanterad instans som kan ha redundans som en enhet till en annan region om alla eller vissa primära databaser blir otillgänglig på grund av ett avbrott i den primära regionen.

  - **SQL Database-servrar**

     Med SQL Database-servrar, kan vissa eller alla användardatabaser på en enda SQL Database-server placeras i en redundansgrupp. Dessutom stöder SQL-databasserver flera grupper för växling vid fel på en enda SQL Database-server.

  - **Hanterade instanser**
  
     En redundansgrupp innehåller alla användardatabaser i den hanterade instansen med hanterad instans, och därför en hanterad instans stöder en enda redundansväxlingsgrupp endast.

- **Primär**

  SQL Database-server eller hanterad instans som är värd för primära databaser i redundansgruppen.

- **Sekundär**

  SQL Database-server eller hanterad instans som är värd för de sekundära databaserna i redundansgruppen. Sekundärt får inte vara i samma region som primärt.

- **Att lägga till enskilda databaser i redundansgruppen**

  Du kan placera flera enskilda databaser på samma SQL Database-server i samma redundansgruppen. Om du lägger till en enskild databas i redundansgruppen skapas automatiskt en sekundär databas med hjälp av samma version och beräkning storlek på den sekundära servern.  Du har angett den här servern i redundansgruppen har skapats. Om du lägger till en databas som redan har en sekundär databas i den sekundära servern ärvs länken geo-replikering av gruppen. När du lägger till en databas som redan har en sekundär databas på en server som inte är en del av redundansgruppen skapas en ny sekundär i den sekundära servern.
  
> [!IMPORTANT]
  > I en hanterad instans replikeras alla användardatabaser. Du kan inte välja en delmängd av användardatabaser för replikering i redundansgruppen.

- **Lägga till databaser i elastiska poolen till redundansgrupp**

  Du kan placera alla eller flera databaser inom en elastisk pool i samma redundansgruppen. Om den primära databasen är i en elastisk pool, skapas automatiskt sekundärt i den elastiska poolen med samma namn (sekundära pool). Du måste se till att den sekundära servern innehåller en elastisk pool med samma namn som exakt och tillräckligt med ledigt kapacitet som värd för de sekundära databaser som skapas av redundansgruppen. Om du lägger till en databas i poolen som redan har en sekundär databas i poolen för sekundär ärvs geo-replikering länken av gruppen. När du lägger till en databas som redan har en sekundär databas på en server som inte är en del av redundansgruppen skapas en ny sekundär i sekundära poolen.
  
  - **Redundans skrivskyddad lyssnare**

  En DNS CNAME-post formaterat som pekar på den aktuella primära URL: en. Tillåter Läs-och SQL-program kan transparent återansluta till den primära databasen när primärt ändras efter redundans.

  - **SQL Database-server DNS CNAME-post för Läs-och lyssnare**

     På en SQL Database-server, DNS CNAME-post för redundansgruppen som pekar på den aktuella primära URL: en har formaterats som `<fog-name>.database.windows.net`.

  - **Hanterad instans DNS CNAME-post för Läs-och lyssnare**

     På en hanterad instans DNS CNAME-post för redundansgruppen som pekar på den aktuella primära URL: en har formaterats som `<fog-name>.zone_id.database.windows.net`.

- **Redundans skrivskyddad lyssnare**

  En DNS CNAME-post formaterat som pekar på den skrivskyddade lyssnare som pekar på den sekundära URL: en. Det gör att skrivskyddade SQL-programmen för transparent anslutning till den sekundära med hjälp av de angivna reglerna för belastningsutjämning.

  - **SQL Database-server DNS CNAME-post för skrivskyddade lyssnare**

     På en SQL Database-server, DNS CNAME-post för den skrivskyddade lyssnare som pekar på den sekundära URL: en har formaterats som `'.secondary.database.windows.net`.

  - **Hanterad instans DNS CNAME-post för skrivskyddade lyssnare**

     På en hanterad instans DNS CNAME-post för den skrivskyddade lyssnare som pekar på den sekundära URL: en har formaterats som `<fog-name>.zone_id.database.windows.net`.

- **Princip för automatisk redundans**

  Som standard konfigureras en redundansgrupp med en princip för automatisk redundans. SQL Database-tjänsten utlöser redundans när felet upptäcks och respittiden har upphört att gälla. Systemet måste kontrollera att driftstörningarna inte kan hanteras av inbyggt [infrastruktur med hög tillgänglighet för SQL Database-tjänsten](sql-database-high-availability.md) på grund av skalan för effekten. Om du vill styra redundans arbetsflödet från programmet kan du inaktivera automatisk växling vid fel.

- **Skrivskyddad redundansprincip**

  Redundans för den skrivskyddade lyssnaren är inaktiverad som standard. Det innebär att prestandan för primärt inte påverkas när sekundärt är offline. Men innebär det också skrivskyddade sessioner inte kommer att kunna ansluta förrän sekundärt återställs. Om du inte tolererar driftstopp för skrivskyddade sessioner och är OK att tillfälligt använda primärt för både skrivskyddad och Skriv-trafik på bekostnad av potentiellt prestandaförsämring på primärt, kan du aktivera redundans för den skrivskyddade lyssnaren. I så fall omdirigeras skrivskyddad trafiken automatiskt till den primära servern om sekundärt inte är tillgänglig.

- **Planerad redundans**

   Planerad redundans utför fullständig synkronisering mellan primära och sekundära databaser innan de sekundära växlarna till den primära rollen. Detta garanterar att inga data går förlorade. Planerad redundans används i följande scenarier:

  - Utföra tester av haveriberedskap (DR) i produktion när data går förlorade inte kan godkännas
  - Flytta databaserna till en annan region
  - Returnera databaserna till den primära regionen när avbrottet har minimerats (återställning).

- **Oplanerad redundans**

   Oplanerad eller framtvingad redundansväxling växlar omedelbart sekundärt till den primära rollen utan någon synkronisering med primärt. Den här åtgärden leder till förlust av data. Oplanerad redundans används som en återställningsmetod för under avbrott när primärt inte är tillgänglig. När den ursprungliga primära databasen är online igen, kommer den automatiskt ansluta igen utan att synkronisera och bli en ny sekundär.

- **Manuell redundans**

  Du kan initiera redundans manuellt när som helst oavsett automatisk redundans-konfiguration. Om automatisk redundans principen inte är konfigurerad krävs manuell redundans för att återställa databaser i redundansgruppen till sekundärt. Du kan initiera framtvingad eller eget redundans (med fullständig synkronisering). Det senare kan användas för att flytta primärt till den sekundära regionen. När redundansväxlingen är klar, uppdateras automatiskt DNS-poster för att säkerställa anslutning till den nya primärt

- **Respitperiod med dataförlust**

  Eftersom de primära och sekundära databaserna synkroniseras med asynkron replikering, kan redundans resultera i dataförlust. Du kan anpassa principen för automatisk redundans så att den återspeglar ditt programs tolerans för dataförlust. Genom att konfigurera **lade**, du kan styra hur länge systemet ska vänta innan du påbörjar redundans som troligen kommer att resultatet data går förlorade.

- **Flera redundansgrupper**

  Du kan konfigurera flera redundansgrupper för samma par av servrar för att styra skalan för redundans. Varje grupp växlas över oberoende av varandra. Om ditt program med flera innehavare använder elastiska pooler, kan du använda den här funktionen för att blanda primära och sekundära databaser i varje pool. Det här sättet du kan minska effekten av ett avbrott till endast hälften av klienterna.

  > [!IMPORTANT]
  > Hanterad instans stöder inte flera grupper för växling vid fel.
  
## <a name="permissions"></a>Behörigheter
Behörigheter för en redundansgrupp hanteras via [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). Den [SQL Server-deltagare](../role-based-access-control/built-in-roles.md#sql-server-contributor) rollen har behörigheterna som är nödvändiga för att hantera redundansgrupper. 

### <a name="create-failover-group"></a>Skapa redundansgrupp
Om du vill skapa en redundansgrupp måste RBAC skrivåtkomst till båda primära och sekundära servrar och till alla databaser i redundansgruppen. Du behöver RBAC skrivåtkomst till både den primära och sekundära hanterad instansen för en hanterad instans, men behörigheter för enskilda databaser är inte relevant eftersom enskilda hanterade instansdatabaser inte kan läggas till eller tas bort från en redundansgrupp. 

### <a name="update-a-failover-group"></a>Uppdatera en redundansgrupp
Om du vill uppdatera en redundansgrupp måste RBAC skrivåtkomst till i redundansgruppen och alla databaser på den primära servern eller hanterad instans.  

### <a name="failover-a-failover-group"></a>Redundansväxla en redundansgrupp
Om du vill redundansväxla en redundansgrupp behöver RBAC skrivåtkomst till redundansgruppen på den nya primära servern eller hanterad instans. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Bästa praxis att använda grupper för växling vid fel med enskilda databaser och elastiska pooler

Gruppen automatisk redundans måste konfigureras på den primära servern för SQL-databas och ansluter den till den sekundära SQL Database-servern i en annan Azure-region.  Grupperna som kan inkludera alla eller vissa databaser i dessa servrar. Följande diagram illustrerar en typisk konfiguration av ett geo-redundant molnprogram med flera databaser och automatisk redundans grupp.

![automatisk redundans](./media/sql-database-auto-failover-group/auto-failover-group.png)

Följ dessa allmänna riktlinjer när du utformar en tjänst med verksamhetskontinuitet i åtanke:

- **Använda en eller flera grupper för växling vid fel för att hantera redundans för flera databaser**

  En eller flera redundansgrupper kan skapas mellan två servrar i olika regioner (primära och sekundära servrar). Varje grupp kan innehålla en eller flera databaser som återställs som en enhet om alla eller vissa primära databaser blir otillgänglig på grund av ett avbrott i den primära regionen. Redundansgruppen skapar geo-sekundär databas med samma tjänstmålet som primär. Om du lägger till en befintlig relation för geo-replikering i redundansgruppen, kontrollera att geo-secondary konfigureras med samma tjänstenivå och beräkningsstorleken som primär.

- **Använd Skriv-lyssnare för OLTP-arbetsbelastning**

  När du utför åtgärder för OLTP, använda `<fog-name>.database.windows.net` som servern URL och anslutningarna automatiskt dirigeras till primärt. Denna URL ändras inte efter redundansen. Obs växling vid fel innebär att uppdatera DNS-posten så klientanslutningarna omdirigeras till den nya primärt först när klienten DNS-cacheminnet har uppdaterats.

- **Använd skrivskyddade lyssnare för skrivskyddade arbetsbelastningar**

  Om du har en logiskt avskild skrivskyddad arbetsbelastning som är tolerant för vissa föråldring av data kan använda du den sekundära databasen i programmet. Skrivskyddad sessioner, använda `<fog-name>.secondary.database.windows.net` som servern URL och anslutningen automatiskt dirigeras till sekundärt. Vi rekommenderar också att du anger i anslutningssträngen läsa avsikten med hjälp av **ApplicationIntent = ReadOnly**.

- **Förberedas för perf försämring**

  SQL redundans beslut är fristående från resten av programmet eller andra tjänster som används. Programmet får ”blandas” med vissa komponenter i en region och vissa i en annan. Om du vill undvika försämringen, se till att redundanta programdistributionen i regionen för Haveriberedskap och följer du dessa [network säkerhetsriktlinjer](#failover-groups-and-network-security).

  > [!NOTE]
  > Program i regionen för Haveriberedskap behöver inte använda en annan anslutningssträng.  

- **Förbereda för förlust av data**

  Om ett avbrott identifieras SQL väntar tills den period som du angett med **lade**. Standardvärdet är 1 timme. Om du inte har råd förlust av data, se till att ange **lade** till tillräckligt många, till exempel 24 timmar. Använd manuell redundansväxling för att återställa från sekundärt till primärt.

> [!IMPORTANT]
> Elastiska pooler med 800 eller färre dtu: er och fler än 250 databaser med hjälp av geo-replikering kan stöta på problem och längre planerade redundansväxlingar och sämre prestanda.  Dessa problem är mer sannolikt kan förekomma för skrivning krävande arbetsbelastningar ger när geo-replikering slutpunkter är långt från varandra efter geografi, eller när flera sekundära slutpunkter som används för varje databas.  Symtomen för de här problemen anges när geo-replikeringsfördröjning ökar med tiden.  Den här fördröjningen kan övervakas med [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Om de här problemen uppstår är åtgärder ökar antalet pool-dtu: er eller minska antalet geo-replikerade databaser i samma pool.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Bästa praxis att använda grupper för växling vid fel med hanterade instanser

Gruppen automatisk redundans måste konfigureras på den primära instansen och ansluter den till den sekundära instansen i en annan Azure-region.  Alla databaser i instansen kommer att replikeras till den sekundära instansen. Följande diagram illustrerar en typisk konfiguration av ett geo-redundant molnprogram som använder en hanterad instans och automatisk redundans grupp.

![automatisk redundans](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!IMPORTANT]
> Automatisk redundans grupper för hanterad instans finns i offentlig förhandsversion.

Om ditt program använder Managed Instance som datanivån, följer du dessa allmänna riktlinjer när du designar för kontinuitet för företag:

- **Skapa sekundär-instans i samma DNS-zon som den primära instansen**

  När en ny instans skapas, är ett unikt id automatiskt genereras som DNS-zonen och ingår i DNS-namnet på instansen. En flera domäner (SAN)-certifikatet för den här instansen har etablerats med SAN-fältet i form av `zone_id.database.windows.net`. Det här certifikatet kan användas för att autentisera klientanslutningar till en instans i samma DNS-zonen. För att säkerställa att icke-avbryts anslutningen till den primära instansen efter en redundansväxling både de primära och sekundära måste instanser vara i samma DNS-zonen. När programmet är redo för Produktionsdistribution, skapa en sekundär instans i en annan region och kontrollera att den delar DNS-zon med den primära instansen. Detta görs genom att ange en `DNS Zone Partner` valfri parameter med hjälp av Azure portal, PowerShell eller REST API.

  Läs mer om hur du skapar den sekundära instansen i samma DNS-zon som den primära instansen, [hantera redundansgrupper med hanterade instanser (förhandsversion)](#powershell-managing-failover-groups-with-managed-instances-preview).

- **Aktivera replikeringstrafik mellan två instanser**

  Eftersom varje instans isoleras i ett eget virtuellt nätverk, måste två enkelriktad trafik mellan de här virtuella nätverken tillåtas. Se [Azure VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Konfigurera en redundansgrupp för att hantera redundans för hela instans**

  Redundansgruppen att hantera redundans för alla databaser i instansen. När en grupp skapas kommer varje databas i instansen att geo-replikeras automatiskt till den sekundära instansen. Du kan inte använda redundansgrupper för att initiera en partiell redundans för en delmängd av databaserna.

  > [!IMPORTANT]
  > Om en databas tas bort från den primära instansen, tas den också bort automatiskt på den sekundära geo-instansen.

- **Använd Skriv-lyssnare för OLTP-arbetsbelastning**

  När du utför åtgärder för OLTP, använda `<fog-name>.zone_id.database.windows.net` som servern URL och anslutningarna automatiskt dirigeras till primärt. Denna URL ändras inte efter redundansen. Växling vid fel innebär att uppdatera DNS-posten så klientanslutningarna omdirigeras till den nya primärt först när klienten DNS-cacheminnet har uppdaterats. Eftersom den sekundära instansen delar DNS-zon med primärt, kommer klientprogrammet att kunna återansluta till den med samma SAN-certifikat.

- **Ansluta direkt till geo-replikerad sekundär för skrivskyddade frågor**

  Om du har en logiskt avskild skrivskyddad arbetsbelastning som är tolerant för vissa föråldring av data kan använda du den sekundära databasen i programmet. Om du vill ansluta direkt till geo-replikerad sekundär `server.secondary.zone_id.database.windows.net` som servern URL och anslutningen görs direkt till geo-replikerad sekundär.

  > [!NOTE]
  > I vissa tjänstnivåer, Azure SQL Database stöder användning av [skrivskyddade repliker](sql-database-read-scale-out.md) att läsa in saldo skrivskyddat arbetsbelastningar med hjälp av kapaciteten för en skrivskyddad replik och använder den `ApplicationIntent=ReadOnly` parameter i anslutningen sträng. När du har konfigurerat en geo-replikerad sekundär, använder du den här funktionen för att ansluta till antingen en skrivskyddad replik på den primära platsen eller i geo-replikerade platsen.
  > - Om du vill ansluta till en skrivskyddad replik på den primära platsen, `<fog-name>.zone_id.database.windows.net`.
  > - Om du vill ansluta till en skrivskyddad replik på den sekundära platsen `<fog-name>.secondary.zone_id.database.windows.net`.

- **Förberedas för perf försämring**

  SQL redundans beslut är fristående från resten av programmet eller andra tjänster som används. Programmet får ”blandas” med vissa komponenter i en region och vissa i en annan. Om du vill undvika försämringen, se till att redundanta programdistributionen i regionen för Haveriberedskap och följer du dessa [network säkerhetsriktlinjer](#failover-groups-and-network-security).

- **Förbereda för förlust av data**

  Om ett avbrott identifieras utlöser SQL automatiskt läs-och växling vid fel om det finns ingen dataförlust till vår kännedom. I annat fall den väntar tills den period som du angett med `GracePeriodWithDataLossHours`. Om du har angett `GracePeriodWithDataLossHours`, att förbereda för förlust av data. I allmänhet prioriterar Azure under avbrott, tillgänglighet. Om du inte har råd förlust av data, se till att ange lade till tillräckligt många, till exempel 24 timmar.

  DNS-uppdatering för Läs-och lyssnare sker omedelbart efter att redundansen initieras. Den här åtgärden ska inte resultera i dataförlust. Processen för att växla databasroller kan dock ta upp till 5 minuter under normala förhållanden. Tills den är klar, kan fortfarande vissa databaser i den nya primära instansen skrivskyddad. Om redundansväxling initieras med hjälp av PowerShell är hela åtgärden synkron. Om den har initierats med Azure-portalen visar Användargränssnittet slutförandestatus. Om den har initierats med hjälp av REST-API, kan du använda avsökningsmekanism för standard Azure Resource Manager för att övervaka för slutförande.

  > [!IMPORTANT]
  > Använd manuell redundansväxling för att flytta USA: s presidentval tillbaka till den ursprungliga platsen. När avbrott som orsakade redundansen har mildras, kan du flytta dina primära databaser till den ursprungliga platsen. För att göra det måste du initiera manuell växling vid fel i gruppen.

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

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Aktivera geo-replikering mellan hanterade instanser och sina virtuella nätverk

När du ställer in en redundansgrupper mellan primära och sekundära hanterade instanser i två olika regioner är varje instans isolerad med hjälp av en oberoende VNet. Om du vill tillåta replikeringstrafik mellan de här virtuella nätverken Följ se till att är dessa krav uppfyllda:

1. Två hanterade instanser måste vara i olika Azure-regioner.
2. Din sekundära måste vara tom (inga användardatabaser).
3. Den primära och sekundära hanterade instanser måste vara i samma resursgrupp.
4. Virtuella nätverk som den hanterade instanser är en del av behovet av att vara ansluten via en [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Global VNet-Peering stöds inte.
5. De två hanterade instans virtuella nätverken får inte ha överlappande IP-adresser.
6. Du behöver konfigurera dina Nätverkssäkerhetsgrupper (NSG) sådana som portarna 5022 och intervallet 11000 ~ 12000 är öppna inkommande och utgående för anslutningar från andra hanterade Instanced undernätet. Detta är att tillåta replikeringstrafik mellan instanser

    > [!IMPORTANT]
    > Felkonfigurerad NSG security regler leder till har fastnat kopia av databasåtgärder.

7. Du måste konfigurera DNS-zon partner på sekundära instans. En DNS-zon är en egenskap för en hanterad instans. Representerar en del av värdnamn som följer Managed Instance name och föregår den `.database.windows.net` prefix. Det genereras som slumpmässig sträng under genereringen av den första hanterade instansen i varje virtuellt nätverk. DNS-zon kan inte ändras efter skapandet av hanterade instansen och alla hanterade instanser inom samma undernät har samma värde för DNS-zon. För hanterad instans redundans inställning delar den primära hanterade instansen och den sekundära hanterade instansen samma värde för DNS-zon. Det gör du genom att ange parametern DnsZonePartner när du skapar den sekundära hanterade instansen. Egenskapen DNS-zon partner definierar den hanterade instansen om du vill dela en redundansgrupp för instansen med. Genom att skicka till resurs-id för en annan hanterad instans som indata för DnsZonePartner ärver samma DNS-zon värde för partnern hanterad instans i den hanterade instansen håller på att skapas.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uppgradera eller nedgradera en primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräkningsstorleken (inom samma tjänstenivå, inte mellan generell användning och affärskritisk) utan att koppla från alla sekundära databaser. När du uppgraderar, rekommenderar vi att du uppgradera alla sekundära databaserna först och sedan uppgradera primärt. När du nedgraderar, i omvänd ordning: Nedgradera primärt först och sedan nedgraderar alla sekundära databaser. När du uppgraderar eller nedgraderar databasen till en annan tjänstnivå, tillämpas den här rekommendationen.

Den här sekvensen rekommenderas speciellt för att undvika problem där sekundärt vid en lägre SKU hämtar överbelastad och måste kunna igen förpopulerad under en uppgradering eller nedgradering. Du kan också undvika problemet genom att göra primärt skrivskyddade på bekostnad av påverkar alla skrivskyddade arbetsbelastningar mot primärt. 

> [!NOTE]
> Om du har skapat sekundär databas som en del av konfigurationen av redundans-grupp inte rekommenderas att nedgradera den sekundära databasen. Det här är att säkerställa att din datanivå har tillräckligt med kapacitet för att bearbeta din vanliga arbetsbelastning när redundans har aktiverats.

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av viktiga data

Kontinuerlig kopiering använder en asynkron replikeringsmekanism på grund av den lång svarstiden för WAN-nätverk. Asynkron replikering kan data gå förlorade oundvikligt med om ett fel inträffar. Vissa program kan dock kräva utan dataförlust. För att skydda viktiga uppdateringar, en programutvecklare kan anropa den [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) system proceduren omedelbart efter att transaktionen genomförs snabbare. Anropa **sp_wait_for_database_copy_sync** blockerar anropstråden tills den senast allokerade transaktionen har skickats till den sekundära databasen. Men väntar den inte överförda transaktionerna till återupprepas och verkställs på sekundärt. **sp_wait_for_database_copy_sync** är begränsad till en specifik kontinuerlig kopiering-länk. Alla användare med behörighet för anslutning till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> **sp_wait_for_database_copy_sync** förhindrar att data går förlorade efter växling vid fel, men garanterar inte fullständig synkronisering för läsåtkomst. Fördröjningen orsakas av en **sp_wait_for_database_copy_sync** proceduranropet kan vara betydande och beror på storleken på transaktionsloggen vid tidpunkten för anropet.

## <a name="failover-groups-and-point-in-time-restore"></a>Redundansgrupper och point-in-time-återställning

Information om hur du använder point-in-time-återställning med redundansgrupper finns i [peka i tiden Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Programmässigt hantera redundansgrupper

Vilket beskrivs ovan, automatisk redundans grupper och aktiv kan geo-replikering också hanteras via programmering med hjälp av Azure PowerShell och REST-API. I följande tabeller beskrivs uppsättningen kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning API: er för Azure Resource Manager för hantering, inklusive den [Azure SQL Database REST API: et](https://docs.microsoft.com/rest/api/sql/) och [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview). Dessa API: er kräver användning av resursgrupper och stöder rollbaserad säkerhet (RBAC). Mer information om hur du implementerar åtkomst roller finns i [rollbaserad åtkomstkontroll i](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: Hantera redundans för SQL-databas med enskilda databaser och elastiska pooler

| Cmdlet | Beskrivning |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära servrar|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Tar bort redundansgruppen från servern och tar bort alla sekundära databaser ingår i gruppen |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar redundanskonfiguration för grupp |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ändrar konfigurationen för redundansgruppen |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Utlösare redundans för redundansgruppen till den sekundära servern |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Lägger till en eller flera databaser i en Azure SQL Database-redundansgrupp|
|  | |

> [!IMPORTANT]
> Ett exempelskript finns i [konfigurera och redundansväxla en redundansgrupp för en enskild databas](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell: Hantera redundansgrupper med hanterade instanser (förhandsversion)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>Installera den senaste versionen för tidig lansering av PowerShell

1. Uppdatera modulen PowerShellGet till 1.6.5 (eller senaste förhandsversionen). Se [plats för PowerShell-förhandsversion](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

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

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>PowerShell-kommandon för att skapa en redundansgrupp för instans

| API | Beskrivning |
| --- | --- |
| New-AzureRmSqlDatabaseInstanceFailoverGroup |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära servrar|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |Ändrar konfigurationen för redundansgruppen|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |Hämtar redundanskonfiguration för grupp|
| Switch-AzureRmSqlDatabaseInstanceFailoverGroup |Utlösare redundans för redundansgruppen till den sekundära servern|
| Remove-AzureRmSqlDatabaseInstanceFailoverGroup | Tar bort en redundansgrupp|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST-API: Hantera SQL database-redundansgrupper med enkel och delade databaser

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Skapar eller uppdaterar en redundansgrupp |
| [Ta bort redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Tar bort redundansgruppen från servern |
| [Redundansväxling (planerad)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Flyttas över från den primära servern till den här servern. |
| [Påtvingad redundans tillåta förlust av Data](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails över från den primära servern till den här servern. Den här åtgärden kan resultera i dataförlust. |
| [Get-redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Hämtar en redundansgrupp. |
| [Lista Redundansgrupper av servern](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Listar redundansgrupperna på en server. |
| [Uppdatera redundansgrupp](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Uppdaterar en redundansgrupp. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>REST-API: Hantera redundansgrupper med hanterade instanser (förhandsversion)

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundansgrupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Skapar eller uppdaterar en redundansgrupp |
| [Ta bort redundansgrupp](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Tar bort redundansgruppen från servern |
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
