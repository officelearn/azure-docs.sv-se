---
title: SQL (PaaS) Database kontra SQL Server i molnet på virtuella datorer (IaaS) | Microsoft Docs
description: 'Läs mer om vilket molnbaserat SQL Server-alternativ som passar ditt program: Azure SQL (PaaS) Database eller SQL Server i molnet på Azure Virtual Machines.'
services: sql-database, virtual-machines
keywords: SQL Server-moln, SQL Server i molnet, PaaS-databas, moln-SQL Server, DBaaS
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: carlrab
ms.openlocfilehash: 66ed36ea3d7b38166b9214e36289e32119659856
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965474"
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Välj ett molnbaserat SQL Server-alternativ: Azure SQL (PaaS) Database eller SQL Server på Azure Virtual Machines (IaaS)

I Azure, kan du ha dina SQL Server-arbetsbelastningar som körs i en värdbaserad infrastruktur (IaaS) eller som körs som en värdbaserad tjänst ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)):

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): en SQL-databasmotorn, baserat på Enterprise-utgåvan av SQL Server, som är optimerad för utveckling av moderna program. Azure SQL Database erbjuder flera alternativ:
  - Du kan distribuera en enkel databas till en [logisk server](sql-database-logical-servers.md).
  - Du kan distribuera till en [elastisk pool](sql-database-elastic-pool.md) på en [logisk server](sql-database-logical-servers.md) att dela resurser och minska kostnaderna. 
  - Du kan distribuera till en [Azure SQL Database-hanterade instanser](sql-database-managed-instance.md). 
      
   Följande bild visar dessa distributionsalternativ:

     ![distributionsalternativ](./media/sql-database-technical-overview/deployment-options.png) 

     > [!NOTE]
     > Lägger till ytterligare funktioner som inte är tillgängliga i SQL Server, som inbyggd intelligens och hantering med alla tre versioner, Azure SQL Database. En logisk server som innehåller enkel och delade databaser erbjuder de flesta databas-omfattande funktioner för SQL Server. Med Azure SQL Database Managed Instance erbjuder Azure SQL Database delade resurser för databaser och ytterligare instans-omfattande funktioner. Azure SQL Database Managed Instance stöder Databasmigrering med minimal inga ändringar i databasen. 

- [SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server är installerad och ligger i molnet på Windows Server eller Linux-datorer (VM) som körs på Azure, även kallat en infrastruktur som en tjänst (IaaS). SQL Server på Azure virtual machines är ett bra alternativ för att migrera lokala SQL Server-databaser och program utan några ändringar i databasen. Alla nya versioner och utgåvor av SQL Server är tillgängliga för installation i en IaaS-dator. Den viktigaste skillnaden från SQL-databas är att SQL Server-datorer tillåter fullständig kontroll över databasmotorn. Du kan välja när Underhåll/korrigeringar börjar att ändra återställningsmodellen till simple eller massloggade för att möjliggöra snabbare inläsning mindre loggen, pausa eller starta motorn när de behövs och du kan anpassa SQL Server database engine. Med den här ytterligare kontroll levereras med tillagda ansvar att hantera de virtuella datorerna.

Lär dig hur varje alternativ för distribution som passar in i Microsofts dataplattform och få hjälp med att matcha rätt alternativ för dina affärsbehov. Oavsett om du prioriterar kostnadsbesparingar eller en minimering av administration över allt annat, kan den här artikeln hjälpa dig att välja det angreppssätt som ger bäst resultat för de verksamhetskrav som är viktigast för dig.

## <a name="microsofts-sql-data-platform"></a>Microsofts dataplattform för SQL

En av de första sakerna man ska förstå när det gäller Azure kontra lokala SQL Server-databaser, är att det är möjligt att använda allt. Microsofts dataplattform utnyttjar SQL Server-teknologi och gör den tillgänglig på fysiska, lokala datorer, i privata molnmiljöer, tredjeparts privata molnmiljöer och det offentliga molnet. Med SQL Server på virtuella datorer i Azure kan du uppfylla unika och skilda affärsbehov genom en kombination av lokala och molnstyrda distributioner, samtidigt som du kan använda samma uppsättningar med serverprodukter, utvecklingsverktyg och expertis i alla dessa miljöer.

   ![SQL Server-molnalternativ: SQL-server på IaaS, eller SaaS-SQL-databas i molnet.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Som diagrammet visar, särskiljs de olika alternativen efter den administrativa nivån du har över infrastrukturen (på x-axeln) och efter den kostnadseffektivitet som kan uppnås av konsolidering på databasnivå och automatisering (på y-axeln).

När du skapar ett program, finns det fyra grundläggande alternativ för att hantera SQL Serverdelen av programmet:

* SQL Server på icke-virtualiserade fysiska datorer
* SQL Server på lokala virtualiserade datorer (privat moln)
* SQL Server på virtuella Azure-datorer (offentligt Microsoft-moln)
* Azure SQL Database (offentligt Microsoft-moln)

I följande avsnitt går vi igenom SQL Server i det offentliga Microsoft-molnet: Azure SQL Database och SQL Server på Azure Virtual Machines. Dessutom går vi igenom vanliga verksamhetsmotiv för att fastställa vilket alternativ som är bäst för ditt fall.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>En närmare titt på Azure SQL Database och SQL Server på virtuella Azure-datorer

**Azure SQL Database** är en relationsdatabas databas-som-en-tjänst (DBaaS) i Azure-molnet som ligger branschkategorin av *Platform-as-a-Service (PaaS)*. [SQL Database](sql-database-technical-overview.md) bygger på standardiserad maskinvara och programvara som ägs, finns hos och hanteras av Microsoft. Du kan använda inbyggda egenskaper och funktioner som kräver omfattande konfiguration i SQL Server med SQL-databas. När du använder SQL Database, betalar du per användning med alternativ att skala upp eller ut för mer kraft utan avbrott. Azure SQL Database är en perfekt miljö för att utveckla nya program i molnet. Och med [Azure SQL Database Managed Instance](sql-database-managed-instance.md), du kan använda din egen licens. Dessutom kan det här alternativet innehåller alla PaaS fördelarna med Azure SQL Database men lägger till funktioner som tidigare endast fanns i SQL-datorer. Detta omfattar ett virtuellt nätverk (VNet) och nästan 100% kompatibilitet med en lokal SQL Server. [Hanterad instans](sql-database-managed-instance.md) är idealisk för den lokala databasen migreringar till Azure med minimala ändringar som krävs. 

**SQL Server på virtuella Azure-datorer (VM:ar)** faller inom branschkategorin *Infrastruktur som en tjänst (IaaS)* och låter dig köra SQL Server på en virtuell dator i molnet. [SQL Server-datorer](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) också köra på standardiserad maskinvara som är ägs, finns hos och hanteras av Microsoft. När du använder SQL Server på en virtuell dator, kan du antingen betala – precis som för en SQL Server-licens som redan ingår i en SQL Server-avbildning eller enkelt använda en befintlig licens. Du kan också stoppa eller återuppta den virtuella datorn efter behov.

Generellt sett är de här två SQL-alternativen optimerade för olika syften:

* **Azure SQL Database** är optimerad för att minska totala kostnader för etablering och hantering av många databaser. Det minskar löpande administrativa kostnader eftersom du inte behöver hantera några virtuella datorer, operativsystem eller någon databasprogramvara. Du behöver inte hantera uppgraderingar, hög tillgänglighet eller [säkerhetskopieringar](sql-database-automated-backups.md). Generellt sett kan Azure SQL Database kraftigt öka antalet databaser som kan hanteras av en enskild IT- eller utvecklingsresurs. [Elastiska pooler](sql-database-elastic-pool.md) stöder även arkitekturer för SaaS-program med flera innehavare med funktioner, inklusive klientisolering och möjligheten att skala för att minska kostnaderna genom att dela resurser över databaser. [Azure SQL Database Managed Instance](sql-database-managed-instance.md) har stöd för instans-omfattande funktioner som aktiverar enkelt migrera befintliga program, samt dela resurser mellan databaser.
* **SQL Server som körs på Azure Virtual Machines** är optimerat för migrering av befintliga Azure-program till Azure eller för att utöka befintliga lokala program till molnet i hybriddistributioner. Du kan också använda SQL Server på en virtuell dator för att utveckla och testa traditionella SQL Server-program. Med SQL Server på virtuella Azure-datorer, har du fullständiga administrativa rättigheter för en dedikerad instans av SQL Server och en molnbaserad VM. Det är det perfekta valet när en organisation redan har IT-resurser tillgängliga för att underhålla de virtuella datorerna. Med dessa funktioner kan du skapa ett ytterst anpassat system för ditt programs specifika prestanda- och tillgänglighetsbehov.

Följande tabell sammanfattar de huvudsakliga egenskaperna för SQL Database och SQL Server på virtuella Azure-datorer:

| | Azure SQL Database<br>Logiska servrar, elastiska pooler och enskilda databaser | Azure SQL Database<br>Managed Instance |Azure virtuell dator<br>SQL Server |
| --- | --- | --- |---|
| **Bäst för:** |Nya molnbaserade program som vill använda de senaste stabila SQL Server-funktioner andhave tidsbegränsningar på utveckling och marknadsföring. | Nya program eller befintliga lokala program som använder de senaste stabila SQL Server-funktionerna och att migreras till molnet med minimala ändringar.  | Befintliga program som kräver snabb migrering till molnet med minimala ändringar eller inga ändringar. Snabba utvecklings- och test-scenarier där du inte vill köpa lokal SQL Server-maskinvara som inte är för produktion. |
|  | Team som behöver inbyggd hög tillgänglighet, haveriberedskap och uppgradering för databasen. | Samma som SQL-databas. | Team som kan konfigurera bra finjustera, anpassa och hantera hög tillgänglighet, haveriberedskap och korrigeringar för SQL Server. Vissa automatiserade funktioner kan avsevärt förenkla detta arbete. | |
|  | Team som inte vill hantera de underliggande operativsystemen och konfigurationsinställningarna. | Samma som SQL-databas. | Du behöver en anpassad miljö med fullständiga administrativa rättigheter. | |
|  | Databaser på upp till 100 TB. | Samma som SQL-databas. | SQL Server-instanser med upp till 64 TB lagring. Instansen har stöd för så många databaser som behövs. |
| **Kompatibilitet** | Stöder de flesta lokala på databasnivå funktioner. | Stöder nästan alla lokala funktioner på instansnivå och på databasnivå. | Har stöd för alla lokala funktioner. |
| **Resurser:** | Du vill inte använda IT-resurser för konfiguration och hantering av den underliggande infrastrukturen, utan vill fokusera på programnivån. | Samma som SQL-databas. | Du har några IT-resurser som ansvarar för konfiguration och hantering. Vissa automatiserade funktioner kan avsevärt förenkla detta arbete. |
| **Totalkostnad för ägarskap:** | Eliminerar kostnaderna för maskinvara och reducerar de administrativa kostnaderna. | Samma som SQL-databas. | Eliminerar maskinvarukostnaderna. |
| **Verksamhetskontinuitet:** |Förutom [inbyggda infrastruktur för feltolerans](sql-database-high-availability.md), Azure SQL Database tillhandahåller funktioner, till exempel [automatiska säkerhetskopior](sql-database-automated-backups.md), [Point-In-Time-återställning](sql-database-recovery-using-backups.md#point-in-time-restore), [geo-återställning](sql-database-recovery-using-backups.md#geo-restore), och [redundans grupper och aktiv geo-replikering](sql-database-geo-replication-overview.md) att öka verksamhetskontinuiteten. Mer information finns i [översikt över verksamhetskontinuitet i SQL Database](sql-database-business-continuity.md). | Samma som SQL-databas, plus användarinitierad, endast kopiering säkerhetskopieringar är tillgängliga. | Med SQL Server på Azure Virtual Machines kan du konfigurera en lösning med hög tillgänglighet och haveriberedskap för din databas specifika behov. Du kan därmed få ett system som är höggradigt optimerat för just ditt program. Du kan själv testa och köra felväxling vid behov. Mer information finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Hybridmoln:** |Ditt lokala program har åtkomst till data i Azure SQL Database. | [Intern implementering](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration) och vara ansluten till din lokala miljö med hjälp av Azure Express Route eller VPN-Gateway. | Med SQL Server på virtuella Azure-datorer kan du ha program som kör delvis i molnet och delvis lokalt. Du kan till exempel utöka ditt lokala nätverk och Active Directory-domän till molnet via [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Dessutom kan du lagra lokala datafiler i Azure Storage med [SQL Server-datafiler i Azure](http://msdn.microsoft.com/library/dn385720.aspx). Mer information finns i [Introduktion till SQL Server 2014 Hybridmoln](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Har stöd för [SQL Server-transaktionsreplikering](https://msdn.microsoft.com/library/mt589530.aspx) som en prenumerant för att replikera data. | Replikering stöds inte för Azure SQL Database Managed Instance. | Fullständigt stöd för [Transaktionsreplikering i SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [ständigt aktiverade Tillgänglighetsgrupper](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services och Loggöverföring för att replikera data. Dessutom finns fullständigt stöd för traditionella SQL Server-säkerhetskopieringar | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Verksamhetsmotivering för att välja Azure SQL Database eller SQL Server på virtuella Azure-datorer
### <a name="cost"></a>Kostnad
Oavsett om du är en startup med dålig kassa, eller ett team i ett etablerat företag som har en begränsad budget. Budgetbegränsningar är vanligtvis den främsta motivationsfaktorn när man bestämmer var man ska ha sina databaser. I det här avsnittet ska vi först gå igenom de grundläggande koncepten med debitering och licensiering i Azure för dessa två relationsdatabasalternativ: SQL Database och SQL Server på Azure Virtual Machines. Du lär dig också hur du beräknar den totala programkostnaden.

#### <a name="billing-and-licensing-basics"></a>Debitering och licensiering

För närvarande **SQL Database** säljs som en tjänst och är tillgänglig i flera servicenivåer med olika priser för resurser, som debiteras timvis till en fast kostnad som baseras på tjänstnivå och beräkningsstorleken som du väljer. Med SQL Database Managed Instance kan du också använda en egen licens. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/). Du kan dynamiskt justera tjänstnivåer och compute så att de matchar ditt programs ändrade behov för genomströmning. Den senaste informationen om den aktuella stöds tjänstnivåer, se [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md). Du kan också skapa [elastiska pooler](sql-database-elastic-pool.md) att dela resurser mellan databasinstanser att minska kostnaderna och hantera användning vid tillfälliga toppar.

Med **SQL Database** så konfigureras, korrigeras och uppgraderas databasens programvara automatiskt av Microsoft, vilket minskar dina administrationskostnader. Dessutom gör dess [inbyggda säkerhetskopierings](sql-database-automated-backups.md)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser. 

Med **SQL Server på Azure Virtual Machines** kan du använda valfri SQL Server-avbildning som plattformen stöder (som omfattar en licens) eller använda din egen SQL Server-licens. Alla SQL Server-versioner (2008R2, 2012, 2014, 2016) och utgåvor (Developer, Express, Web, Standard, Enterprise) som stöds är tillgängliga. Det finns också BYOL-versioner (Bring-Your-Own-License) av avbildningarna. När du använder de avbildningar som Azure tillhandahåller beror driftskostnaderna på storleken på de virtuella datorerna samt vilken utgåva av SQL Server du väljer. Oavsett VM-storlek eller SQL Server-utgåva betalar du per minut licensieringskostnader för SQL Server och Windows eller Linux-Server, tillsammans med kostnaden för Azure Storage för VM-diskarna. Betalningsalternativet per minut låter dig använda SQL Server så länge du behöver utan att köpa ytterligare SQL Server-licenser. Om du ta med din egen SQL Server-licens till Azure debiteras du för server och endast kostnader för lagring. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Beräkna den totala programkostnaden
När du börjar använda en molnplattform, inkluderar kostnaden för programkörning kostnad för ny utveckling och pågående administrationskostnader samt tjänstekostnaderna offentligt moln plattform.

**Med Azure SQL Database:**

- Kraftigt minimerade administrationskostnader
- Begränsad utvecklingskostnader för migrerade program
- SQL Database-Tjänstekostnader
- Inga inköpschef kostnader för maskinvara

**När du använder SQL Server på virtuella Azure-datorer:**

- Högre administrationskostnader
- Begränsad till ingen utvecklingskostnader för migrerade program
- VM-Tjänstekostnader
- Kostnader för SQL Server-licens
- Inga inköpschef kostnader för maskinvara

Mer information om priser finns i följande resurser:

* [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
* [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) för [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) och för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
* [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration
För många företag handlar beslutet att övergå till en molntjänst lika mycket om att minska administrativ komplexitet som kostnad. Med IaaS och PaaS, Microsoft tillämpar den underliggande infrastrukturen och automatiskt replikerar alla data för att ge haveriberedskap, konfigurerar och uppgraderar databasprogramvaran, hanterar belastningsutjämning och genomför transparent växling om det finns en serverfel inom ett datacenter. 

- Med **Azure SQL Database**, du kan fortsätta att administrera din databas, men du behöver inte längre hantera databasmotorn, operativsystemet för servern eller maskinvara.  Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet. Dessutom kräver konfigurera hög tillgänglighet till ett annat Datacenter minimal konfiguration och administration.
- Med **SQL Server på Azure Virtual Machines** har du fullständig kontroll över operativsystemet och konfigurationen av SQL Server-instansen. Med en VM bestämmer du själv när operativsystem och databasprogramvara ska uppdateras/uppgraderas och när ytterligare programvara, t.ex. antivirusprogram, ska installeras. Vissa automatiska funktioner kan avsevärt underlätta arbetet med korrigeringar, säkerhetskopiering och hög tillgänglighet. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer. Med Azure kan du ändra storleken på en virtuell dator efter behov. Mer information finns i [Storlekar för virtuella Azure-datorer och Azure-molntjänster](../virtual-machines/windows/sizes.md). 

### <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)
För många IT-avdelningar är det av högsta vikt att uppfylla skyldigheterna på upptid enligt serviceavtalet (SLA). I det här avsnittet tittar vi på de SLA som är tillämpliga för varje databasalternativ.

För **SQL Database**, tillhandahåller Microsoft en SLA med 99,99% tillgänglighet. Den senaste informationen finns i [Serviceavtalet](https://azure.microsoft.com/support/legal/sla/sql-database/). 

För **SQL Server som kör på virtuella Azure-datorer** erbjuder Microsoft en tillgänglighets-SLA på 99,95 %, vilket bara täcker den virtuella datorn. Det här SLA:t omfattar inte de processer som körs på den virtuella datorn (till exempel SQL Server) som kräver att du är värd för minst två VM-instanser i en tillgänglighetsuppsättning. Den senaste informationen finns på [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För databasen med hög tillgänglighet (HA) i virtuella datorer bör du konfigurera en av alternativ för hög tillgänglighet som stöds i SQL Server, till exempel [ständigt aktiverade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Användningen av ett alternativ för hög tillgänglighet som stöds medför inget nytt SLA, men ger > 99,99 % databastillgänglighet.

### <a name="market"></a>Dags att flytta till Azure
**SQL Database logiska servrar, elastiska pooler och enskilda databaser** är rätt lösning för molndesignade program när produktivitet för utvecklare och snabb tid till marknad för nya lösningar är avgörande. Med sin programmässiga DBA-lika funktionalitet, är det perfekt för molnarkitekter och utvecklare, eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen. 

**SQL Database Managed Instance** förenklar migreringen av befintliga program till Azure SQL Database, så att du kan föra en migrerade databasen program på marknaden snabbt i Azure.

**SQL Server som körs på Azure Virtual Machines** är perfekt om dina befintliga eller nya program kräver stora databaser eller få åtkomst till alla funktioner i SQL Server eller Windows/Linux, och du vill undvika att tid och pengar för att skaffa nya lokal maskinvara. Det är också passa om du vill migrera befintliga lokala program och databaser till Azure som – är – i fall där Azure SQL Database Managed Instance inte är ett bra alternativ. Eftersom du inte behöver ändra presentationen, programmet eller datalagren, kan du spara in tid och budget på att omstrukturera din befintliga lösning. Istället kan du fokusera på att migrera alla dina lösningar till Azure och på att genomföra prestandaoptimeringar som kan krävas av Azure-plattformen. Mer information finns i [Bästa praxis för prestanda i SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Nästa steg

* Se [Din första Azure SQL Database](sql-database-get-started-portal.md) för att komma igång med SQL Database.
* Se [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
* Se [Etablera en virtuell SQL Server-dator i Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) för att komma igång med SQL Server på virtuella Azure-datorer.
