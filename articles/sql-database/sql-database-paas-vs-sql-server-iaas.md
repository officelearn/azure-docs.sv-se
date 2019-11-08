---
title: Välj rätt distributions alternativ
description: Lär dig hur du väljer mellan distributions alternativen i Azure SQL mellan SQL-databaser, SQL-hanterade instanser och SQL Server på virtuella Azure-datorer.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server-moln, SQL Server i molnet, PaaS-databas, moln-SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/22/2019
ms.openlocfilehash: e642454807511e8e0bc0b6b6ca7af837e03de2cf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821352"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Välj rätt distributions alternativ i Azure SQL

Lär dig hur varje distributions alternativ passar in i Microsofts Azure SQL data-plattform och få hjälp att matcha rätt alternativ för dina affärs behov. Oavsett om du prioriterar kostnads besparingar eller minimal administration kan den här artikeln hjälpa dig att avgöra vilken metod som passar bäst för de affärs krav du bryr dig om.

## <a name="microsofts-azure-sql-data-platform"></a>Microsofts Azure SQL data-plattform

Från lyft och Shift-migreringar till modernisering av befintliga program för att skapa moderna moln tjänster är Azure SQL en modern SQL-plattform som har flera distributions alternativ som drivs av en branschledande Microsoft SQL Server motor. Azure SQL har utformats för att stödja en mängd olika program mönster med olika kontroll nivåer över den underliggande plattformen för att uppfylla de mest krävande kraven för migrering och modernisering. Azure SQL eliminerar komplexiteten vid hantering av olika samlingar av SQL Server-baserade program i stor skala genom att tillhandahålla en enda enhetlig hanterings upplevelse.

En av de första sakerna man ska förstå när det gäller Azure kontra lokala SQL Server-databaser, är att det är möjligt att använda allt. Microsofts data plattform utnyttjar SQL Server teknik och gör den tillgänglig på fysiska lokala datorer, privata moln miljöer, privata moln miljöer från tredje part och det offentliga molnet. SQL Server på virtuella Azure-datorer (virtuella SQL-datorer) kan du möta unika och skilda affärs behov genom en kombination av lokala och molnbaserade distributioner, samtidigt som du använder samma uppsättning Server produkter, utvecklingsverktyg och expertis i dessa miljöer.

   ![SQL Server-molnalternativ: SQL-server på IaaS, eller SaaS-SQL-databas i molnet.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Som det visas i diagrammet kan varje erbjudande karakteriseras av den hanterings nivå som du har över infrastrukturen och i takt med kostnads effektiviteten.

I Azure kan du ha SQL Server arbets belastningar som körs som en värdbaserad tjänst ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) eller en värdbaserad infrastruktur ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). I PaaS har du flera distributions alternativ och tjänst nivåer inom varje distributions alternativ. Den viktiga fråga som du behöver fråga när du väljer mellan PaaS eller IaaS är att du vill hantera din databas, tillämpa korrigeringar och göra säkerhets kopior, eller vill du delegera dessa åtgärder till Azure?

Beroende på svaret har du följande alternativ:

- [**SQL-databaser**](sql-database-technical-overview.md): bäst för moderna moln program som vill använda de senaste stabila SQL Server funktionerna och har tidsbegränsningar i utvecklingen och marknadsföringen. En fullständigt hanterad SQL Database-motor, baserat på den senaste stabila Enterprise-versionen av SQL Server. Det här är en Relations databas som en tjänst (DBaaS) som finns i Azure-molnet och som hamnar i kategorin *Platform-as-a-Service (PaaS)* . SQL Database har flera distributions alternativ som bygger på standardiserad maskin-och program vara som ägs, hanteras och underhålls av Microsoft. Med SQL Server kan du använda inbyggda funktioner och funktioner som kräver omfattande konfiguration (antingen lokalt eller i en virtuell Azure-dator). När du använder SQL Database, betalar du per användning med alternativ att skala upp eller ut för mer kraft utan avbrott. SQL Database har ytterligare funktioner som inte är tillgängliga i SQL Server, till exempel inbyggd hög tillgänglighet, intelligens och hantering.


  Databaser erbjuder följande distributions alternativ:
  - Som en [***enskild databas***](sql-database-single-database.md) med en egen uppsättning resurser som hanteras via en databas server. En enda databas liknar en [innesluten databas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) i SQL Server. Det här alternativet är optimerat för modern program utveckling av nya molnbaserade program. Alternativen för [storskalig](sql-database-service-tier-hyperscale.md) och [Server](sql-database-serverless.md) lös är tillgängliga.
  - En [***elastisk pool***](sql-database-elastic-pool.md), som är en samling databaser med en delad uppsättning resurser som hanteras via en databas server. Enskilda databaser kan flyttas till och från en elastisk pool. Det här alternativet är optimerat för modern program utveckling av nya molnbaserade program med hjälp av SaaS program mönster för flera innehavare. Elastiska pooler ger en kostnads effektiv lösning för att hantera prestanda för flera databaser som har varierande användnings mönster.
  - En [***databas server***](sql-database-servers.md)som används för att hantera grupper av enskilda databaser och elastiska pooler. Databas servrar fungerar som en central administrativ punkt för flera databaser i en enda databas, [inloggningar](sql-database-manage-logins.md), [brand Väggs regler](sql-database-firewall-configure.md), [gransknings regler](sql-database-auditing.md), [principer för hot identifiering](sql-database-threat-detection.md)och [failover-grupper](sql-database-auto-failover-group.md).

- [**SQL-hanterade instanser**](sql-database-managed-instance.md): bäst för de flesta migreringar till molnet. Hanterad instans är en samling system-och användar databaser med en delad uppsättning resurser som lyfter och Skift är klara. Bäst för nya program eller befintliga lokala program som vill använda de senaste stabila SQL Server funktionerna och som migreras till molnet med minimala ändringar. En hanterad instans liknar en instans av [Microsoft SQL Server databas motor](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) som erbjuder delade resurser för databaser och ytterligare funktioner för instans omfattning. Hanterad instans har stöd för migrering av databasen från lokala datorer med minimal till ingen databas ändring. Det här alternativet ger alla PaaS-förmåner för Azure SQL Database men lägger till funktioner som tidigare endast var tillgängliga i virtuella SQL-datorer. Detta inkluderar ett internt virtuellt nätverk (VNet) och nära 100%-kompatibilitet med lokala SQL Server. Hanterade instanser ger fullständig SQL Server åtkomst och funktions kompatibilitet för migrering av SQL-servrar till Azure.


- [**Virtuella SQL-datorer**](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md): bäst för migreringar och program som kräver åtkomst till operativ system nivå. Virtuella SQL-datorer är hiss-och-Shift-klara för befintliga program som kräver snabb migrering till molnet med minimala förändringar eller inga ändringar. Virtuella SQL-datorer ger fullständig administrativ kontroll över SQL Server-instansen och underliggande operativ system för migrering till Azure. Snabba utvecklings- och test-scenarier där du inte vill köpa lokal SQL Server-maskinvara som inte är för produktion. Virtuella SQL-datorer ingår i bransch kategorin *Infrastructure-as-a-Service (IaaS)* och gör att du kan köra SQL Server i en fullständigt hanterad virtuell dator (VM) i Azure-molnet. Virtuella SQL-datorer körs också på standardiserad maskin vara som ägs, hanteras och underhålls av Microsoft. När du använder virtuella SQL-datorer kan du antingen betala per användning för en SQL Server licens som redan finns i en SQL Server avbildning eller enkelt använda en befintlig licens. Du kan också stoppa eller återuppta den virtuella datorn efter behov. SQL Server installerat och som finns i molnet körs på virtuella Windows Server-eller Linux-datorer som körs på Azure, även kallat en infrastruktur som en tjänst (IaaS). Virtuella SQL-datorer är ett lämpligt alternativ för att migrera lokala SQL Server databaser och program utan någon databas ändring. Alla nya versioner och utgåvor av SQL Server är tillgängliga för installation på en virtuell IaaS-dator. Den viktigaste skillnaden mellan SQL-databaser och SQL-hanterade instanser är att SQL Server virtuella datorer tillåter fullständig kontroll över databas motorn. Du kan välja när du vill starta underhåll/korrigering, ändra återställnings modellen till enkel eller Mass utloggning, pausa eller starta tjänsten vid behov, och du kan anpassa den SQL Server databas motorn helt. Med den här extra kontrollen får du extra ansvar att hantera den virtuella datorn.

  Optimerad för migrering av befintliga program till Azure eller för att utöka befintliga lokala program till molnet i hybrid distributioner. Du kan också använda SQL Server på en virtuell dator för att utveckla och testa traditionella SQL Server-program. Med virtuella SQL-datorer har du fullständiga administrativa rättigheter över en dedikerad SQL Server instans och en molnbaserad virtuell dator. Det är det perfekta valet när en organisation redan har IT-resurser tillgängliga för att underhålla de virtuella datorerna. Med dessa funktioner kan du skapa ett ytterst anpassat system för ditt programs specifika prestanda- och tillgänglighetsbehov.


Ytterligare skillnader visas i följande tabell, men ***både databaser och hanterade instanser är optimerade för att minska de totala hanterings kostnaderna till minst för etablering och hantering av många databaser.*** Det minskar löpande administrativa kostnader eftersom du inte behöver hantera några virtuella datorer, operativsystem eller någon databasprogramvara. Du behöver inte hantera uppgraderingar, hög tillgänglighet eller [säkerhetskopieringar](sql-database-automated-backups.md). Generellt sett kan Azure SQL Database kraftigt öka antalet databaser som kan hanteras av en enskild IT- eller utvecklingsresurs. [Elastiska pooler](sql-database-elastic-pool.md) stöder även SaaS program arkitekturer med flera innehavare med funktioner som klient isolering och möjlighet att skala för att minska kostnaderna genom att dela resurser mellan databaser. [Hanterad instans](sql-database-managed-instance.md) har stöd för instans omfattnings funktioner som möjliggör enkel migrering av befintliga program, samt delning av resurser mellan databaser.

| SQL-databaser | SQL-hanterade instanser | Virtuella SQL-datorer |
| :--- | :--- | :--- |
|Stöder de flesta lokala funktioner på databas nivå. De vanligaste SQL Server funktionerna är tillgängliga.<br/>99,995% tillgänglighet garanterad.<br/>Inbyggda säkerhets kopieringar, korrigeringar och återställning.<br/>Senaste versionen av stabil databas motor.<br/>Möjlighet att tilldela nödvändiga resurser (CPU/lagring) till enskilda databaser.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Ändring av resurser online (CPU/lagring).| Har stöd för nästan alla lokala funktioner på instans nivå och databas nivå. Hög kompatibilitet med SQL Server lokalt.<br/>99,99% tillgänglighet garanterad.<br/>Inbyggda säkerhets kopieringar, korrigeringar och återställning.<br/>Senaste versionen av stabil databas motor.<br/>Enkel migrering från SQL Server.<br/>Privat IP-adress i Azure VNet.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Ändring av resurser online (CPU/lagring).| Du har fullständig kontroll över SQL Servers motorn. Stöder alla lokala funktioner.<br/>Upp till 99,99% tillgänglighet.<br/>Fullständig paritet med den matchande versionen av lokala SQL Server.<br/>Fast och välkänd databas motor version.<br/>Enkel migrering från SQL Server lokalt.<br/>Privat IP-adress i Azure VNet.<br/>Du kan distribuera program eller tjänster på värden där SQL Server placeras.|
|Migrering från SQL Server kan vara svårt.<br/>Vissa SQL Server funktioner är inte tillgängliga.<br/>Ingen garanterad, exakt underhålls tid (men nästan transparent).<br/>Kompatibilitet med SQL Server-versionen kan bara uppnås med hjälp av kompatibilitetsnivån för databaser.<br/>Det går inte att tilldela en privat IP-adress (du kan begränsa åtkomsten med brand Väggs regler).|Det finns fortfarande ett minimalt antal SQL Server funktioner som inte är tillgängliga.<br/>Ingen garanterad, exakt underhålls tid (men nästan transparent).<br/>Kompatibilitet med SQL Server-versionen kan bara uppnås med hjälp av kompatibilitetsnivån för databaser.|Du måste hantera dina säkerhets kopieringar och uppdateringar.<br>Du måste implementera din egen lösning för hög tillgänglighet.<br/>Det finns ett avbrott när du ändrar resurser (CPU/lagring)|
| Databaser på upp till 100 TB. | Upp till 8 TB. | SQL Server instanser med upp till 256 TB lagrings utrymme. Instansen har stöd för så många databaser som behövs. |
| Lokala program kan komma åt data i Azure SQL Database. | [Inbyggd implementering av virtuella nätverk](sql-database-managed-instance-vnet-configuration.md) och anslutning till din lokala miljö med hjälp av Azure Express Route eller VPN gateway. | Med virtuella SQL-datorer kan du ha program som körs delvis i molnet och delvis lokalt. Du kan till exempel utöka ditt lokala nätverk och Active Directory-domän till molnet via [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Mer information om hybrid moln lösningar finns i [utöka lokala data lösningar till molnet](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |




## <a name="business-motivations-for-choosing-databases-managed-instances-or-sql-virtual-machines"></a>Affärs motivation för att välja databaser, hanterade instanser eller virtuella SQL-datorer

Det finns flera faktorer som kan påverka ditt beslut att välja mellan olika data erbjudanden:

- [Cost](#cost) – både PaaS-och IaaS-alternativet inkluderar bas priset som omfattar underliggande infrastruktur och licensiering. Men med alternativet IaaS måste du investera ytterligare tid och resurser för att hantera din databas, men i PaaS får du de här administrations funktionerna som ingår i priset. Med alternativet IaaS kan du stänga av dina resurser medan du inte använder dem för att minska kostnaderna, medan PaaS-versionen alltid körs om du tar bort och återskapar dina resurser när de behövs.
- [Administration](#administration) – PaaS alternativ minskar den tid som du måste investera för att administrera-databasen. Det begränsar dock också intervallet för anpassade administrations uppgifter och skript som du kan utföra eller köra. CLR stöds till exempel inte för databaser med enkel eller pool, men stöds för en hanterad instans. Dessutom stöder inga distributions alternativ i PaaS användningen av spårnings flaggor.
- [Service nivå avtal](#service-level-agreement-sla) – både IaaS och PaaS ger hög service avtal för bransch standard. Alternativet PaaS garanterar 99,99% SLA, medan IaaS garanterar 99,95% SLA for Infrastructure, vilket innebär att du måste implementera ytterligare mekanismer för att säkerställa tillgängligheten för dina databaser. Du kan implementera en lösning med hög tillgänglighet på 99,99% genom att skapa ytterligare SQL Server i VM och konfigurera AlwaysOn-tillgänglighetsgrupper.
- [Tiden för att flytta till Azure](#market) -SQL Server i Azure VM är den exakta matchningen av din miljö, så migreringen från lokala datorer till Azure SQL VM skiljer sig inte från att flytta databaserna från en lokal server till en annan. Hanterad instans möjliggör även mycket enkel migrering. Det kan dock finnas några ändringar som du måste tillämpa innan du migrerar till en hanterad instans.

Dessa faktorer beskrivs i mer information i följande avsnitt.

### <a name="cost"></a>Kostnad

Oavsett om du är en startup med dålig kassa, eller ett team i ett etablerat företag som har en begränsad budget. Budgetbegränsningar är vanligtvis den främsta motivationsfaktorn när man bestämmer var man ska ha sina databaser. I det här avsnittet får du lära dig mer om fakturerings-och licensierings grunderna i Azure med avseende på dessa två Relations databas alternativ: SQL Database och virtuella SQL-datorer. Du lär dig också hur du beräknar den totala programkostnaden.

#### <a name="billing-and-licensing-basics"></a>Debitering och licensiering

För närvarande säljs **SQL Database** som en tjänst och är tillgänglig med flera distributions alternativ och på flera tjänst nivåer med olika priser för resurser, som alla debiteras per timme i ett fast pris baserat på tjänst nivå och beräknings storlek som du väljer . Den senaste informationen om aktuella tjänst nivåer, beräknings storlek och lagrings utrymme som stöds finns i [DTU-baserad inköps modell](sql-database-service-tiers-dtu.md) och [vCore-baserad inköps modell](sql-database-service-tiers-vcore.md).

- Med SQL Database kan du välja en tjänst nivå som passar dina behov från en mängd olika priser med början från 5 $/månad för Basic-nivån.
- Du kan skapa [elastiska pooler](sql-database-elastic-pool.md) för att dela resurser mellan databas instanser för att minska kostnaderna och få användnings toppar.
- Med SQL-hanterad instans kan du också ta med din egen licens. Mer information om att använda en egen licensiering finns [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/) eller Använd [Azure Hybrid-förmån kalkylator](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) för att se hur du **sparar upp till 40%** .

Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/). Du kan justera tjänst nivåerna dynamiskt och beräknings storlekarna så att de överensstämmer med programmets varierande data flödes behov.

Med **SQL-databaser och SQL-hanterade instanser**konfigureras databas program varan automatiskt, korrigeras och uppgraderas av Microsoft, vilket minskar administrations kostnaderna. Dessutom gör dess [inbyggda säkerhetskopierings](sql-database-automated-backups.md)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser.

Med **virtuella SQL-datorer**kan du använda vilken plattform som helst SQL Server avbildningar (som innehåller en licens) eller ta SQL Server licensen. Alla SQL Server-versioner (2008R2, 2012, 2014, 2016) och utgåvor (Developer, Express, Web, Standard, Enterprise) som stöds är tillgängliga. Det finns också BYOL-versioner (Bring-Your-Own-License) av avbildningarna. När du använder de avbildningar som Azure tillhandahåller beror driftskostnaderna på storleken på de virtuella datorerna samt vilken utgåva av SQL Server du väljer. Oavsett storleken på virtuella datorer eller SQL Server Edition betalar du licens kostnaderna per minut för SQL Server och Windows-eller Linux-servern, tillsammans med Azure Storage kostnaden för de virtuella dator diskarna. Betalningsalternativet per minut låter dig använda SQL Server så länge du behöver utan att köpa ytterligare SQL Server-licenser. Om du använder din egen SQL Server-licens till Azure debiteras du bara för Server-och lagrings kostnader. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Beräkna den totala programkostnaden

När du börjar använda en moln plattform inkluderar kostnaden för att köra ditt program kostnaden för nya utvecklings-och pågående administrations kostnader samt kostnaderna för den offentliga moln tjänsten.

Mer information om priser finns i följande resurser:

- [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)
- [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) för [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) och för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

För många företag handlar beslutet att övergå till en molntjänst lika mycket om att minska administrativ komplexitet som kostnad. Med IaaS och PaaS, administrerar Microsoft den underliggande infrastrukturen och replikerar automatiskt alla data för att tillhandahålla haveri beredskap, konfigurera och uppgradera databas program vara, hanterar belastnings utjämning och transparent redundans om det finns en Server problem i ett Data Center.

- Med **SQL-databaser och SQL-hanterade instanser**kan du fortsätta att administrera din databas, men du behöver inte längre hantera databas motorn, operativ systemet eller maskin varan. Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet. Dessutom krävs minimal konfiguration och administration för att konfigurera hög tillgänglighet till ett annat data Center.
- Med **virtuella SQL-datorer**har du fullständig kontroll över operativ systemet och SQL Server instans konfiguration. Med en VM bestämmer du själv när operativsystem och databasprogramvara ska uppdateras/uppgraderas och när ytterligare programvara, t.ex. antivirusprogram, ska installeras. Vissa automatiska funktioner kan avsevärt underlätta arbetet med korrigeringar, säkerhetskopiering och hög tillgänglighet. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer. Med Azure kan du ändra storleken på en virtuell dator efter behov. Mer information finns i [Storlekar för virtuella Azure-datorer och Azure-molntjänster](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)

För många IT-avdelningar är det av högsta vikt att uppfylla skyldigheterna på upptid enligt serviceavtalet (SLA). I det här avsnittet tittar vi på de SLA som är tillämpliga för varje databasalternativ.

För **SQL Database**tillhandahåller Microsoft ett SLA för tillgänglighet på 99,99%. Den senaste informationen finns i [Serviceavtalet](https://azure.microsoft.com/support/legal/sla/sql-database/).

För **virtuella SQL-datorer**tillhandahåller Microsoft ett service avtal på 99,95% som bara täcker den virtuella datorn. Det här SLA:t omfattar inte de processer som körs på den virtuella datorn (till exempel SQL Server) som kräver att du är värd för minst två VM-instanser i en tillgänglighetsuppsättning. Den senaste informationen finns på [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För hög tillgänglighet för databasen (HA) i virtuella datorer bör du konfigurera en av de alternativ för hög tillgänglighet som stöds i SQL Server, till exempel [Always on-tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Användningen av ett alternativ för hög tillgänglighet som stöds medför inget nytt SLA, men ger > 99,99 % databastillgänglighet.

### <a name="market"></a>Tid att flytta till Azure

**SQL Database** (enstaka databaser eller elastiska pooler) är rätt lösning för molnbaserade program när produktiviteten för utvecklare och snabb tids till marknad för nya lösningar är viktiga. Med sin programmässiga DBA-lika funktionalitet, är det perfekt för molnarkitekter och utvecklare, eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen.

**SQL-hanterad instans** fören klar migreringen av befintliga program till Azure SQL avsevärt, så att du snabbt kan flytta migrerade databas program till marknaden i Azure.

**Virtuella SQL-datorer** är perfekta om dina befintliga eller nya program kräver stora databaser eller åtkomst till alla funktioner i SQL Server eller Windows/Linux, och du vill undvika tid och kostnader för att förvärva ny lokal maskin vara. Det är också en bra anpassning när du vill migrera befintliga lokala program och databaser till Azure som de är i fall där Azure SQL Database hanterade instansen inte passar bra. Eftersom du inte behöver ändra presentations-, program-och data lager sparar du tid och budget när du skapar en ny konstruktion för din befintliga lösning. Istället kan du fokusera på att migrera alla dina lösningar till Azure och på att genomföra prestandaoptimeringar som kan krävas av Azure-plattformen. Mer information finns i [Bästa praxis för prestanda i SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Nästa steg

- Se [din första Azure SQL-databas](sql-database-single-database-get-started.md) för att komma igång med SQL Database.
- Se [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Se [Etablera en virtuell SQL Server-dator i Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) för att komma igång med SQL Server på virtuella Azure-datorer.
- [Identifiera rätt SQL Database-eller SQL-hanterad instans-SKU för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).
