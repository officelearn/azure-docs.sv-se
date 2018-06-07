---
title: Redundans grupper och aktiv geo-replikering - Azure SQL Database | Microsoft Docs
description: Använda grupper för automatisk växling vid fel med aktiv geo-replikering och aktivera autoomatic växling vid fel vid ett avbrott.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 0c27aec90dad6eb3aeb46871d20202870eba886d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647653"
---
# <a name="overview-failover-groups-and-active-geo-replication"></a>Översikt: Redundans grupper och aktiv geo-replikering
Aktiv geo-replikering kan du konfigurera upp till fyra läsbara sekundära databaser på samma eller olika data center platser (regioner). Sekundära databaser är tillgängliga för frågor och växling vid fel om det finns ett avbrott för data center eller oförmåga att ansluta till den primära databasen. Växling vid fel måste initieras manuellt med hjälp av användaren. Efter växling vid fel har den nya primärt slutpunkten för en annan anslutning. 

> [!NOTE]
> Aktiv geo-replikering är tillgänglig för alla databaser i alla servicenivåer i alla regioner.
>  

Azure SQL Database automatisk redundans grupper (i förhandsversion) är en funktion för SQL-databas som utformats för att hantera automatiskt geo-replikering relation-, anslutnings- och växling vid fel i större skala. Får kunder möjligheten att automatiskt återskapa flera relaterade databaser i den sekundära regionen efter oåterkalleligt regionala fel eller andra oplanerade händelser som ger fullständig eller partiell förlust av tillgänglighet för SQL Database-tjänsten i den primär region. Dessutom kan de använda läsbara sekundära databaser för att avlasta skrivskyddade arbetsbelastningar.  Eftersom automatisk redundans grupper inkluderar flera databaser, måste de konfigureras på den primära servern. Både primära och sekundära servrar måste finnas i samma prenumeration. Automatisk redundans grupper stöd för replikering av alla databaser i gruppen för att endast en sekundär server i en annan region. Aktiv geo-replikering, utan automatisk redundans grupper kan upp till fyra sekundärservrar i en region.

Om du använder aktiv geo-replikering och skäl din primära databasen misslyckas för alla eller bara behöver vara offline, kan du initiera redundans till någon av de sekundära databaserna. När växling vid fel är aktiverat för en av de sekundära databaserna länkas alla sekundära automatiskt till den nya primärt. Om du använder automatisk redundans grupper (i förhandsversion) för att hantera återställning av databasen och eventuella avbrott som påverkar en eller flera databaser i gruppen resultaten i automatisk redundans. Du kan konfigurera principen för automatisk redundans som bäst uppfyller behoven för ditt program eller du kan välja och använda manuell aktivering. Dessutom automatisk redundans grupper (i förhandsversion) ger Läs-och skrivbehörighet och skrivskyddad lyssnare slutpunkter som förblir oförändrade under redundans. Om du använder manuell eller automatisk redundans aktivering växlar redundans alla sekundära databaser i gruppen till primär. När för växling vid databasfel är klar uppdateras automatiskt DNS-posten så att slutpunkterna till den nya regionen.

Du kan hantera replikering och redundans för en individuell databas eller en uppsättning databaser på en server eller i en elastisk pool använder aktiv geo-replikering. Du kan göra det med 

- Den [Azure-portalen](sql-database-geo-replication-portal.md)
- [PowerShell: Enskild databas](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Elastisk pool med](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell: Redundansväxlingsgrupp](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL: Enskild databas eller elastisk pool](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST-API: Enskild databas](/rest/api/sql/replicationlinks/failover)
- [REST-API: Redundansväxlingsgrupp](/rest/api/sql/failovergroups/failover). 
 
Kontrollera autentiseringskraven för servern och databasen har konfigurerats på den nya primärt efter redundans. Mer information finns i [SQL Database-säkerhet efter katastrofåterställning](sql-database-geo-replication-security-config.md). Detta gäller både aktiva geo-replikering och automatisk redundans grupper (i förhandsversion).

Aktiv geo-replikering utnyttjar den [alltid på](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) teknik för SQL Server för att replikera asynkront genomförda transaktioner på den primära databasen till en sekundär databas med hjälp av läsa allokerat ögonblicksbildisolering (RCSI). Automatisk redundans grupper ger gruppen semantik ovanpå aktiv geo-replikering, men samma asynkrona replikeringsmekanism används. Den sekundära databasen kan vara något bakom den primära databasen, sekundära data garanterat aldrig har delvis transaktioner medan vid varje given tidpunkt. Cross-region redundans gör det möjligt för program att snabbt återställa en förlust av ett helt datacenter eller delar av ett datacenter som orsakats av naturkatastrof, oåterkalleligt mänskliga fel eller skadliga åtgärder. Specifika Återställningspunktmål data finns på [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).

Följande bild visar ett exempel på en aktiv geo-replikering konfigureras med en primär region norra centrala USA och sekundär i södra centrala USA-region.

![GEO-replikering relation](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Eftersom sekundära databaser är läsbar, kan de användas för att avlasta skrivskyddade arbetsbelastningar som rapportering jobb. Om du använder aktiv geo-replikering är det möjligt att skapa den sekundära databasen i samma region med primärt, men den ökar inte programmets återhämtning för katastrofalt fel. Om du använder automatisk redundans grupper (i förhandsversion) skapas alltid din sekundära databas i en annan region.

Förutom disaster kan recovery aktiv geo-replikering användas i följande scenarier:

* **Databasen migrering**: du kan använda aktiv geo-replikering för att migrera en databas från en server till en annan online med minimal avbrottstid.
* **Programuppgraderingar**: du kan skapa en extra sekundär som en bakre kopia misslyckas under programuppgraderingar.

För att uppnå verkliga affärskontinuitet, lägger till databasredundans mellan Datacenter är endast en del av lösningen. Återställa ett program (tjänsten) slutpunkt till slutpunkt efter återställning av alla komponenter som utgör tjänsten och alla beroende tjänster kräver att ett oåterkalleligt fel. Exempel på dessa komponenter är klientprogram (till exempel en webbläsare med en anpassad JavaScript), frontwebbservrar, lagring och DNS. Det är viktigt att alla komponenter är känsligt för samma fel och bli tillgänglig inom den recovery tid mål för Återställningstid för programmet. Därför måste du identifiera alla beroende tjänster och förstå garantier och funktioner som de tillhandahåller. Måste du vidta lämpliga åtgärder för att se till att tjänsten fungerar under växling vid fel som den beroende tjänster. Mer information om hur man designar lösningar för katastrofåterställning finns [designa Molnlösningar för Disaster Recovery använder aktiv geo-replikering](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Funktioner för aktiv geo-replikering
Funktionen aktiv geo-replikering innehåller följande viktiga funktioner:
* **Automatisk asynkron replikering**: du kan bara skapa en sekundär databas genom att lägga till en befintlig databas. Sekundärt kan skapas i en Azure SQL Database-server. När du skapat fylls den sekundära databasen med de data som kopieras från den primära databasen. Den här processen kallas seeding. När sekundära databas har skapats och angetts, replikeras uppdateringar till den primära databasen asynkront till den sekundära databasen automatiskt. Asynkron replikering innebär att transaktionerna sparas på den primära databasen innan de replikeras till den sekundära databasen. 
* **Läsbara sekundära databaser**: ett program kan komma åt en sekundär databas för skrivskyddade åtgärder med samma eller olika säkerhetsobjekt används för åtkomst till den primära databasen. De sekundära databaserna fungerar ögonblicksbild i isolerat läge så replikering av uppdateringar av den primära servern (loggen replay) inte skjutas upp med frågor som körs på sekundärt.

   > [!NOTE]
   > Loggen replay är försenad på den sekundära databasen om det finns schemauppdateringar på primärt. Dessa kräver ett schemalås på den sekundära databasen. 
   > 

* **Flera läsbara sekundärservrar**: två eller flera sekundära databaser öka redundans och nivå av skydd för den primära databasen och program. Om det finns flera sekundära databaser, förblir programmet skyddat även om en av de sekundära databaserna misslyckas. Om det finns endast en sekundär databas och misslyckas, exponeras programmet för högre risk förrän en ny sekundär databas har skapats.

   > [!NOTE]
   > Du kan skapa sekundär för en sekundär (en process som kallas länkning) om du använder aktiv geo-replikering för att skapa ett globalt distribuerat program och behöver ange skrivskyddad åtkomst till data i mer än fyra områden. Det här sättet kan du uppnå praktiskt taget obegränsade skalan för databasreplikering. Dessutom minskar länkning kostnader för replikering från den primära databasen. En kompromiss är bättre replikeringsfördröjning på lövnivå mest sekundära databaser. 
   >

* **Stöd för elastiska poolen databaser**: varje replik kan separat deltar i en elastisk Pool eller inte i en elastisk pool alls. Pool-alternativ för varje replik är separat och är inte beroende av konfigurationen för andra replikeringar (om det primära eller sekundära). Varje elastisk Pool som ingår i en enskild region, flera repliker i samma topologin kan därför inte dela en elastisk Pool.
* **Konfigurerbara prestandanivåerna för den sekundära databasen**: både primära och sekundära databaser måste ha samma tjänstnivån. Du kan skapa en sekundär databas med lägre prestandanivå (Dtu) än primärt. Det här alternativet rekommenderas inte för program med hög Databasaktivitet för skrivning eftersom den ökade replikeringsfördröjning ökar risken för betydande dataförlust efter en redundansväxling. Efter redundans påverkas programmets prestanda dessutom tills den nya primärt uppgraderas till en högre prestandanivå. Logg-i/o procentandel diagrammet på Azure-portalen innehåller ett bra sätt att uppskatta den sekundära som krävs för att upprätthålla belastningen replikering minimal prestandanivå. Till exempel om den primära databasen är P6 (1000 DTU) och loggen IO-procent är 50% sekundär måste vara minst P4 (500 DTU). Du kan också hämta logg-i/o-data med hjälp av [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) eller [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) databasen vyer.  Mer information om prestandanivåer för SQL-databas finns [vad är SQL Database servicenivåer](sql-database-service-tiers.md). 
* **Användaren styrs redundans och återställning efter fel**: en sekundär databas kan uttryckligen växlas till den primära rollen när som helst genom att programmet eller användaren. Under en verklig avbrott ska alternativet ”oplanerad” användas som främjar omedelbart en sekundär för att vara primärt. När den felaktiga primärt återställer och är tillgänglig igen, markerar den återställda primärt som en sekundär automatiskt i systemet och göra den uppdaterade med den nya primärt. På grund av asynkrona hur replikering, kan en liten mängd data gå förlorade under oplanerad redundans om en primär misslyckas innan de senaste ändringarna replikeras till sekundärt. När en primärnyckel med flera sekundärservrar växlar, konfigurerar replikeringens relationer automatiskt i systemet och länkar återstående sekundärservrar till den nyligen uppgraderat primärt utan användarens ingripande. När avbrott som har orsakat redundans minskas kan det vara önskvärt att returnera programmet till den primära regionen. För att göra det, bör redundanskommandot anropas med alternativet ”planerad”. 
* **Synkronisera autentiseringsuppgifter och brandväggsregler**: Vi rekommenderar att du använder [databasen brandväggsregler](sql-database-firewall-configure.md) för geo-replikerade databaser så att de här reglerna kan replikeras med databasen för att se till att alla sekundära databaser har den samma brandväggsregler som den primära servern. Den här metoden slipper för kunder att manuellt konfigurera och underhålla brandväggsregler på servrar som är värd för både de primära och sekundära databaserna. På samma sätt med hjälp av [innehöll databasanvändare](sql-database-manage-logins.md) för data access ser du till både primära och sekundära databaser har alltid samma autentiseringsuppgifter så att det finns inga avbrott på grund av överensstämmer inte med inloggningsnamn och lösenord under en redundansväxling. Med tillägg av [Azure Active Directory](../active-directory/active-directory-whatis.md)kunder kan hantera användarnas åtkomst till både primära och sekundära databaser och eliminera behovet av att hantera autentiseringsuppgifter i databaser helt och hållet.

## <a name="auto-failover-group-capabilities"></a>Automatisk redundans grupp funktioner

Automatisk redundans grupper funktion tillhandahåller en kraftfull abstraktion av aktiv geo-replikering genom att stödja grupp nivån replikering och automatisk redundans. Dessutom kan den tar bort behovet av att ändra den SQL-anslutningssträngen efter växling vid fel genom att tillhandahålla ytterligare lyssnare slutpunkter. 

* **Redundansväxlingsgrupp**: en eller flera grupper för växling vid fel kan skapas mellan två servrar i olika regioner (primära och sekundära servrar). Varje grupp kan innehålla en eller flera databaser som återställs som en enhet om alla eller vissa av de databaser som primär blir otillgänglig på grund av ett avbrott i den primära regionen.  
* **Primär server**: en server som är värd för de primära databaserna i gruppen växling vid fel.
* **Sekundär server**: en server som värd för de sekundära databaserna i gruppen växling vid fel. Den sekundära servern kan inte vara i samma region som den primära servern.
* **Lägga till databaser till redundansväxlingsgrupp**: du kan placera flera databaser i en server eller i en elastisk pool i samma grupp för växling vid fel. Om du lägger till en fristående databas i gruppen skapar automatiskt en sekundär databas med samma version och prestandanivå. Om den primära databasen är i en elastisk pool, skapas sekundärt automatiskt i den elastiska poolen med samma namn. Om du lägger till en databas som redan har en sekundär databas i den sekundära servern ärvs att geo-replikering av gruppen.

   > [!NOTE]
   > När du lägger till en databas som redan har en sekundär databas på en server som inte ingår i gruppen växling vid fel, skapas en ny sekundär i den sekundära servern. 
   >

* **Redundans skrivskyddad lyssnare**: skapas från en DNS CNAME-post  **&lt;redundans gruppnamn&gt;. database.windows.net** som pekar på den aktuella primära server-URL. Det gör att läsa / skriva SQL programmen transparent återansluta till den primära databasen när primärt ändras efter växling vid fel. 
* **Redundans skrivskyddad lyssnare**: skapas från en DNS CNAME-post  **&lt;redundans gruppnamn&gt;. secondary.database.windows.net** som pekar på den sekundära servern URL. Det gör att skrivskyddad SQL programmen transparent ansluta till den sekundära databasen med hjälp av de angivna reglerna för belastningsutjämning. Alternativt kan du ange om du vill att skrivskyddad trafik dirigeras automatiskt till den primära servern när den sekundära servern inte är tillgänglig.
* **Princip för automatisk redundans**: som standard failover-grupp har konfigurerats med en princip för automatisk redundans. Systemet utlöser växling vid fel när felet har identifierats. Om du vill styra redundans arbetsflödet från programmet, kan du inaktivera automatisk redundans. 
* **Manuell växling**: du kan initiera redundans manuellt när som helst oavsett automatisk redundans-konfiguration. Om automatisk redundans principen inte är konfigurerad krävs manuell växling för att återställa databaser i gruppen växling vid fel. Du kan initiera framtvingad eller eget redundansväxling (med Fullständig datasynkronisering). Dessa kan användas för att flytta den aktiva servern till den primära regionen. När redundansväxlingen är klar uppdateras automatiskt DNS-poster för att kontrollera nätverksanslutningen till rätt server.
* **Grace-period med dataförlust**: eftersom de primära och sekundära databaserna är synkroniserad med hjälp av asynkron replikering, redundans kan resultera i förlust av data. Du kan anpassa principen för automatisk redundans för att återspegla programmets tolerans för dataförlust. Genom att konfigurera **GracePeriodWithDataLossHours**, du kan styra hur länge systemet ska vänta innan du utför redundans som troligen kommer att resultatet dataförlust. 

   > [!NOTE]
   > När systemet identifierar att databaserna i gruppen är online (exempelvis avbrottet endast påverkas service control plan), redundans med Fullständig datasynkronisering (eget failover) aktiveras omedelbart oavsett värdet av  **GracePeriodWithDataLossHours**. Det här beteendet garanteras att det finns inga data går förlorade under återställningen. Respittiden gäller endast när ett eget failover inte är möjligt. Om avbrottet minskas innan respittiden upphör att gälla, aktiveras inte för växling vid fel.
   >

* **Flera grupper för växling vid fel**: du kan konfigurera flera redundans grupper för samma par av servrar för att styra omfattningen av växling vid fel. Varje grupp flyttas över oberoende av varandra. Om ditt program med flera innehavare använder elastiska pooler, kan du använda den här funktionen för att blanda primära och sekundära databaser i varje pool. Det här sättet kan du minska effekten av ett avbrott för att endast hälften av innehavarna.

## <a name="best-practices-of-building-highly-available-service"></a>Metodtips för att skapa tjänst med hög tillgänglighet

Om du vill skapa en tjänst med hög tillgänglighet som använder Azure SQL database, bör du följa dessa riktlinjer:

- **Använd failover grupp**: en eller flera grupper för växling vid fel kan skapas mellan två servrar i olika regioner (primära och sekundära servrar). Varje grupp kan innehålla en eller flera databaser som återställs som en enhet om alla eller vissa av de databaser som primär blir otillgänglig på grund av ett avbrott i den primära regionen. Gruppen redundans skapar geo sekundär databas med samma tjänstmålet som primärt. Om du lägger till en befintlig relation geo-replikering gruppen växling vid fel, kontrollera geo-sekundär har konfigurerats med samma servicenivåmålet som primärt.
- **Använda läsa / skriva-lyssnare för OLTP-arbetsbelastning**: när du utför OLTP-åtgärder, Använd  **&lt;redundans gruppnamn&gt;. database.windows.net** som servern är URL och anslutningar omdirigeras automatiskt till den primära servern. URL: en ändras inte efter växling vid fel. Obs växling vid fel innebär att uppdatera DNS-posten så klientanslutningarna omdirigeras till den nya primärt förrän klienten DNS-cachen uppdateras.
- **Använd endast Läs-lyssnaren för skrivskyddad arbetsbelastning**: Om du har en logiskt isolerade skrivskyddad arbetsbelastning som är feltolerant till vissa föråldrad av data kan du använda den sekundära databasen i programmet. Skrivskyddad sessioner, Använd  **&lt;redundans gruppnamn&gt;. secondary.database.windows.net** som server URL: en och anslutningen automatiskt omdirigeras till sekundärt. Vi rekommenderar också att du anger i anslutningssträngen läsa avsikten med hjälp av **ApplicationIntent ReadOnly =**. 
- **Förberedas för prestanda försämras**: SQL redundans beslut är fristående från resten av programmet eller andra tjänster som används. Programmet får ”blandas” med vissa komponenter i en region och en del i en annan. För att undvika nedbrytning, kontrollera redundant programdistributionen i DR region och Följ riktlinjerna i den här artikeln. Observera programmet i DR region behöver inte använda en annan anslutningssträng.  
- **Förbereda för förlust av data**: om ett avbrott har upptäckts SQL automatiskt utlöser skrivskyddad växling vid fel om det finns ingen dataförlust till vår kännedom. I annat fall det väntar på den period som du angett med **GracePeriodWithDataLossHours**. Om du har angett **GracePeriodWithDataLossHours**, att förbereda för förlust av data. I allmänhet prioriterar Azure under avbrott, tillgänglighet. Om du inte har råd dataförlust, se till att ange **GracePeriodWithDataLossHours** till tillräckligt många, till exempel 24 timmar. 

> [!IMPORTANT]
> Elastiska pooler med 800 eller mindre dtu: er och fler än 250 databaser med geo-replikering kan stöta på problem längre planerad redundans och sämre prestanda.  De här problemen sannolikt skrivåtgärder intensiva arbetsbelastningar kan uppstå när geo-replikering slutpunkter är långt ifrån varandra efter geografi, eller när flera sekundära slutpunkter som används för varje databas.  Symptom på de här problemen anges när geo-replikeringsfördröjning ökar med tiden.  Den här fördröjningen kan övervakas med [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Om dessa problem uppstår kan innehålla åtgärder öka antalet poolen dtu: er eller minska antalet geo-replikerade databaser i samma pool.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uppgradera eller nedgradera en primär databas
Du kan uppgradera eller nedgradera en primär databas till en annan prestandanivå (inom samma tjänstnivån) utan att koppla från alla sekundära databaser. När du uppgraderar, rekommenderar vi att du uppgraderar den sekundära databasen först och sedan uppgradera primärt. När nedgradera, i omvänd ordning: först nedgradera den primära servern och nedgradera sekundär. När du uppgraderar eller nedgradera databasen till en annan tjänstnivå, tillämpas den här rekommendationen. 

> [!NOTE]
> Det rekommenderas inte att degradera den sekundära databasen om du har skapat sekundära databasen som en del av redundanskonfiguration för gruppen. Detta är att din datanivå har tillräcklig kapacitet för att bearbeta din vanliga arbetsbelastning efter växling vid fel är aktiverat. 
>

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av viktiga data
På grund av fördröjningar av WAN-nätverk använder kontinuerlig kopiering en asynkron replikering. Asynkron replikering kan data gå förlorade oundvikligt om ett fel inträffar. Vissa program kan dock kräva att inga data går förlorade. För att skydda viktiga uppdateringar, programutvecklare kan anropa den [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) system proceduren omedelbart efter att genomföra transaktionen. Anropar **sp_wait_for_database_copy_sync** hindrar den anropande tråden tills senaste allokerad transaktion har skickats till den sekundära databasen. Men väntar det inte på att de överförda transaktionerna som ska spelas och verkställs på sekundärt. **sp_wait_for_database_copy_sync** är begränsad till en specifik kontinuerlig kopiering länk. Alla användare med behörighet för anslutning till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> **sp_wait_for_database_copy_sync** förhindrar dataförlust efter växling vid fel, men garanterar inte fullständig synkronisering för läsåtkomst. Fördröjningen på grund av en **sp_wait_for_database_copy_sync** proceduranropet kan vara betydande och beror på storleken på transaktionsloggen vid tidpunkten för anropet. 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Programmässigt hantera redundans grupper och aktiv geo-replikering
Som beskrivits tidigare, automatisk redundans grupper (i förhandsversion) och aktiv kan geo-replikering också hanteras via programmering med Azure PowerShell och REST-API. I följande tabeller beskrivs uppsättningen kommandon som är tillgängliga.

**Azure Resource Manager API och rollbaserad säkerhet**: aktiv geo-replikering innehåller en uppsättning API: er för Azure Resource Manager för hantering, inklusive den [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) och [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview). Dessa API: er kräver användning av resursgrupper och stöder rollbaserad säkerhet (RBAC). Mer information om hur du implementerar åtkomst roller finns [rollbaserad åtkomstkontroll i](../role-based-access-control/overview.md).

## <a name="manage-sql-database-failover-using-transact-sql"></a>Hantera SQL-databas redundans med Transact-SQL

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Använd Lägg till sekundär på SERVER argumentet för att skapa en sekundär databas för en befintlig databas och startar datareplikering |
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Använd växling vid fel eller FORCE_FAILOVER_ALLOW_DATA_LOSS för att växla en sekundär databas för att vara primär att initiera redundans |
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Använd ta bort sekundär på servern att avbryta en datareplikering mellan en SQL-databas och den angivna sekundära databasen. |
| [sys.geo_replication_links (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Returnerar information om alla befintliga replikeringslänkar för varje databas på den logiska Azure SQL Database-servern. |
| [sys.dm_geo_replication_link_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Hämtar senast replikering, senaste replikeringsfördröjning och annan information om replikeringslänken för en angiven SQL-databas. |
| [sys.dm_operation_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Visar status för alla databasåtgärder, inklusive statusen för länkar för databasreplikering. |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |gör att programmet ska vänta tills alla genomförda transaktioner replikeras och bekräftas av den aktiva sekundära databasen. |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>Hantera SQL-databas med växling vid fel med hjälp av PowerShell

| Cmdlet | Beskrivning |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Hämtar en eller flera databaser. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Skapar en sekundär databas för en befintlig databas och startar datareplikeringen. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Växlar en sekundär databas till att vara primär för att initiera redundans. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Avslutar datareplikering mellan en SQL Database och den angivna sekundära databasen. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Hämtar geo-replikeringslänkar mellan en Azure SQL Database och en resursgrupp eller SQL Server. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Det här kommandot skapar en redundansväxlingsgrupp och registrerar den på både primära och sekundära servrar|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Tar bort gruppen växling vid fel från servern och tar bort alla sekundära databaser ingår i gruppen |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Hämtar grupp redundans-konfiguration |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Ändrar konfigurationen för failover-grupp |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Utlösare för växling vid fel i gruppen redundans till den sekundära servern |
|  | |

> [!IMPORTANT]
> Exempel på skript, se [konfigurera och redundans en enskild databas som använder aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [konfigurera och redundans en delad databas som använder aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md), och [ Konfigurera och växling vid fel redundans grupp för en enskild databas (förhandsgranskning)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>Hantera SQL-databas med växling vid fel med hjälp av REST-API
| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera databasen (createMode = återställer)](/rest/api/sql/Databases/CreateOrUpdate) |Skapar, uppdaterar eller återställer en primär eller sekundär databas. |
| [Hämta skapa eller uppdatera databasens Status](/rest/api/sql/Databases/CreateOrUpdate) |Returnerar status under en Skapa-åtgärd. |
| [Ange Sekundär databasen som primär (planerad redundans)](/rest/api/sql/replicationlinks/failover) |Anger vilka replikdatabasen är primär av misslyckande från den aktuella primära replik-databasen. |
| [Ange Sekundär databasen som primär (oplanerad växling vid fel)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Anger vilka replikdatabasen är primär av misslyckande från den aktuella primära replik-databasen. Den här åtgärden kan resultera i förlust av data. |
| [Hämta replikeringslänk](/rest/api/sql/replicationlinks/get) |Hämtar en specifik replikeringslänk för en angiven SQL-databas i ett partnerskap geo-replikering. Den hämtar informationen som visas i vyn sys.geo_replication_links katalog. |
| [Replikeringslänkar - lista av databasen](/rest/api/sql/replicationlinks/listbydatabase) | Hämtar alla länkar för databasreplikering för en angiven SQL-databas i ett partnerskap geo-replikering. Den hämtar informationen som visas i vyn sys.geo_replication_links katalog. |
| [Ta bort länk för Platsdatareplikering](/rest/api/sql/databases/delete) | Tar bort en databasreplikeringslänk. Kan inte utföras under växling vid fel. |
| [Skapa eller uppdatera Failover-grupp](/rest/api/sql/failovergroups/createorupdate) | Skapar eller uppdaterar en redundansväxlingsgrupp |
| [Ta bort redundans grupp](/rest/api/sql/failovergroups/delete) | Tar bort gruppen växling vid fel från servern |
| [Växling vid fel (planerad)](/rest/api/sql/failovergroups/failover) | Flyttas över från den aktuella primära servern till den här servern. |
| [Påtvingad redundans tillåta förlust av Data](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails över från den aktuella primära servern till den här servern. Den här åtgärden kan resultera i förlust av data. |
| [Get redundans grupp](/rest/api/sql/failovergroups/get) | Hämtar en redundansväxlingsgrupp. |
| [Lista redundans grupper av servern](/rest/api/sql/failovergroups/listbyserver) | Listar grupperna för växling vid fel på en server. |
| [Uppdatera grupp om växling vid fel](/rest/api/sql/failovergroups/update) | Uppdaterar en redundansväxlingsgrupp. |
|  | |

## <a name="next-steps"></a>Nästa steg
* För exempel på skript, se:
   - [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [Konfigurera och redundansväxla en databas i pool med aktiv geo-replikering](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [Konfigurera och redundansväxla en redundansgrupp för en enskild databas (förhandsversion)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* En översikt över verksamhetskontinuitet och scenarier finns [översikt över verksamhetskontinuitet](sql-database-business-continuity.md)
* Lär dig mer om Azure SQL Database automatiserad säkerhetskopieringar, se [SQL-databas automatisk säkerhetskopiering](sql-database-automated-backups.md).
* Läs om hur du använder automatisk säkerhetskopiering för återställning i [återställa en databas från säkerhetskopior service-initierad](sql-database-recovery-using-backups.md).
* Läs om autentiseringskrav på för en ny primär server och databas i [SQL Database-säkerhet efter katastrofåterställning](sql-database-geo-replication-security-config.md).

