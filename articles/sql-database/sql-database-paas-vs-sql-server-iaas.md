---
title: Välja mellan SQL-alternativ i Azure | Microsoft Docs
description: Lär dig att välja bland distributionsalternativ i Azure SQL Database och mellan Azure SQL Database och SQL Server på Azure virtual machines
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
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: d9cd5ba0b697cbf67f943eb49d66010745d8561e
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360816"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Välja rätt SQL Server-alternativ i Azure

I Azure, kan du ha dina SQL Server-arbetsbelastningar som körs i en värdbaserad infrastruktur (IaaS) eller som körs som en värdbaserad tjänst ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). I PaaS har du flera alternativ och tjänstnivåer inom varje distributionsalternativ. Viktig fråga som du behöver fråga när du bestämmer mellan PaaS eller IaaS är vill du hantera din databas, tillämpa korrigeringar, göra säkerhetskopior eller du vill delegera de här åtgärderna till Azure?
Beroende på svaret har du följande alternativ:

- [Azure SQL Database](sql-database-technical-overview.md): En fullständigt hanterad SQL-databasmotorn, baserat på den senaste stabila Enterprise-utgåvan av SQL Server. Det här är en relationell databas-som-tjänst-(DBaaS) i Azure-molnet som ligger branschkategorin av *Platform-as-a-Service (PaaS)*. SQL database har flera distributionsalternativ, som bygger på standardiserad maskinvara och programvara som är ägs, finns hos och hanteras av Microsoft. Med SQL-databas, kan du använda inbyggda egenskaper och funktioner som kräver omfattande konfiguration när de används i SQL Server (antingen lokalt eller i en Azure virtuell dator). När du använder SQL Database, betalar du per användning med alternativ att skala upp eller ut för mer kraft utan avbrott. SQL Database har ytterligare funktioner som inte är tillgängliga i SQL Server, till exempel inbyggd hög tillgänglighet, intelligens och hantering. Azure SQL Database erbjuder följande distributionsalternativ:
  
  - Som en [enkel databas](sql-database-single-database.md) med en egen uppsättning resurser som hanteras via SQL-databasserver. En enkel databas liknar en [inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) i SQL Server. Det här alternativet är optimerat för moderna program utvecklingen av nya molnlagrade program.
  - En [elastisk pool](sql-database-elastic-pool.md), vilket är en uppsättning databaser med en delad uppsättning resurser som hanteras via en SQL Database-server. Enkla databaser kan flyttas in och ut från en elastisk pool. Det här alternativet är optimerat för utveckling av moderna program av nya molnlagrade program som använder det SaaS-programmet.
  - [Hanterad instans](sql-database-managed-instance.md), som är en samling av system- och -databaser med en delad uppsättning resurser. En hanterad instans liknar en instans av [Microsoft SQL Server-databasmotorn] erbjudande delade resurser för databaser och ytterligare instans-omfattande funktioner. Hanterad instans har stöd för migrering av databas från en lokal plats med minimal inga ändringar i databasen. Det här alternativet innehåller alla PaaS fördelarna med Azure SQL Database men lägger till funktioner som tidigare endast fanns i SQL-datorer. Detta omfattar ett virtuellt nätverk (VNet) och nästan 100% kompatibilitet med en lokal SQL Server.
- [SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) hamnar branschkategorin *Infrastructure-as-a-Service (IaaS)* och låter dig köra SQL Server i en fullständigt hanterad virtuell dator i Azure-molnet. [SQL Server-datorer](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) också köra på standardiserad maskinvara som är ägs, finns hos och hanteras av Microsoft. När du använder SQL Server på en virtuell dator, kan du antingen betala – precis som för en SQL Server-licens som redan ingår i en SQL Server-avbildning eller enkelt använda en befintlig licens. Du kan också stoppa eller återuppta den virtuella datorn efter behov. SQL Server installerad och ligger i molnet på Windows Server eller Linux-datorer (VM) som körs på Azure, även kallat en infrastruktur som en tjänst (IaaS). SQL Server på Azure virtual machines är ett bra alternativ för att migrera lokala SQL Server-databaser och program utan några ändringar i databasen. Alla nya versioner och utgåvor av SQL Server är tillgängliga för installation i en IaaS-dator. Den viktigaste skillnaden från SQL-databas är att SQL Server-datorer tillåter fullständig kontroll över databasmotorn. Du kan välja när Underhåll/korrigeringar börjar att ändra återställningsmodellen till simple eller massloggade för att möjliggöra snabbare inläsning mindre loggen, pausa eller starta motorn när de behövs och du kan anpassa SQL Server database engine. Med den här ytterligare kontroll levereras med tillagda ansvar att hantera de virtuella datorerna.

De viktigaste skillnaderna mellan de här alternativen finns i följande tabell:

| SQL Server på virtuella datorer | Hanterad instans i SQL-databas | Enkel databas / elastisk pool i SQL-databas |
| --- | --- | --- |
|Du har fullständig kontroll över SQL Server-motorn.<br/>Upp till 99,95% tillgänglighet.<br/>Fullständig paritet med matchande version av en lokal SQL Server.<br/>Fast, välkända databasmotorn version.<br/>Enkel migrering från SQL Server lokalt.<br/>Privat IP-adress inom Azure virtuellt nätverk.<br/>Du har möjlighet att distribuera program eller tjänster på värden där SQL Server är placerad.| Hög kompatibilitet med SQL Server lokalt.<br/>99,99% tillgänglighet garanteras.<br/>Inbyggd säkerhetskopiering, korrigeringar, återställning.<br/>Senaste säkra Database Engine-versionen.<br/>Enkel migrering från SQL Server.<br/>Privat IP-adress inom Azure virtuellt nätverk.<br/>Inbyggda avancerade intelligens och säkerhet.<br/>Online ändring av resurser (CPU/storage).|De mest använda SQL Server-funktionerna är tillgängliga.<br/>99,99% tillgänglighet garanteras.<br/>Inbyggd säkerhetskopiering, korrigeringar, återställning.<br/>Senaste säkra Database Engine-versionen.<br/>Möjligheten att tilldela resurser som krävs (CPU/storage) till enskilda databaser.<br/>Inbyggda avancerade intelligens och säkerhet.<br/>Online ändring av resurser (CPU/storage).|
|Du måste hantera dina säkerhetskopior och korrigeringar.<br>Du behöver implementera en egen lösning för hög tillgänglighet.<br/>Det finns ett driftstopp medan du ändrar resources(CPU/storage)|Det finns fortfarande ett minimalt antal SQL Server-funktioner som inte är tillgängliga.<br/>Ingen garanterad exakta Underhåll tid (men nästan genomskinligt).<br/>Kompatibilitet med SQL Server-versionen kan uppnås endast med kompatibilitetsnivå för databas.|Migrering från SQL Server kan vara svårt.<br/>Vissa SQL Server-funktioner är inte tillgängliga.<br/>Ingen garanterad exakta Underhåll tid (men nästan genomskinligt).<br/>Kompatibilitet med SQL Server-versionen kan uppnås endast med kompatibilitetsnivå för databas.<br/>Privat IP-adress kan inte tilldelas (du kan begränsa åtkomst med hjälp av brandväggsregler).|

Lär dig hur varje alternativ för distribution som passar in i Microsofts dataplattform och få hjälp med att matcha rätt alternativ för dina affärsbehov. Oavsett om du prioriterar kostnadsbesparingar eller en minimering av administration över allt annat, kan den här artikeln hjälpa dig att välja det angreppssätt som ger bäst resultat för de verksamhetskrav som är viktigast för dig.

## <a name="microsofts-sql-data-platform"></a>Microsofts dataplattform för SQL

En av de första sakerna man ska förstå när det gäller Azure kontra lokala SQL Server-databaser, är att det är möjligt att använda allt. Microsofts dataplattform utnyttjar SQL Server-teknologi och gör den tillgänglig på fysiska, lokala datorer, i privata molnmiljöer, tredjeparts privata molnmiljöer och det offentliga molnet. Med SQL Server på virtuella datorer i Azure kan du uppfylla unika och skilda affärsbehov genom en kombination av lokala och molnstyrda distributioner, samtidigt som du kan använda samma uppsättningar med serverprodukter, utvecklingsverktyg och expertis i alla dessa miljöer.

   ![Alternativ för SQLServer i molnet: SQLServer på IaaS eller SaaS-SQL-databas i molnet.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Som diagrammet visar, särskiljs de olika alternativen efter den administrativa nivån du har över infrastrukturen (på x-axeln) och efter den kostnadseffektivitet som kan uppnås av konsolidering på databasnivå och automatisering (på y-axeln).

När du skapar ett program, finns det fyra grundläggande alternativ för att hantera SQL Serverdelen av programmet:

- SQL Server på icke-virtualiserade fysiska datorer
- SQL Server på lokala virtualiserade datorer (privat moln)
- SQL Server på virtuella Azure-datorer (offentligt Microsoft-moln)
- Azure SQL Database (offentligt Microsoft-moln)

I följande avsnitt kommer du lära dig om SQL Server i det offentliga Microsoft-molnet: Azure SQL Database och SQLServer på virtuella Azure-datorer. Dessutom går vi igenom vanliga verksamhetsmotiv för att fastställa vilket alternativ som är bäst för ditt fall.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>En närmare titt på Azure SQL Database och SQL Server på virtuella Azure-datorer

Generellt sett är de här två SQL-alternativen optimerade för olika syften:

- **Azure SQL Database**

Optimerad för att minska totala kostnader för etablering och hantering av många databaser. Det minskar löpande administrativa kostnader eftersom du inte behöver hantera några virtuella datorer, operativsystem eller någon databasprogramvara. Du behöver inte hantera uppgraderingar, hög tillgänglighet eller [säkerhetskopieringar](sql-database-automated-backups.md). Generellt sett kan Azure SQL Database kraftigt öka antalet databaser som kan hanteras av en enskild IT- eller utvecklingsresurs. [Elastiska pooler](sql-database-elastic-pool.md) stöder även arkitekturer för SaaS-program med flera innehavare med funktioner, inklusive klientisolering och möjligheten att skala för att minska kostnaderna genom att dela resurser över databaser. [Hanterad instans](sql-database-managed-instance.md) har stöd för instans-omfattande funktioner som aktiverar enkelt migrera befintliga program, samt dela resurser mellan databaser.

- **SQL Server på virtuella Azure-datorer**

Optimerat för migrering av befintliga program till Azure eller utöka befintliga lokala program till molnet i hybriddistributioner. Du kan också använda SQL Server på en virtuell dator för att utveckla och testa traditionella SQL Server-program. Med SQL Server på virtuella Azure-datorer, har du fullständiga administrativa rättigheter för en dedikerad instans av SQL Server och en molnbaserad VM. Det är det perfekta valet när en organisation redan har IT-resurser tillgängliga för att underhålla de virtuella datorerna. Med dessa funktioner kan du skapa ett ytterst anpassat system för ditt programs specifika prestanda- och tillgänglighetsbehov.

Följande tabell sammanfattar de huvudsakliga egenskaperna för SQL Database och SQL Server på virtuella Azure-datorer:

| | SQL Database enskilda databaser och elastiska pooler | SQL Database-hanterade instanser |Azure-datorer med SQL Server |
| --- | --- | --- |---|
| **Bäst för:** |Nya molnbaserade program som vill använda de senaste stabila SQL Server-funktionerna och tidsbegränsningar på utveckling och marknadsföring. | Nya program eller befintliga lokala program som använder de senaste stabila SQL Server-funktionerna och att migreras till molnet med minimala ändringar.  | Befintliga program som kräver snabb migrering till molnet med minimala ändringar eller inga ändringar. Snabba utvecklings- och test-scenarier där du inte vill köpa lokal SQL Server-maskinvara som inte är för produktion. |
|  | Team som behöver inbyggd hög tillgänglighet, haveriberedskap och uppgradering för databasen. | Samma som SQL Database enkel och delade databaser. | Team som kan konfigurera bra finjustera, anpassa och hantera hög tillgänglighet, haveriberedskap och korrigeringar för SQL Server. Vissa automatiserade funktioner kan avsevärt förenkla detta arbete. |
|  | Team som inte vill hantera de underliggande operativsystemen och konfigurationsinställningarna. | Samma som SQL Database enkel och delade databaser. | Du behöver en anpassad miljö med fullständiga administrativa rättigheter. |
|  | Databaser på upp till 100 TB. | Upp till 8 TB. | SQL Server-instanser med upp till 64 TB lagring. Instansen har stöd för så många databaser som behövs. |
| **Kompatibilitet** | Stöder de flesta lokala på databasnivå funktioner. | Stöder nästan alla lokala funktioner på instansnivå och på databasnivå. | Har stöd för alla lokala funktioner. |
| **Resurser:** | Du vill inte anställa IT-resurser för konfiguration och hantering av underliggande den infrastrukturen utan vill fokusera på programnivån. | Samma som SQL Database enkel och delade databaser. | Du har några IT-resurser som ansvarar för konfiguration och hantering. Vissa automatiserade funktioner kan avsevärt förenkla detta arbete. |
| **Totalkostnad för ägarskap:** | Eliminerar kostnaderna för maskinvara och reducerar de administrativa kostnaderna. | Samma som SQL Database enkel och delade databaser. | Eliminerar maskinvarukostnaderna. |
| **Verksamhetskontinuitet:** |Förutom [inbyggda infrastruktur för feltolerans](sql-database-high-availability.md), Azure SQL Database tillhandahåller funktioner, till exempel [automatiska säkerhetskopior](sql-database-automated-backups.md), [Point-In-Time-återställning](sql-database-recovery-using-backups.md#point-in-time-restore), [geo-återställning](sql-database-recovery-using-backups.md#geo-restore), [aktiv geo-replikering](sql-database-active-geo-replication.md), och [automatisk redundans grupper](sql-database-auto-failover-group.md) att öka verksamhetskontinuiteten. Mer information finns i [översikt över verksamhetskontinuitet i SQL Database](sql-database-business-continuity.md). | Samma som SQL Database enkel och delade databaser, plus användarinitierad, endast kopiering säkerhetskopieringar är tillgängliga. | Med SQL Server på Azure Virtual Machines kan du konfigurera en lösning med hög tillgänglighet och haveriberedskap för din databas specifika behov. Du kan därmed få ett system som är höggradigt optimerat för just ditt program. Du kan själv testa och köra felväxling vid behov. Mer information finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Hybridmoln:** |Ditt lokala program har åtkomst till data i Azure SQL Database. | [Intern implementering](sql-database-managed-instance-vnet-configuration.md) och vara ansluten till din lokala miljö med hjälp av Azure Express Route eller VPN-Gateway. | Med SQL Server på virtuella Azure-datorer kan du ha program som kör delvis i molnet och delvis lokalt. Du kan till exempel utöka ditt lokala nätverk och Active Directory-domän till molnet via [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Läs mer på hybridmolnlösningar [utöka lokala datalösningar till molnet](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Har stöd för [SQL Server-transaktionsreplikering](https://msdn.microsoft.com/library/mt589530.aspx) som en prenumerant för att replikera data. | Replikering stöds för den hanterade instansen som en förhandsversion av funktionen. | Fullständigt stöd för [Transaktionsreplikering i SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [ständigt aktiverade Tillgänglighetsgrupper](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services och Loggöverföring för att replikera data. Dessutom finns fullständigt stöd för traditionella SQL Server-säkerhetskopieringar |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Verksamhetsmotivering för att välja Azure SQL Database eller SQL Server på virtuella Azure-datorer

Det finns flera faktorer som kan påverka ditt beslut att välja PaaS eller IaaS som värd för dina SQL-databaser:

- [Kostnaden](#cost) -alternativet för både PaaS och IaaS innehålla base pris som omfattar underliggande infrastruktur och licensiering. Men med IaaS-alternativet måste du investera mer tid och resurser för att hantera din databas när du får dessa administrationsfunktioner som ingår i priset i PaaS. IaaS-alternativet kan du avställning dina resurser medan du inte använder dem för att minska kostnaden, medan PaaS-versionen körs alltid om inte om du ta bort och återskapa dina resurser när de behövs.
- [Administration](#administration) -PaaS alternativ minska den tid som du behöver investera för att administrera databasen. Det begränsar också en mängd anpassade administrationsuppgifter och skript som du kan utföra eller köra. Exempelvis CLR stöds inte med enkel eller grupperade databaser, men det finns stöd för en hanterad instans. Inga distributionsalternativ i PaaS stöder också användningen av spårningsflaggor.
- [Servicenivåavtal](#service-level-agreement-sla) -både IaaS och PaaS serviceavtal hög, branschens standard. PaaS-alternativet garanterar 99,99% SLA, medan IaaS garanterar 99,95% SLA för infrastruktur, vilket innebär att du behöver implementera ytterligare metoder för att säkerställa tillgängligheten för dina databaser. I fallen, om du vill implementera hög tillgänglighet-lösning som matchar PaaS, kan du behöva skapa ytterligare SQL Server i virtuell dator och konfigurera AlwaysOn-Tillgänglighetsgrupper som kan double kostnaden för din databas.
- [Dags att flytta till Azure](#market) – SQL Server i Azure VM är exakt matchning av din miljö, så migreringen från en lokal plats till Azure SQL-VM inte är lika flyttar databaser från en lokal server till en annan. Hanterad instans kan också mycket enkelt att migrera; Det kan dock finnas vissa ändringar som du måste tillämpa innan du migrerar till en hanterad instans.

De här faktorerna kommer att diskuteras mer detaljerat i följande avsnitt.

### <a name="cost"></a>Kostnad

Oavsett om du är en startup med dålig kassa, eller ett team i ett etablerat företag som har en begränsad budget. Budgetbegränsningar är vanligtvis den främsta motivationsfaktorn när man bestämmer var man ska ha sina databaser. I det här avsnittet får du lära dig om debitering och licensiering i Azure för de här två relationella databasalternativen: SQL Database och SQLServer på virtuella Azure-datorer. Du lär dig också hur du beräknar den totala programkostnaden.

#### <a name="billing-and-licensing-basics"></a>Debitering och licensiering

För närvarande **SQL Database** säljs som en tjänst och är tillgänglig med flera distributionsalternativ för och i flera servicenivåer med olika priser för resurser, som debiteras timvis till en fast kostnad som baseras på tjänstnivån och beräkna storleken som du väljer. Den senaste informationen om den aktuella stöds tjänstnivåer, compute storlekar och lagring belopp, se [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

- Med SQL Database enkel databas, kan du välja en tjänstnivå som passar dina behov från en mängd priser från och med 5$ / månad för basic-nivån.
- Du kan skapa [elastiska pooler](sql-database-elastic-pool.md) att dela resurser mellan databasinstanser att minska kostnaderna och hantera användning vid tillfälliga toppar.
- Med SQL Database-hanterad instans, kan du också använda en egen licens. Mer information om bring-your-own-licensiering finns i [License Mobility genom Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/) eller Använd [Azure Hybrid-förmånen Kalkylatorn](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) att se hur du **spara upp till 40%**.

Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/). Du kan dynamiskt justera tjänstnivåer och compute så att de matchar ditt programs ändrade behov för genomströmning.

Med **SQL Database** så konfigureras, korrigeras och uppgraderas databasens programvara automatiskt av Microsoft, vilket minskar dina administrationskostnader. Dessutom gör dess [inbyggda säkerhetskopierings](sql-database-automated-backups.md)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser.

Med **SQL Server på Azure Virtual Machines** kan du använda valfri SQL Server-avbildning som plattformen stöder (som omfattar en licens) eller använda din egen SQL Server-licens. Alla SQL Server-versioner (2008R2, 2012, 2014, 2016) och utgåvor (Developer, Express, Web, Standard, Enterprise) som stöds är tillgängliga. Det finns också BYOL-versioner (Bring-Your-Own-License) av avbildningarna. När du använder de avbildningar som Azure tillhandahåller beror driftskostnaderna på storleken på de virtuella datorerna samt vilken utgåva av SQL Server du väljer. Oavsett VM-storlek eller SQL Server-utgåva betalar du per minut licensieringskostnader för SQL Server och Windows eller Linux-Server, tillsammans med kostnaden för Azure Storage för VM-diskarna. Betalningsalternativet per minut låter dig använda SQL Server så länge du behöver utan att köpa ytterligare SQL Server-licenser. Om du ta med din egen SQL Server-licens till Azure debiteras du för server och endast kostnader för lagring. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Beräkna den totala programkostnaden

När du börjar använda en molnplattform, inkluderar kostnaden för programkörning kostnad för ny utveckling och pågående administrationskostnader samt tjänstekostnaderna offentligt moln plattform.

**Med Azure SQL Database:**

- Kraftigt minimerade administrationskostnader
- Begränsad utvecklingskostnader för migrerade program (hanterade instanser)
- SQL Database-Tjänstekostnader
- Inga inköpschef kostnader för maskinvara

**När du använder SQL Server på virtuella Azure-datorer:**

- Högre administrationskostnader
- Begränsad till ingen utvecklingskostnader för migrerade program
- VM-Tjänstekostnader
- Inga inköpschef kostnader för maskinvara

Mer information om priser finns i följande resurser:

- [Prisinformation för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
- [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) för [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) och för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

För många företag handlar beslutet att övergå till en molntjänst lika mycket om att minska administrativ komplexitet som kostnad. Med IaaS och PaaS, Microsoft tillämpar den underliggande infrastrukturen och automatiskt replikerar alla data för att ge haveriberedskap, konfigurerar och uppgraderar databasprogramvaran, hanterar belastningsutjämning och genomför transparent växling om det finns en serverfel inom ett datacenter.

- Med **Azure SQL Database**, du kan fortsätta att administrera din databas, men du behöver inte längre hantera databasmotorn, operativsystemet eller maskinvaran. Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet. Dessutom kräver konfigurera hög tillgänglighet till ett annat Datacenter minimal konfiguration och administration.
- Med **SQL Server på Azure Virtual Machines** har du fullständig kontroll över operativsystemet och konfigurationen av SQL Server-instansen. Med en VM bestämmer du själv när operativsystem och databasprogramvara ska uppdateras/uppgraderas och när ytterligare programvara, t.ex. antivirusprogram, ska installeras. Vissa automatiska funktioner kan avsevärt underlätta arbetet med korrigeringar, säkerhetskopiering och hög tillgänglighet. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer. Med Azure kan du ändra storleken på en virtuell dator efter behov. Mer information finns i [Storlekar för virtuella Azure-datorer och Azure-molntjänster](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)

För många IT-avdelningar är det av högsta vikt att uppfylla skyldigheterna på upptid enligt serviceavtalet (SLA). I det här avsnittet tittar vi på de SLA som är tillämpliga för varje databasalternativ.

För **SQL Database**, tillhandahåller Microsoft en SLA med 99,99% tillgänglighet. Den senaste informationen finns i [Serviceavtalet](https://azure.microsoft.com/support/legal/sla/sql-database/).

För **SQL Server som kör på virtuella Azure-datorer** erbjuder Microsoft en tillgänglighets-SLA på 99,95 %, vilket bara täcker den virtuella datorn. Det här SLA:t omfattar inte de processer som körs på den virtuella datorn (till exempel SQL Server) som kräver att du är värd för minst två VM-instanser i en tillgänglighetsuppsättning. Den senaste informationen finns på [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För databasen med hög tillgänglighet (HA) i virtuella datorer bör du konfigurera en av alternativ för hög tillgänglighet som stöds i SQL Server, till exempel [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Användningen av ett alternativ för hög tillgänglighet som stöds medför inget nytt SLA, men ger > 99,99 % databastillgänglighet.

### <a name="market"></a>Dags att flytta till Azure

**SQL Database enskilda databaser eller elastiska pooler** är rätt lösning för molndesignade program när produktivitet för utvecklare och snabb tid till marknad för nya lösningar är avgörande. Med sin programmässiga DBA-lika funktionalitet, är det perfekt för molnarkitekter och utvecklare, eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen.

**SQL Database-hanterad instans** förenklar migreringen av befintliga program till Azure SQL Database, så att du kan föra migrerade databasprogram på marknaden snabbt i Azure.

**SQL Server som körs på Azure Virtual Machines** är perfekt om dina befintliga eller nya program kräver stora databaser eller få åtkomst till alla funktioner i SQL Server eller Windows/Linux, och du vill undvika tid och pengar för att skaffa nya lokal maskinvara. Det är också passa om du vill migrera befintliga lokala program och databaser till Azure som – är – i fall där Azure SQL Database managed instance inte är ett bra alternativ. Eftersom du inte behöver ändra presentationen, programmet och datalager spara du tid och budget på att behöva bygga om din befintliga lösning. Istället kan du fokusera på att migrera alla dina lösningar till Azure och på att genomföra prestandaoptimeringar som kan krävas av Azure-plattformen. Mer information finns i [Bästa praxis för prestanda i SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Nästa steg

- Se [Din första Azure SQL Database](sql-database-single-database-get-started.md) för att komma igång med SQL Database.
- Se [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Se [Etablera en virtuell SQL Server-dator i Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) för att komma igång med SQL Server på virtuella Azure-datorer.
