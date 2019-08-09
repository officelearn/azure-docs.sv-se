---
title: Välja bland SQL-alternativ i Azure | Microsoft Docs
description: Lär dig hur du väljer bland distributions alternativen i Azure SQL Database och mellan Azure SQL Database och SQL Server på virtuella Azure-datorer
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
ms.date: 03/11/2019
ms.openlocfilehash: fc0204e774e66e9304887f47377a102df1b828cb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884336"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Välj rätt distributions alternativ i Azure SQL

I Azure kan du ha SQL Server arbets belastningar som körs i en värdbaserad infrastruktur (IaaS) eller som körs som en värdbaserad tjänst ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). I PaaS har du flera distributions alternativ och tjänst nivåer inom varje distributions alternativ. Den viktiga fråga som du behöver fråga när du väljer mellan PaaS eller IaaS är att du vill hantera din databas, använda korrigeringar, göra säkerhets kopior eller vill delegera dessa åtgärder till Azure?
Beroende på svaret har du följande alternativ:

- [Azure SQL Database](sql-database-technical-overview.md): En fullständigt hanterad SQL Database-motor, baserat på den senaste stabila Enterprise-versionen av SQL Server. Det här är en Relations databas som en tjänst (DBaaS) som finns i Azure-molnet och som hamnar i kategorin *Platform-as-a-Service (PaaS)* . SQL Database har flera distributions alternativ som bygger på standardiserad maskin-och program vara som ägs, hanteras och underhålls av Microsoft. Med SQL Database kan du använda inbyggda funktioner och funktioner som kräver omfattande konfiguration när de används i SQL Server (antingen lokalt eller i en virtuell Azure-dator). När du använder SQL Database, betalar du per användning med alternativ att skala upp eller ut för mer kraft utan avbrott. SQL Database har ytterligare funktioner som inte är tillgängliga i SQL Server, till exempel inbyggd hög tillgänglighet, intelligens och hantering. Azure SQL Database erbjuder följande distributions alternativ:
  
  - Som en [enskild databas](sql-database-single-database.md) med en egen uppsättning resurser som hanteras via en SQL Database-Server. En enda databas liknar en innesluten [](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) databas i SQL Server. Det här alternativet är optimerat för modern program utveckling av nya molnbaserade program.
  - En [elastisk pool](sql-database-elastic-pool.md), som är en samling databaser med en delad uppsättning resurser som hanteras via en SQL Database-Server. Enskilda databaser kan flyttas till och från en elastisk pool. Det här alternativet är optimerat för modern program utveckling av nya molnbaserade program med hjälp av SaaS-programmet för flera innehavare.
  - [Hanterad instans](sql-database-managed-instance.md), som är en samling system-och användar databaser med en delad uppsättning resurser. En hanterad instans liknar en instans av [Microsoft SQL Server Database Engine] som erbjuder delade resurser för databaser och ytterligare funktioner för instans omfattning. Hanterad instans har stöd för migrering av databasen från lokala datorer med minimal till ingen databas ändring. Det här alternativet ger alla PaaS-förmåner för Azure SQL Database men lägger till funktioner som tidigare endast var tillgängliga i virtuella SQL-datorer. Detta inkluderar ett internt virtuellt nätverk (VNet) och nära 100%-kompatibilitet med lokala SQL Server.
- [SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) hamnar i bransch kategorin *Infrastructure-as-a-Service (IaaS)* och gör att du kan köra SQL Server i en fullständigt hanterad virtuell dator i Azure-molnet. [SQL Server virtuella datorer](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) körs också på standardiserad maskin vara som ägs, hanteras och underhålls av Microsoft. När du använder SQL Server på en virtuell dator kan du antingen betala per användning för en SQL Server licens som redan ingår i en SQL Server avbildning eller enkelt använda en befintlig licens. Du kan också stoppa eller återuppta den virtuella datorn efter behov. SQL Server installerat och finns i molnet på virtuella Windows Server-eller Linux-datorer (VM: ar) som körs på Azure, även kallat en infrastruktur som en tjänst (IaaS). SQL Server på Azure Virtual Machines är ett lämpligt alternativ för att migrera lokala SQL Server databaser och program utan någon databas ändring. Alla nya versioner och utgåvor av SQL Server är tillgängliga för installation på en virtuell IaaS-dator. Den viktigaste skillnaden från SQL Database är att SQL Server virtuella datorer tillåter fullständig kontroll över databas motorn. Du kan välja när du vill starta underhåll/korrigering, ändra återställnings modellen till enkel eller Mass utloggning, pausa eller starta tjänsten vid behov, och du kan anpassa den SQL Server databas motorn helt. Med den här extra kontrollen får du extra ansvar att hantera den virtuella datorn.

De huvudsakliga skillnaderna mellan dessa alternativ visas i följande tabell:

| SQL Server på virtuella datorer | Hanterad instans i SQL Database | Enkel databas/elastisk pool i SQL Database |
| --- | --- | --- |
|Du har fullständig kontroll över SQL Servers motorn.<br/>Upp till 99,99% tillgänglighet.<br/>Fullständig paritet med den matchande versionen av lokala SQL Server.<br/>Fast och välkänd databas motor version.<br/>Enkel migrering från SQL Server lokalt.<br/>Privat IP-adress i Azure VNet.<br/>Du kan distribuera program eller tjänster på värden där SQL Server placeras.| Hög kompatibilitet med SQL Server lokalt.<br/>99,99% tillgänglighet garanterad.<br/>Inbyggda säkerhets kopieringar, korrigeringar och återställning.<br/>Senaste versionen av stabil databas motor.<br/>Enkel migrering från SQL Server.<br/>Privat IP-adress i Azure VNet.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Ändring av resurser online (CPU/lagring).|De vanligaste SQL Server funktionerna är tillgängliga.<br/>99,99% tillgänglighet garanterad.<br/>Inbyggda säkerhets kopieringar, korrigeringar och återställning.<br/>Senaste versionen av stabil databas motor.<br/>Möjlighet att tilldela nödvändiga resurser (CPU/lagring) till enskilda databaser.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Ändring av resurser online (CPU/lagring).|
|Du måste hantera dina säkerhets kopieringar och uppdateringar.<br>Du måste implementera din egen lösning för hög tillgänglighet.<br/>Det finns ett avbrott när du ändrar resurser (CPU/lagring)|Det finns fortfarande ett minimalt antal SQL Server funktioner som inte är tillgängliga.<br/>Ingen garanterad, exakt underhålls tid (men nästan transparent).<br/>Kompatibilitet med SQL Server-versionen kan bara uppnås med hjälp av kompatibilitetsnivån för databaser.|Migrering från SQL Server kan vara svårt.<br/>Vissa SQL Server funktioner är inte tillgängliga.<br/>Ingen garanterad, exakt underhålls tid (men nästan transparent).<br/>Kompatibilitet med SQL Server-versionen kan bara uppnås med hjälp av kompatibilitetsnivån för databaser.<br/>Det går inte att tilldela en privat IP-adress (du kan begränsa åtkomsten med brand Väggs regler).|

Lär dig hur varje distributions alternativ passar in i Microsofts data plattform och få hjälp med att matcha rätt alternativ efter dina affärs behov. Oavsett om du prioriterar kostnadsbesparingar eller en minimering av administration över allt annat, kan den här artikeln hjälpa dig att välja det angreppssätt som ger bäst resultat för de verksamhetskrav som är viktigast för dig.

## <a name="microsofts-sql-data-platform"></a>Microsofts SQL data-plattform

En av de första sakerna man ska förstå när det gäller Azure kontra lokala SQL Server-databaser, är att det är möjligt att använda allt. Microsofts dataplattform utnyttjar SQL Server-teknologi och gör den tillgänglig på fysiska, lokala datorer, i privata molnmiljöer, tredjeparts privata molnmiljöer och det offentliga molnet. Med SQL Server på virtuella datorer i Azure kan du uppfylla unika och skilda affärsbehov genom en kombination av lokala och molnstyrda distributioner, samtidigt som du kan använda samma uppsättningar med serverprodukter, utvecklingsverktyg och expertis i alla dessa miljöer.

   ![Alternativ för moln SQL Server: SQL Server på IaaS eller SaaS SQL Database i molnet.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Som diagrammet visar, särskiljs de olika alternativen efter den administrativa nivån du har över infrastrukturen (på x-axeln) och efter den kostnadseffektivitet som kan uppnås av konsolidering på databasnivå och automatisering (på y-axeln).

När du skapar ett program, finns det fyra grundläggande alternativ för att hantera SQL Serverdelen av programmet:

- SQL Server på icke-virtualiserade fysiska datorer
- SQL Server på lokala virtualiserade datorer (privat moln)
- SQL Server på virtuella Azure-datorer (offentligt Microsoft-moln)
- Azure SQL Database (offentligt Microsoft-moln)

I följande avsnitt får du lära dig mer om SQL Server i det offentliga Microsoft-molnet: Azure SQL Database och SQL Server på virtuella Azure-datorer. Dessutom går vi igenom vanliga verksamhetsmotiv för att fastställa vilket alternativ som är bäst för ditt fall.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>En närmare titt på Azure SQL Database och SQL Server på virtuella Azure-datorer

Generellt sett är de här två SQL-alternativen optimerade för olika syften:

- **Azure SQL Database**

Optimerad för att minska de totala hanterings kostnaderna till minst för etablering och hantering av många databaser. Det minskar löpande administrativa kostnader eftersom du inte behöver hantera några virtuella datorer, operativsystem eller någon databasprogramvara. Du behöver inte hantera uppgraderingar, hög tillgänglighet eller [säkerhetskopieringar](sql-database-automated-backups.md). Generellt sett kan Azure SQL Database kraftigt öka antalet databaser som kan hanteras av en enskild IT- eller utvecklingsresurs. [Elastiska pooler](sql-database-elastic-pool.md) stöder även SaaS program arkitekturer med flera innehavare med funktioner som klient isolering och möjlighet att skala för att minska kostnaderna genom att dela resurser mellan databaser. [Hanterad instans](sql-database-managed-instance.md) har stöd för instans omfattnings funktioner som möjliggör enkel migrering av befintliga program, samt delning av resurser mellan databaser.

- **SQL Server som körs på virtuella Azure-datorer**

Optimerad för migrering av befintliga program till Azure eller för att utöka befintliga lokala program till molnet i hybrid distributioner. Du kan också använda SQL Server på en virtuell dator för att utveckla och testa traditionella SQL Server-program. Med SQL Server på virtuella Azure-datorer, har du fullständiga administrativa rättigheter för en dedikerad instans av SQL Server och en molnbaserad VM. Det är det perfekta valet när en organisation redan har IT-resurser tillgängliga för att underhålla de virtuella datorerna. Med dessa funktioner kan du skapa ett ytterst anpassat system för ditt programs specifika prestanda- och tillgänglighetsbehov.

Följande tabell sammanfattar de huvudsakliga egenskaperna för SQL Database och SQL Server på virtuella Azure-datorer:

| | SQL Database enkla databaser och elastiska pooler | SQL Database hanterade instanser |Virtuella Azure-datorer med SQL Server |
| --- | --- | --- |---|
| **Bäst för:** |Nya molnbaserade program som vill använda de senaste stabila SQL Server funktionerna och har tidsbegränsningar i utvecklings-och marknadsförings syfte. | Nya program eller befintliga lokala program som vill använda de senaste stabila SQL Server funktionerna och som migreras till molnet med minimala ändringar.  | Befintliga program som kräver snabb migrering till molnet med minimala ändringar eller inga ändringar. Snabba utvecklings- och test-scenarier där du inte vill köpa lokal SQL Server-maskinvara som inte är för produktion. |
|  | Team som behöver inbyggd hög tillgänglighet, haveriberedskap och uppgradering för databasen. | Samma som SQL Database enkla databaser och databaser i pooler. | Team som kan konfigurera, finjustera, anpassa och hantera hög tillgänglighet, haveri beredskap och korrigering för SQL Server. Vissa automatiserade funktioner kan avsevärt förenkla detta arbete. |
|  | Team som inte vill hantera de underliggande operativsystemen och konfigurationsinställningarna. | Samma som SQL Database enkla databaser och databaser i pooler. | Du behöver en anpassad miljö med fullständiga administrativa rättigheter. |
|  | Databaser på upp till 100 TB. | Upp till 8 TB. | SQL Server instanser med upp till 256 TB lagrings utrymme. Instansen har stöd för så många databaser som behövs. |
| **Kompatibilitetsläget** | Stöder de flesta lokala funktioner på databas nivå. | Har stöd för nästan alla lokala funktioner på instans nivå och databas nivå. | Stöder alla lokala funktioner. |
| **Resurser:** | Du vill inte använda IT-resurser för konfiguration och hantering av den underliggande infrastrukturen, men vill fokusera på program lagret. | Samma som SQL Database enkla databaser och databaser i pooler. | Du har några IT-resurser som ansvarar för konfiguration och hantering. Vissa automatiserade funktioner kan avsevärt förenkla detta arbete. |
| **Totalkostnad för ägarskap:** | Eliminerar kostnaderna för maskinvara och reducerar de administrativa kostnaderna. | Samma som SQL Database enkla databaser och databaser i pooler. | Eliminerar maskinvarukostnaderna. |
| **Verksamhetskontinuitet:** |Förutom inbyggda funktioner [för fel tolerans infrastruktur](sql-database-high-availability.md)tillhandahåller Azure SQL Database funktioner, till exempel [automatiserade säkerhets kopieringar](sql-database-automated-backups.md), tidpunkts [återställning](sql-database-recovery-using-backups.md#point-in-time-restore), [geo-återställning](sql-database-recovery-using-backups.md#geo-restore), [aktiv geo-replikering](sql-database-active-geo-replication.md)och [ Grupper för automatisk redundans](sql-database-auto-failover-group.md) för att öka affärs kontinuiteten. Mer information finns i [översikt över verksamhetskontinuitet i SQL Database](sql-database-business-continuity.md). | Samma som SQL Database enkla databaser och grupperade databaser, samt användardefinierade säkerhets kopieringar är tillgängliga. | Med SQL Server på Azure Virtual Machines kan du konfigurera en lösning med hög tillgänglighet och haveriberedskap för din databas specifika behov. Du kan därmed få ett system som är höggradigt optimerat för just ditt program. Du kan själv testa och köra felväxling vid behov. Mer information finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Hybridmoln:** |Ditt lokala program har åtkomst till data i Azure SQL Database. | [Inbyggd implementering av virtuella nätverk](sql-database-managed-instance-vnet-configuration.md) och anslutning till din lokala miljö med hjälp av Azure Express Route eller VPN gateway. | Med SQL Server på virtuella Azure-datorer kan du ha program som kör delvis i molnet och delvis lokalt. Du kan till exempel utöka ditt lokala nätverk och Active Directory-domän till molnet via [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Mer information om hybrid moln lösningar finns i [utöka lokala data lösningar till molnet](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Har stöd för [SQL Server-transaktionsreplikering](https://msdn.microsoft.com/library/mt589530.aspx) som en prenumerant för att replikera data. | Replikering stöds för hanterade instanser som en för hands versions funktion. | Har fullt stöd för [SQL Server](https://msdn.microsoft.com/library/mt589530.aspx)Transaktionsreplikering, [Always on-tillgänglighetsgrupper](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services och logg överföring för att replikera data. Dessutom finns fullständigt stöd för traditionella SQL Server-säkerhetskopieringar |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Verksamhetsmotivering för att välja Azure SQL Database eller SQL Server på virtuella Azure-datorer

Det finns flera faktorer som kan påverka ditt beslut att välja PaaS eller IaaS för att vara värd för dina SQL-databaser:

- [Cost](#cost) – både PaaS-och IaaS-alternativet inkluderar bas priset som omfattar underliggande infrastruktur och licensiering. Men med alternativet IaaS måste du investera ytterligare tid och resurser för att hantera din databas, men i PaaS får du de här administrations funktionerna som ingår i priset. Med alternativet IaaS kan du stänga av dina resurser medan du inte använder dem för att minska kostnaderna, medan PaaS-versionen alltid körs om du tar bort och återskapar dina resurser när de behövs.
- [Administration](#administration) – PaaS alternativ minskar den tid som du måste investera för att administrera-databasen. Det begränsar dock också intervallet för anpassade administrations uppgifter och skript som du kan utföra eller köra. CLR stöds till exempel inte för databaser med enkel eller pool, men stöds för en hanterad instans. Dessutom stöder inga distributions alternativ i PaaS användningen av spårnings flaggor.
- [Service nivå avtal](#service-level-agreement-sla) – både IaaS och PaaS ger hög service avtal för bransch standard. Alternativet PaaS garanterar 99,99% SLA, medan IaaS garanterar 99,95% SLA for Infrastructure, vilket innebär att du måste implementera ytterligare mekanismer för att säkerställa tillgängligheten för dina databaser. Du kan implementera en lösning med hög tillgänglighet på 99,99% genom att skapa ytterligare SQL Server i VM och konfigurera AlwaysOn-tillgänglighetsgrupper.
- [Tiden för att flytta till Azure](#market) -SQL Server i Azure VM är den exakta matchningen av din miljö, så migreringen från lokala datorer till Azure SQL VM skiljer sig inte från att flytta databaserna från en lokal server till en annan. Hanterad instans möjliggör även mycket enkel migrering. Det kan dock finnas några ändringar som du måste tillämpa innan du migrerar till en hanterad instans.

Dessa faktorer beskrivs i mer information i följande avsnitt.

### <a name="cost"></a>Kostnad

Oavsett om du är en startup med dålig kassa, eller ett team i ett etablerat företag som har en begränsad budget. Budgetbegränsningar är vanligtvis den främsta motivationsfaktorn när man bestämmer var man ska ha sina databaser. I det här avsnittet får du lära dig mer om fakturerings-och licensierings grunderna i Azure med avseende på dessa två Relations databas alternativ: SQL Database och SQL Server på virtuella Azure-datorer. Du lär dig också hur du beräknar den totala programkostnaden.

#### <a name="billing-and-licensing-basics"></a>Debitering och licensiering

För närvarande säljs **SQL Database** som en tjänst och är tillgänglig med flera distributions alternativ och på flera tjänst nivåer med olika priser för resurser, som alla debiteras per timme i ett fast pris baserat på tjänst nivå och beräknings storlek som du väljer . Den senaste informationen om aktuella tjänst nivåer, beräknings storlek och lagrings utrymme som stöds finns i [DTU-baserad inköps modell](sql-database-service-tiers-dtu.md) och [vCore-baserad inköps modell](sql-database-service-tiers-vcore.md).

- Med SQL Database enskild databas kan du välja en tjänst nivå som passar dina behov från en mängd olika priser med början från 5 $/månad för Basic-nivån.
- Du kan skapa [elastiska pooler](sql-database-elastic-pool.md) för att dela resurser mellan databas instanser för att minska kostnaderna och få användnings toppar.
- Med SQL Database Hanterad instans kan du också ta med din egen licens. Mer information om att använda en egen licensiering finns [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/) eller Använd [Azure Hybrid-förmån kalkylator](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) för att se hur du **sparar upp till 40%** .

Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/). Du kan justera tjänst nivåerna dynamiskt och beräknings storlekarna så att de överensstämmer med programmets varierande data flödes behov.

Med **SQL Database** så konfigureras, korrigeras och uppgraderas databasens programvara automatiskt av Microsoft, vilket minskar dina administrationskostnader. Dessutom gör dess [inbyggda säkerhetskopierings](sql-database-automated-backups.md)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser.

Med **SQL Server på Azure Virtual Machines** kan du använda valfri SQL Server-avbildning som plattformen stöder (som omfattar en licens) eller använda din egen SQL Server-licens. Alla SQL Server-versioner (2008R2, 2012, 2014, 2016) och utgåvor (Developer, Express, Web, Standard, Enterprise) som stöds är tillgängliga. Det finns också BYOL-versioner (Bring-Your-Own-License) av avbildningarna. När du använder de avbildningar som Azure tillhandahåller beror driftskostnaderna på storleken på de virtuella datorerna samt vilken utgåva av SQL Server du väljer. Oavsett storleken på virtuella datorer eller SQL Server Edition betalar du licens kostnaderna per minut för SQL Server och Windows-eller Linux-servern, tillsammans med Azure Storage kostnaden för de virtuella dator diskarna. Betalningsalternativet per minut låter dig använda SQL Server så länge du behöver utan att köpa ytterligare SQL Server-licenser. Om du använder din egen SQL Server-licens till Azure debiteras du bara för Server-och lagrings kostnader. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Beräkna den totala programkostnaden

När du börjar använda en moln plattform inkluderar kostnaden för att köra ditt program kostnaden för nya utvecklings-och pågående administrations kostnader samt kostnaderna för den offentliga moln tjänsten.

**Med Azure SQL Database:**

- Mycket minimerade administrations kostnader
- Begränsade utvecklings kostnader för migrerade program (hanterade instanser)
- SQL Database tjänste kostnader
- Inga kostnader för maskin varu inköp

**När du använder SQL Server på virtuella Azure-datorer:**

- Högre administrations kostnader
- Begränsat till inga utvecklings kostnader för migrerade program
- Kostnader för virtuell dator tjänst
- Inga kostnader för maskin varu inköp

Mer information om priser finns i följande resurser:

- [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)
- [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) för [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) och för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

För många företag handlar beslutet att övergå till en molntjänst lika mycket om att minska administrativ komplexitet som kostnad. Med IaaS och PaaS, administrerar Microsoft den underliggande infrastrukturen och replikerar automatiskt alla data för att tillhandahålla haveri beredskap, konfigurera och uppgradera databas program vara, hanterar belastnings utjämning och transparent redundans om det finns en Server problem i ett Data Center.

- Med **Azure SQL Database**kan du fortsätta att administrera din databas, men du behöver inte längre hantera databas motorn, operativ systemet eller maskin varan. Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet. Dessutom krävs minimal konfiguration och administration för att konfigurera hög tillgänglighet till ett annat data Center.
- Med **SQL Server på Azure Virtual Machines** har du fullständig kontroll över operativsystemet och konfigurationen av SQL Server-instansen. Med en VM bestämmer du själv när operativsystem och databasprogramvara ska uppdateras/uppgraderas och när ytterligare programvara, t.ex. antivirusprogram, ska installeras. Vissa automatiska funktioner kan avsevärt underlätta arbetet med korrigeringar, säkerhetskopiering och hög tillgänglighet. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer. Med Azure kan du ändra storleken på en virtuell dator efter behov. Mer information finns i [Storlekar för virtuella Azure-datorer och Azure-molntjänster](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)

För många IT-avdelningar är det av högsta vikt att uppfylla skyldigheterna på upptid enligt serviceavtalet (SLA). I det här avsnittet tittar vi på de SLA som är tillämpliga för varje databasalternativ.

För **SQL Database**tillhandahåller Microsoft ett SLA för tillgänglighet på 99,99%. Den senaste informationen finns i [Serviceavtalet](https://azure.microsoft.com/support/legal/sla/sql-database/).

För **SQL Server som kör på virtuella Azure-datorer** erbjuder Microsoft en tillgänglighets-SLA på 99,95 %, vilket bara täcker den virtuella datorn. Det här SLA:t omfattar inte de processer som körs på den virtuella datorn (till exempel SQL Server) som kräver att du är värd för minst två VM-instanser i en tillgänglighetsuppsättning. Den senaste informationen finns på [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För hög tillgänglighet för databasen (HA) i virtuella datorer bör du konfigurera en av de alternativ för hög tillgänglighet som stöds i SQL Server, till exempel [Always on-tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Användningen av ett alternativ för hög tillgänglighet som stöds medför inget nytt SLA, men ger > 99,99 % databastillgänglighet.

### <a name="market"></a>Tid att flytta till Azure

**SQL Database enkla databaser eller elastiska pooler** är rätt lösning för molnbaserade program när produktiviteten för utvecklare och snabb tids-till-marknad för nya lösningar är viktiga. Med sin programmässiga DBA-lika funktionalitet, är det perfekt för molnarkitekter och utvecklare, eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen.

**SQL Database hanterade instanser** fören klar migreringen av befintliga program till Azure SQL Database, så att du snabbt kan ta över migrerade databas program till marknaden i Azure.

**SQL Server som körs på virtuella Azure-datorer** är perfekt om dina befintliga eller nya program kräver stora databaser eller åtkomst till alla funktioner i SQL Server eller Windows/Linux, och du vill undvika tid och kostnader för att förvärva ny lokal maskin vara. Det är också en bra anpassning när du vill migrera befintliga lokala program och databaser till Azure som de är i fall där Azure SQL Database hanterade instansen inte passar bra. Eftersom du inte behöver ändra presentations-, program-och data lager sparar du tid och budget när du skapar en ny konstruktion för din befintliga lösning. Istället kan du fokusera på att migrera alla dina lösningar till Azure och på att genomföra prestandaoptimeringar som kan krävas av Azure-plattformen. Mer information finns i [Bästa praxis för prestanda i SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Nästa steg

- Se [Din första Azure SQL-databas](sql-database-single-database-get-started.md) för att komma igång med SQL Database.
- Se [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Se [Etablera en virtuell SQL Server-dator i Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) för att komma igång med SQL Server på virtuella Azure-datorer.
- [Identifiera den högra Azure SQL Database/hanterade instans-SKU: n för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).
