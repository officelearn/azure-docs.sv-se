---
title: Välj rätt distributionsalternativ
description: Lär dig hur du väljer bland distributionsalternativen i Azure SQL mellan SQL-databaser, SQL-hanterade instanser och SQL Server på virtuella Azure-datorer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821352"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Välj rätt distributionsalternativ i Azure SQL

Lär dig hur varje distributionsalternativ passar in i Microsofts Azure SQL-dataplattform och få hjälp med att matcha rätt alternativ för dina affärskrav. Oavsett om du prioriterar kostnadsbesparingar eller minimal administration kan den här artikeln hjälpa dig att avgöra vilken metod som levererar mot de affärskrav du bryr dig mest om.

## <a name="microsofts-azure-sql-data-platform"></a>Microsofts Azure SQL-dataplattform

Azure SQL är en modern SQL-plattform som erbjuder flera distributionsalternativ som drivs av en branschledande Microsoft SQL Server-motor, från övergångstillvarpning och modernisering av befintliga program till att bygga moderna molntjänster. Azure SQL är utformat för att stödja en mängd olika programmönster med olika nivåer av kontroll över den underliggande plattformen för att uppfylla de mest krävande migrerings- och moderniseringskraven. Azure SQL eliminerar komplexiteten i att hantera olika samlingar av SQL Server-baserade program i stor skala genom att tillhandahålla en enda enhetlig hanteringsupplevelse.

En av de första sakerna man ska förstå när det gäller Azure kontra lokala SQL Server-databaser, är att det är möjligt att använda allt. Microsofts dataplattform utnyttjar SQL Server-teknik och gör den tillgänglig på fysiska lokala datorer, privata molnmiljöer, privata molnmiljöer från tredje part och det offentliga molnet. Med SQL Server på virtuella Azure-datorer (virtuella SQL-datorer) kan du uppfylla unika och mångsidiga affärsbehov genom en kombination av lokala och molnbaserade distributioner, samtidigt som du använder samma uppsättning serverprodukter, utvecklingsverktyg och expertis i dessa miljöer.

   ![SQL Server-molnalternativ: SQL-server på IaaS, eller SaaS-SQL-databas i molnet.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Som framgår av diagrammet kan varje erbjudande kännetecknas av den administrationsnivå du har över infrastrukturen och av graden av kostnadseffektivitet.

I Azure kan du låta dina SQL Server-arbetsbelastningar köras som en värdtjänst ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) eller en värdinfrastruktur ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). Inom PaaS har du flera distributionsalternativ och tjänstnivåer inom varje distributionsalternativ. Den viktigaste frågan som du behöver ställa när du bestämmer mellan PaaS eller IaaS är vill du hantera databasen, tillämpa korrigeringar och ta säkerhetskopior eller vill du delegera dessa åtgärder till Azure?

Beroende på svaret har du följande alternativ:

- [**SQL-databaser:**](sql-database-technical-overview.md)Bäst för moderna molnprogram som vill använda de senaste stabila SQL Server-funktionerna och har tidsbegränsningar i utveckling och marknadsföring. En fullständigt hanterad SQL-databasmotor, baserad på den senaste stabila Enterprise Edition av SQL Server. Detta är en relationell databas-som-en-tjänst (DBaaS) som finns i Azure-molnet som tillhör branschkategorin *Platform-as-a-Service (PaaS)*. SQL-databasen har flera distributionsalternativ, som alla bygger på standardiserad maskinvara och programvara som ägs, finns värd och underhålls av Microsoft. Med SQL Server kan du använda inbyggda funktioner som kräver omfattande konfiguration (antingen lokalt eller i en virtuell Azure-dator). När du använder SQL Database, betalar du per användning med alternativ att skala upp eller ut för mer kraft utan avbrott. SQL Database har några ytterligare funktioner som inte är tillgängliga i SQL Server, till exempel inbyggd hög tillgänglighet, intelligens och hantering.


  Databaser erbjuder följande distributionsalternativ:
  - Som en [***enda databas***](sql-database-single-database.md) med sin egen uppsättning resurser som hanteras via en databasserver. En enskild databas liknar en [innesluten databas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) i SQL Server. Det här alternativet är optimerat för modern applikationsutveckling av nya molnfödda program. [Hyperskala](sql-database-service-tier-hyperscale.md) och [serverlösa](sql-database-serverless.md) alternativ är tillgängliga.
  - En [***elastisk pool***](sql-database-elastic-pool.md), som är en samling databaser med en delad uppsättning resurser som hanteras via en databasserver. Enskilda databaser kan flyttas till och från en elastisk pool. Det här alternativet är optimerat för modern programutveckling av nya molnfödda program med hjälp av saas-programmönstret för flera innehavare. Elastiska pooler ger en kostnadseffektiv lösning för att hantera prestanda för flera databaser som har variabla användningsmönster.
  - En [***databasserver***](sql-database-servers.md)som används för att hantera grupper av enskilda databaser och elastiska pooler. Databasservrar fungerar som en central administrativ punkt för flera enskilda eller poolade databaser, [inloggningar,](sql-database-manage-logins.md) [brandväggsregler,](sql-database-firewall-configure.md) [granskningsregler,](sql-database-auditing.md) [principer för hotidentifiering](sql-database-threat-detection.md)och [redundansgrupper](sql-database-auto-failover-group.md).

- [**SQL-hanterade instanser**](sql-database-managed-instance.md): Bäst för de flesta migreringar till molnet. Hanterad instans är en samling system- och användardatabaser med en delad uppsättning resurser som är redo för lyft och skift. Bäst för nya program eller befintliga lokala program som vill använda de senaste stabila SQL Server-funktionerna och som migreras till molnet med minimala ändringar. En hanterad instans liknar en instans av [Microsoft SQL Server-databasmotorn](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) som erbjuder delade resurser för databaser och ytterligare instansomfattade funktioner. Hanterad instans stöder databasmigrering från lokalt med minimal till ingen databasändring. Det här alternativet innehåller alla PaaS-fördelar med Azure SQL Database men lägger till funktioner som tidigare bara var tillgängliga i virtuella SQL-datorer. Detta inkluderar ett inbyggt virtuellt nätverk (VNet) och nära 100 % kompatibilitet med lokal SQL Server. Hanterade instanser ger fullständig SQL Server-åtkomst och funktionskompatibilitet för att migrera SQL-servrar till Azure.


- [**Virtuella SQL-datorer:**](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)Bäst för migreringar och program som kräver åtkomst på OS-nivå. Virtuella SQL-datorer är lift-and-shift redo för befintliga program som kräver snabb migrering till molnet med minimala ändringar eller inga ändringar. Virtuella SQL-datorer ger fullständig administrativ kontroll över SQL Server-instansen och underliggande operativsystem för migrering till Azure. Snabba utvecklings- och test-scenarier där du inte vill köpa lokal SQL Server-maskinvara som inte är för produktion. Virtuella SQL-datorer tillhör branschkategorin *Infrastructure-as-a-Service (IaaS)* och låter dig köra SQL Server i en fullständigt hanterad virtuell dator (VM) i Azure-molnet. Virtuella SQL-datorer körs också på standardiserad maskinvara som ägs, är värd och underhålls av Microsoft. När du använder virtuella SQL-datorer kan du antingen betala per användning för en SQL Server-licens som redan ingår i en SQL Server-avbildning eller enkelt använda en befintlig licens. Du kan också stoppa eller återuppta den virtuella datorn efter behov. SQL Server installeras och finns i molnet körs på Windows Server eller Linux virtuella datorer som körs på Azure, även känd som en infrastruktur som en tjänst (IaaS). Virtuella SQL-datorer är ett bra alternativ för att migrera lokala SQL Server-databaser och -program utan någon databasändring. Alla senaste versioner och utgåvor av SQL Server är tillgängliga för installation i en virtuell IaaS-dator. Den största skillnaden från SQL-databaser och SQL-hanterade instanser är att virtuella SQL Server-datorer ger fullständig kontroll över databasmotorn. Du kan välja när du vill starta underhåll/korrigering, ändra återställningsmodellen till enkel eller massloggad, pausa eller starta tjänsten när det behövs, och du kan anpassa SQL Server-databasmotorn helt. Med denna extra kontroll kommer det extra ansvaret för att hantera den virtuella datorn.

  Optimerad för att migrera befintliga program till Azure eller utöka befintliga lokala program till molnet i hybriddistributioner. Du kan också använda SQL Server på en virtuell dator för att utveckla och testa traditionella SQL Server-program. Med virtuella SQL-datorer har du fullständiga administrativa rättigheter via en dedikerad SQL Server-instans och en molnbaserad virtuell dator. Det är det perfekta valet när en organisation redan har IT-resurser tillgängliga för att underhålla de virtuella datorerna. Med dessa funktioner kan du skapa ett ytterst anpassat system för ditt programs specifika prestanda- och tillgänglighetsbehov.


Ytterligare skillnader visas i följande tabell, men ***både databaser och hanterade instanser är optimerade för att minska de totala hanteringskostnaderna till ett minimum för etablering och hantering av många databaser.*** Det minskar löpande administrativa kostnader eftersom du inte behöver hantera några virtuella datorer, operativsystem eller någon databasprogramvara. Du behöver inte hantera uppgraderingar, hög tillgänglighet eller [säkerhetskopieringar](sql-database-automated-backups.md). Generellt sett kan Azure SQL Database kraftigt öka antalet databaser som kan hanteras av en enskild IT- eller utvecklingsresurs. [Elastiska pooler](sql-database-elastic-pool.md) stöder också Programarkitekturer för Flera innehavare med funktioner som klientisolering och möjligheten att skala för att minska kostnaderna genom att dela resurser mellan databaser. [Hanterad instans](sql-database-managed-instance.md) ger stöd för exempel-scoped funktioner som möjliggör enkel migrering av befintliga program, samt dela resurser mellan databaser.

| SQL-databaser | SQL-hanterade instanser | Virtuella SQL-datorer |
| :--- | :--- | :--- |
|Stöder de flesta lokala funktioner på databasnivå. De vanligaste SQL Server-funktionerna är tillgängliga.<br/>99.995% tillgänglighet garanteras.<br/>Inbyggda säkerhetskopior, korrigering, återställning.<br/>Senaste stabila Databasmotor version.<br/>Möjlighet att tilldela nödvändiga resurser (CPU/lagring) till enskilda databaser.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Onlineändring av resurser (CPU/lagring).| Stöder nästan alla lokala funktioner på instansnivå och databasnivå. Hög kompatibilitet med SQL Server lokalt.<br/>99.99% tillgänglighet garanteras.<br/>Inbyggda säkerhetskopior, korrigering, återställning.<br/>Senaste stabila Databasmotor version.<br/>Enkel migrering från SQL Server.<br/>Privat IP-adress i Azure VNet.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Onlineändring av resurser (CPU/lagring).| Du har full kontroll över SQL Server-motorn. Stöder alla lokala funktioner.<br/>Upp till 99,99 % tillgänglighet.<br/>Fullständig paritet med den matchande versionen av lokal SQL Server.<br/>Fast, välkänd databasmotorversion.<br/>Enkel migrering från SQL Server lokalt.<br/>Privat IP-adress i Azure VNet.<br/>Du har möjlighet att distribuera program eller tjänster på värden där SQL Server är placerad.|
|Migrering från SQL Server kan vara svår.<br/>Vissa SQL Server-funktioner är inte tillgängliga.<br/>Ingen garanterad exakt underhållstid (men nästan transparent).<br/>Kompatibilitet med SQL Server-versionen kan endast uppnås med hjälp av databaskompatibilitetsnivåer.<br/>Det går inte att tilldela privat IP-adress (du kan begränsa åtkomsten med hjälp av brandväggsregler).|Det finns fortfarande ett minimalt antal SQL Server-funktioner som inte är tillgängliga.<br/>Ingen garanterad exakt underhållstid (men nästan transparent).<br/>Kompatibilitet med SQL Server-versionen kan endast uppnås med hjälp av databaskompatibilitetsnivåer.|Du måste hantera dina säkerhetskopior och patchar.<br>Du måste implementera din egen lösning med hög tillgänglighet.<br/>Det finns ett driftstopp när resurserna (CPU/lagring) ändras.There is a downtime while changing the resources(CPU/storage)|
| Databaser på upp till 100 TB. | Upp till 8 TB. | SQL Server-instanser med upp till 256 TB lagringsutrymme. Instansen har stöd för så många databaser som behövs. |
| Lokalt program kan komma åt data i Azure SQL Database. | [Inbyggd implementering](sql-database-managed-instance-vnet-configuration.md) av virtuella nätverk och anslutning till din lokala miljö med Azure Express Route eller VPN Gateway. | Med virtuella SQL-datorer kan du ha program som körs dels i molnet och delvis lokalt. Du kan till exempel utöka ditt lokala nätverk och Active Directory-domän till molnet via [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Mer information om hybridmolnlösningar finns i [Utöka lokala datalösningar till molnet](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |




## <a name="business-motivations-for-choosing-databases-managed-instances-or-sql-virtual-machines"></a>Affärsmotiv för att välja databaser, hanterade instanser eller virtuella SQL-datorer

Det finns flera faktorer som kan påverka ditt beslut att välja mellan de olika dataerbjudandena:

- [Kostnad](#cost) - Både PaaS och IaaS alternativ inkluderar baspris som täcker underliggande infrastruktur och licensiering. Men med IaaS alternativ måste du investera ytterligare tid och resurser för att hantera din databas, medan i PaaS du får dessa administrationsfunktioner ingår i priset. IaaS alternativet kan du stänga av dina resurser medan du inte använder dem för att minska kostnaden, medan PaaS version är alltid igång om du inte släpper och återskapa dina resurser när de behövs.
- [Administration](#administration) - PaaS-alternativ minskar den tid som du behöver investera för att administrera databasen. Men det begränsar också intervallet för anpassade administrationsuppgifter och skript som du kan utföra eller köra. CLR stöds till exempel inte med enstaka eller poolade databaser, men stöds för en hanterad instans. Dessutom stöder inga distributionsalternativ i PaaS användningen av spårningsflaggor.
- [Servicenivåavtal](#service-level-agreement-sla) - Både IaaS och PaaS ger ett sla-sla med hög branschstandard. PaaS-alternativet garanterar 99,99 % SLA, medan IaaS garanterar 99,95 % serviceavtal för infrastruktur, vilket innebär att du måste implementera ytterligare mekanismer för att säkerställa tillgängligheten för dina databaser. Du kan implementera lösning med hög tillgänglighet på 99,99 % genom att skapa ytterligare en SQL Server i vm och konfigurera AlwaysOn-tillgänglighetsgrupper.
- [Dags att flytta till Azure](#market) - SQL Server i Azure VM är den exakta matchningen av din miljö, så migrering från lokalt till Azure SQL VM skiljer sig inte än att flytta databaserna från en lokal server till en annan. Hanterad instans möjliggör också extremt enkel migrering. Det kan dock finnas vissa ändringar som du måste använda innan du migrerar till en hanterad instans.

Dessa faktorer kommer att diskuteras mer detaljer i följande avsnitt.

### <a name="cost"></a>Kostnad

Oavsett om du är en startup med dålig kassa, eller ett team i ett etablerat företag som har en begränsad budget. Budgetbegränsningar är vanligtvis den främsta motivationsfaktorn när man bestämmer var man ska ha sina databaser. I det här avsnittet får du lära dig mer om grunderna för fakturering och licensiering i Azure när det gäller dessa två relationsdatabasalternativ: SQL Database och virtuella SQL-datorer. Du lär dig också hur du beräknar den totala programkostnaden.

#### <a name="billing-and-licensing-basics"></a>Debitering och licensiering

För närvarande säljs **SQL Database** som en tjänst och är tillgänglig med flera distributionsalternativ och på flera tjänstnivåer med olika priser för resurser, som alla faktureras per timme till en fast kurs baserat på den tjänstnivå och beräkningsstorlek du väljer. Den senaste informationen om de aktuella tjänstnivåerna, beräkningsstorlekarna och lagringsbeloppen finns i [DTU-baserad inköpsmodell](sql-database-service-tiers-dtu.md) och [vCore-baserad inköpsmodell](sql-database-service-tiers-vcore.md).

- Med SQL-databas kan du välja en tjänstnivå som passar dina behov från ett brett utbud av priser från 5$/månad för grundläggande nivå.
- Du kan skapa [elastiska pooler](sql-database-elastic-pool.md) för att dela resurser mellan databasinstanser för att minska kostnaderna och hantera användningstoppar.
- Med SQL-hanterad instans kan du också ta med din egen licens. Mer information om bring-your-own-licensiering finns i [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/) eller använd Azure Hybrid [Benefit-kalkylator för](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) att se hur du sparar upp till **40 %**.

Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/). Du kan justera tjänstnivåer och beräkningsstorlekar dynamiskt så att de matchar programmets olika dataflödesbehov.

Med **SQL-databaser och SQL-hanterade instanser**konfigureras, korrigeras och uppgraderas databasprogramvaran automatiskt av Microsoft, vilket minskar administrationskostnaderna. Dessutom gör dess [inbyggda säkerhetskopierings](sql-database-automated-backups.md)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser.

Med **virtuella SQL-datorer**kan du använda någon av de plattformsbaserade SQL Server-avbildningarna (som innehåller en licens) eller ta med sql server-licensen. Alla SQL Server-versioner (2008R2, 2012, 2014, 2016) och utgåvor (Developer, Express, Web, Standard, Enterprise) som stöds är tillgängliga. Det finns också BYOL-versioner (Bring-Your-Own-License) av avbildningarna. När du använder de avbildningar som Azure tillhandahåller beror driftskostnaderna på storleken på de virtuella datorerna samt vilken utgåva av SQL Server du väljer. Oavsett VM-storlek eller SQL Server-utgåva betalar du licenskostnaden per minut för SQL Server och Windows eller Linux Server, tillsammans med Azure Storage-kostnaden för VM-diskarna. Betalningsalternativet per minut låter dig använda SQL Server så länge du behöver utan att köpa ytterligare SQL Server-licenser. Om du tar med din egen SQL Server-licens till Azure debiteras du endast för server- och lagringskostnader. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Beräkna den totala programkostnaden

När du börjar använda en molnplattform inkluderar kostnaden för att köra ditt program kostnaden för nyutveckling och löpande administrationskostnader, plus de offentliga kostnaderna för molnplattformstjänsten.

Mer information om priser finns i följande resurser:

- [Priser för SQL-databas](https://azure.microsoft.com/pricing/details/sql-database/)
- [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) för [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

För många företag handlar beslutet att övergå till en molntjänst lika mycket om att minska administrativ komplexitet som kostnad. Med IaaS och PaaS administrerar Microsoft den underliggande infrastrukturen och replikerar automatiskt alla data för att tillhandahålla haveriberedskap, konfigurerar och uppgraderar databasprogramvaran, hanterar belastningsutjämning och gör en genomskinlig redundans om det finns en serverfel i ett datacenter.

- Med **SQL-databaser och SQL-hanterade instanser**kan du fortsätta att administrera databasen, men du behöver inte längre hantera databasmotorn, operativsystemet eller maskinvaran. Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet. Dessutom kräver konfiguration av hög tillgänglighet till ett annat datacenter minimal konfiguration och administration.
- Med **virtuella SQL-datorer**har du full kontroll över konfigurationen av operativsystemet och SQL Server-instansen. Med en VM bestämmer du själv när operativsystem och databasprogramvara ska uppdateras/uppgraderas och när ytterligare programvara, t.ex. antivirusprogram, ska installeras. Vissa automatiska funktioner kan avsevärt underlätta arbetet med korrigeringar, säkerhetskopiering och hög tillgänglighet. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer. Med Azure kan du ändra storleken på en virtuell dator efter behov. Mer information finns i [Storlekar för virtuella Azure-datorer och Azure-molntjänster](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)

För många IT-avdelningar är det av högsta vikt att uppfylla skyldigheterna på upptid enligt serviceavtalet (SLA). I det här avsnittet tittar vi på de SLA som är tillämpliga för varje databasalternativ.

För **SQL Database**tillhandahåller Microsoft ett serviceavtal för tillgänglighet på 99,99 %. Den senaste informationen finns i [Serviceavtalet](https://azure.microsoft.com/support/legal/sla/sql-database/).

För **virtuella SQL-datorer**tillhandahåller Microsoft ett SLA-anslutnings-serviceavtal med 99,95 % som bara täcker den virtuella datorn. Det här SLA:t omfattar inte de processer som körs på den virtuella datorn (till exempel SQL Server) som kräver att du är värd för minst två VM-instanser i en tillgänglighetsuppsättning. Den senaste informationen finns på [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För databasens hög tillgänglighet (HA) inom virtuella datorer bör du konfigurera ett av de alternativ för hög tillgänglighet som stöds i SQL Server, till exempel [Alltid på tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Användningen av ett alternativ för hög tillgänglighet som stöds medför inget nytt SLA, men ger > 99,99 % databastillgänglighet.

### <a name="time-to-move-to-azure"></a><a name="market"></a>Dags att flytta till Azure

**SQL-databas** (enskilda databaser eller elastiska pooler) är rätt lösning för molndesignade program när utvecklarproduktivitet och snabb time-to-market för nya lösningar är avgörande. Med sin programmässiga DBA-lika funktionalitet, är det perfekt för molnarkitekter och utvecklare, eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen.

**SQL-hanterad instans** förenklar avsevärt migreringen av befintliga program till Azure SQL, så att du snabbt kan få migrerade databasprogram till marknaden i Azure.

**Virtuella SQL-datorer** är perfekta om dina befintliga eller nya program kräver stora databaser eller åtkomst till alla funktioner i SQL Server eller Windows/Linux, och du vill undvika tid och bekostnad av att skaffa ny lokal maskinvara. Det passar också bra när du vill migrera befintliga lokala program och databaser till Azure som de är - i de fall där Azure SQL Database-hanterad instans inte passar bra. Eftersom du inte behöver ändra presentations-, program- och datalager sparar du tid och budget på att återskapa din befintliga lösning. Istället kan du fokusera på att migrera alla dina lösningar till Azure och på att genomföra prestandaoptimeringar som kan krävas av Azure-plattformen. Mer information finns i [Bästa praxis för prestanda i SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Nästa steg

- Se [din första Azure SQL-databas](sql-database-single-database-get-started.md) för att komma igång med SQL Database.
- Se [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Se [Etablera en virtuell SQL Server-dator i Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) för att komma igång med SQL Server på virtuella Azure-datorer.
- [Identifiera rätt SQL-databas eller SQL-hanterad instans SKU för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).
