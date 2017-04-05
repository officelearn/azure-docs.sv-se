---
title: "SQL (PaaS) Database kontra SQL Server i molnet på virtuella datorer (IaaS) | Microsoft Docs"
description: "Läs mer om vilket molnbaserat SQL Server-alternativ som passar ditt program: Azure SQL (PaaS) Database eller SQL Server i molnet på Azure Virtual Machines."
services: sql-database, virtual-machines
keywords: SQL Server-moln, SQL Server i molnet, PaaS-databas, moln-SQL Server, DBaaS
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cjgronlund
ms.assetid: 7467f422-b77d-4b60-9cb5-0f1ec17ec565
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 28edde3e70bca833d888cdf9831c1544d8cdd4bb
ms.lasthandoff: 03/31/2017


---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Välj ett molnbaserat SQL Server-alternativ: Azure SQL (PaaS) Database eller SQL Server på Azure Virtual Machines (IaaS)
Azure har två alternativ för att hantera SQL Server-arbetsbelastningar i Microsoft Azure:

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): en SQL-databas inbyggd i molnet, kallas även för en plattform som tjänst-databas (PaaS) eller en databas som en tjänst (DBaaS) som är optimerad för apputveckling för programvara som en tjänst (SaaS). Den är kompatibel med de flesta funktioner i SQL Server. Mer information om PaaS finns i [Vad är PaaS?](https://azure.microsoft.com/overview/what-is-paas/)
* [SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server som är installerad och ligger i molnet på virtuella Windows Server-datorer (VM:ar) och körs i Azure, vilket också kallas för infrastruktur som en tjänst (IaaS).
  SQL Server på virtuella datorer i Azure är optimerat för migrering av befintliga SQL Server-program. Alla versioner och utgåvor av SQL Server är tillgängliga. Det är hundra procent kompatibelt med SQL Server så att du kan lägga till så många databaser du behöver och köra transaktioner mellan databaser. Du har fullständig kontroll i SQL Server och Windows.

Lär dig hur varje alternativ passar in i Microsofts dataplattform och få hjälp med att matcha rätt alternativ för dina verksamhetsbehov. Oavsett om du prioriterar kostnadsbesparingar eller en minimering av administration över allt annat, kan den här artikeln hjälpa dig att välja det angreppssätt som ger bäst resultat för de verksamhetskrav som är viktigast för dig.

## <a name="microsofts-data-platform"></a>Microsofts dataplattform
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
**Azure SQL Database** är en relationell databas som en tjänst (DBaaS) som ligger i Azure-molnet och faller inom branschkategorierna *Programvara som en tjänst (SaaS)* och *Plattform som en tjänst (PaaS)*. [SQL Database](sql-database-technical-overview.md) bygger på standardiserad maskinvara och programvara som ägs, finns hos och hanteras av Microsoft. Med SQL Database, kan du utveckla direkt i tjänsten med hjälp av inbyggda funktioner. När du använder SQL Database, betalar du per användning med alternativ att skala upp eller ut för mer kraft utan avbrott.

**SQL Server på virtuella Azure-datorer (VM:ar)** faller inom branschkategorin *Infrastruktur som en tjänst (IaaS)* och låter dig köra SQL Server på en virtuell dator i molnet. På ett liknande sätt som SQL Database så är den byggd på standardiserad maskinvara som ägs, finns hos och hanteras av Microsoft. När du använder SQL Server på en virtuell dator kan du antingen betala per användning för en SQL Server-licens som redan finns i en SQL Server-avbildning eller enkelt använda en befintlig licens. Du kan också enkelt skala upp/ned och pausa/återuppta den virtuella datorn efter behov.

Generellt sett är de här två SQL-alternativen optimerade för olika syften:

* **Azure SQL Database** är optimerat för att minimera totalkostnaderna för etablering och hantering av många databaser. Det minskar löpande administrativa kostnader eftersom du inte behöver hantera några virtuella datorer, operativsystem eller någon databasprogramvara. Du behöver inte hantera uppgraderingar, hög tillgänglighet eller [säkerhetskopieringar](sql-database-automated-backups.md). Generellt sett kan Azure SQL Database kraftigt öka antalet databaser som kan hanteras av en enskild IT- eller utvecklingsresurs.
* **SQL Server som körs på Azure Virtual Machines** är optimerat för migrering av befintliga Azure-program till Azure eller för att utöka befintliga lokala program till molnet i hybriddistributioner. Du kan också använda SQL Server på en virtuell dator för att utveckla och testa traditionella SQL Server-program. Med SQL Server på virtuella Azure-datorer, har du fullständiga administrativa rättigheter för en dedikerad instans av SQL Server och en molnbaserad VM. Det är det perfekta valet när en organisation redan har IT-resurser tillgängliga för att underhålla de virtuella datorerna. Med dessa funktioner kan du skapa ett ytterst anpassat system för ditt programs specifika prestanda- och tillgänglighetsbehov.

Följande tabell sammanfattar de huvudsakliga egenskaperna för SQL Database och SQL Server på virtuella Azure-datorer:

| **Bäst för:** | **Azure SQL Database** | **SQL Server på en virtuell Azure-dator** |
| --- | --- | --- |
|  |Nya molnbaserade program med tidsbegränsningar på utveckling och marknadsföring. |Befintliga program som kräver snabb migrering till molnet med minimala ändringar. Snabba utvecklings- och test-scenarier där du inte vill köpa lokal SQL Server-maskinvara som inte är för produktion. |
|  | Team som behöver inbyggd hög tillgänglighet, haveriberedskap och uppgradering för databasen. |Team som kan konfigurera och hantera hög tillgänglighet, haveriberedskap och korrigeringar för SQL Server. Vissa automatiserade funktioner kan avsevärt förenkla detta arbete. | |
|  | Team som inte vill hantera de underliggande operativsystemen och konfigurationsinställningarna. |Om du behöver en anpassad miljö med fullständiga administrativa rättigheter. | |
|  | Databaser på upp till 1 TB eller större databaser som kan vara [vågrätt eller lodrätt partitionerade](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) med ett skalbart mönster. |SQL Server-instanser med upp till 64 TB lagring. Instansen har stöd för så många databaser som behövs. | |
|  | [Skapa programvara som en tjänst (SaaS)-program](sql-database-design-patterns-multi-tenancy-saas-applications.md). |Migrera och utveckla företags- och hybridprogram. | |
|  | | |
| **Resurser:** |Du vill inte använda IT-resurser för konfiguration och hantering av den underliggande infrastrukturen, utan vill fokusera på programnivån. |Du har några IT-resurser som ansvarar för konfiguration och hantering. Vissa automatiserade funktioner kan avsevärt förenkla detta arbete. |
| **Totalkostnad för ägarskap:** |Eliminerar kostnaderna för maskinvara och reducerar de administrativa kostnaderna. |Eliminerar maskinvarukostnaderna. |
| **Verksamhetskontinuitet:** |Utöver inbyggt feltolerant infrastruktur, erbjuder Azure SQL Database även funktioner som [automatiserad säkerhetskopiering](sql-database-automated-backups.md), [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore), [geo-återställning](sql-database-recovery-using-backups.md#geo-restore) och [aktiv geo-replikering](sql-database-geo-replication-overview.md) för att öka verksamhetskontinuiteten. Mer information finns i [översikt över verksamhetskontinuitet i SQL Database](sql-database-business-continuity.md). |Med SQL Server på Azure Virtual Machines kan du konfigurera en lösning med hög tillgänglighet och haveriberedskap för din databas specifika behov. Du kan därmed få ett system som är höggradigt optimerat för just ditt program. Du kan själv testa och köra felväxling vid behov. Mer information finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Hybridmoln:** |Ditt lokala program har åtkomst till data i Azure SQL Database. |Med SQL Server på virtuella Azure-datorer kan du ha program som kör delvis i molnet och delvis lokalt. Du kan till exempel utöka ditt lokala nätverk och Active Directory-domän till molnet via [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Dessutom kan du kan lagra lokala datafiler i Azure Storage med [SQL Server-datafiler i Azure](http://msdn.microsoft.com/library/dn385720.aspx). Mer information finns i [Introduktion till SQL Server 2014 Hybridmoln](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Har stöd för [SQL Server-transaktionsreplikering](https://msdn.microsoft.com/library/mt589530.aspx) som en prenumerant för att replikera data. |Har fullständigt stöd för [SQL Server-transaktionsreplikering](https://msdn.microsoft.com/library/mt589530.aspx), [AlwaysOn-tillgänglighetsgrupper](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services och loggöverföring för att replikera data. Dessutom finns fullständigt stöd för traditionella SQL Server-säkerhetskopieringar | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Verksamhetsmotivering för att välja Azure SQL Database eller SQL Server på virtuella Azure-datorer
### <a name="cost"></a>Kostnad
Oavsett om du är en startup med dålig kassa, eller ett team i ett etablerat företag som har en begränsad budget. Budgetbegränsningar är vanligtvis den främsta motivationsfaktorn när man bestämmer var man ska ha sina databaser. I det här avsnittet ska vi först gå igenom de grundläggande koncepten med debitering och licensiering i Azure för dessa två relationsdatabasalternativ: SQL Database och SQL Server på Azure Virtual Machines. Du lär dig också hur du beräknar den totala programkostnaden.

#### <a name="billing-and-licensing-basics"></a>Debitering och licensiering
**SQL Database** säljs till kunder som en tjänst, inte med en licens.  [SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) säljs med en medföljande licens som du betalar per minut. Om du har en befintlig licens kan du också använda den.  

För nuvarande finns **SQL Database** tillgänglig i flera servicenivåer, där alla debiteras timvis till en fast kostnad som baseras på den servicenivå och prestandanivå du valt. Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/). Servicenivåerna Basic, Standard och Premium är skapade för att leverera förutsägbar prestanda med flera prestandanivåer som matchar ditt programs högsta krav. Du kan byta mellan servicenivåer och prestandanivåer för att matcha ditt programs ändrade behov för genomströmning. Om din databas har stora transaktionsvolymer och behöver stödja flera samtidiga användare, rekommenderar vi premiumnivån. För den senaste informationen om de servicenivåer som stöds för tillfället, kan du se [Azure SQL Database Servicenivåer](sql-database-service-tiers.md). Du kan också skapa [elastiska pooler](sql-database-elastic-pool.md) för att dela prestandaresurser mellan databasinstanser.

Med **SQL Database** så konfigureras, korrigeras och uppgraderas databasens programvara automatiskt av Microsoft, vilket minskar dina administrationskostnader. Dessutom gör dess [inbyggda säkerhetskopierings](sql-database-automated-backups.md)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser.

Med **SQL Server på Azure Virtual Machines** kan du använda valfri SQL Server-avbildning som plattformen stöder (som omfattar en licens) eller använda din egen SQL Server-licens. Alla SQL Server-versioner (2008R2, 2012, 2014, 2016) och utgåvor (Developer, Express, Web, Standard, Enterprise) som stöds är tillgängliga. Det finns också BYOL-versioner (Bring-Your-Own-License) av avbildningarna. När du använder de avbildningar som Azure tillhandahåller beror driftskostnaderna på storleken på de virtuella datorerna samt vilken utgåva av SQL Server du väljer. Oavsett VM-storlek eller SQL Server-utgåva, betalar du licensieringskostnader per minut för SQL Server och Windows Server, tillsammans med kostnaden för Azure Storage för VM-diskarna. Betalningsalternativet per minut låter dig använda SQL Server så länge du behöver utan att köpa ytterligare SQL Server-licenser. Om du använder din egen SQL Server-licens i Azure, debiteras du bara för Windows Server och lagringskostnader. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Beräkna den totala programkostnaden
När du börjar använda en molnplattform inkluderar kostnaden för att köra programmet även utvecklings- och administrationskostnaderna, samt plattformstjänstkostnaderna för det offentliga molnet.

Här följer en detaljerad kostnadskalkyl för ditt program på SQL Database och SQL Server på virtuella Azure-datorer:

**Med Azure SQL Database:**

*Totalkostnad för programmet = kraftigt minimerade administrationskostnader + kostnader för programvaruutveckling + SQL Database-tjänstekostnader*

**När du använder SQL Server på virtuella Azure-datorer:**

*Totalkostnaden för programmet = mycket mindre programvaruutvecklingskostnad + administrationskostnader + licensieringskostnader för SQL Server och Windows Server + kostnader för Azure Storage*

Mer information om priser finns i följande resurser:

* [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
* [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) för [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) och för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
* [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

> [!NOTE]
> Det finns vissa funktioner på SQL Server som inte är tillämpliga eller tillgängliga med SQL Databaser. Mer information finns i [SQL Database Features](sql-database-features.md) (SQL Database-funktioner) och [SQL Database Transact-SQL information](sql-database-transact-sql-information.md) (Information om SQL Database Transact-SQL). Om du flyttar en befintlig SQL Server-lösning till molnet, kan du kolla [migrera en SQL Server-databas till Azure SQL Database](sql-database-cloud-migrate.md). När du migrerar ett befintligt, lokalt SQL Server-program till SQL Database, bör du överväga att uppdatera programmet för att dra nytta av alla möjligheter som molntjänster erbjuder. Du kan exempelvis överväga att använda [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/) eller [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) som värd för ditt programlager för att öka kostnadsfördelar.
> 
> 

### <a name="administration"></a>Administration
För många företag handlar beslutet att övergå till en molntjänst lika mycket om att minska administrativ komplexitet som kostnad. Med **SQL Database** administrerar Microsoft den underliggande maskinvaran. Microsoft replikerar automatiskt alla data för att tillhandahålla hög tillgänglighet, konfigurerar och uppgraderar databasprogramvaran, hanterar belastningsutjämning och utför transparent redundansväxling i händelse av ett serverfel. Du kan fortsätta att administrera din databas, men du behöver inte längre hantera databasmotorn, operativsystemet för servern eller maskinvaran.  Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet.

Med **SQL Server på Azure Virtual Machines** har du fullständig kontroll över operativsystemet och konfigurationen av SQL Server-instansen. Med en VM bestämmer du själv när operativsystem och databasprogramvara ska uppdateras/uppgraderas och när ytterligare programvara, t.ex. antivirusprogram, ska installeras. Vissa automatiska funktioner kan avsevärt underlätta arbetet med korrigeringar, säkerhetskopiering och hög tillgänglighet. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer. Med Azure kan du ändra storleken på en virtuell dator efter behov. Mer information finns i [Storlekar för virtuella Azure-datorer och Azure-molntjänster](../virtual-machines/windows/sizes.md). 

### <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)
För många IT-avdelningar är det av högsta vikt att uppfylla skyldigheterna på upptid enligt serviceavtalet (SLA). I det här avsnittet tittar vi på de SLA som är tillämpliga för varje databasalternativ.

För **SQL Database**-servicenivåerna Basic, Standard och Premium ger Microsoft en SLA med 99,99 % tillgänglighet. Den senaste informationen finns i [Serviceavtalet](https://azure.microsoft.com/support/legal/sla/sql-database/). Den senaste informationen om servicenivåer för SQL Database och de planer för verksamhetskontinuitet som stöds finns i [servicenivåer](sql-database-service-tiers.md).

För **SQL Server som kör på virtuella Azure-datorer** erbjuder Microsoft en tillgänglighets-SLA på 99,95 %, vilket bara täcker den virtuella datorn. Det här SLA:t omfattar inte de processer som körs på den virtuella datorn (till exempel SQL Server) som kräver att du är värd för minst två VM-instanser i en tillgänglighetsuppsättning. Den senaste informationen finns på [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För hög tillgänglighet (HA) för databaser inom VM:ar, bör du konfigurera en av de alternativ för hög tillgänglighet som stöds i SQL Server, som [AlwaysOn-tillgänglighetsgrupper](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Användningen av ett alternativ för hög tillgänglighet som stöds medför inget nytt SLA, men ger > 99,99 % databastillgänglighet.

### <a name="market"></a>Tid till marknad
**SQL Database** är den rätta lösningen för molnutformade program där utvecklarproduktivitet och en snabb tid till marknad är avgörande. Med sin programmässiga DBA-lika funktionalitet, är det perfekt för molnarkitekter och utveckare, eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen. Du kan till exempel använda [REST-API:n](http://msdn.microsoft.com/library/azure/dn505719.aspx) och [PowerShell-Cmdletar](http://msdn.microsoft.com/library/mt740629.aspx) för att automatisera och hantera administrativa åtgärder för tusentals databaser. Funktioner som [elastiska pooler](sql-database-elastic-pool.md) låter dig fokusera på programlagret och leverera din lösning till marknaden snabbare.

**SQL Server som körs på Azure Virtual Machines** är perfekt om dina befintliga eller nya program kräver stora databaser, relaterade databaser eller åtkomst till alla funktioner i SQL Server eller Windows. Det kan också passa om du vill migrera befintliga lokala program och databaser till Azure som de är. Eftersom du inte behöver ändra presentationen, programmet eller datalagren, kan du spara in tid och budget på att omstrukturera din befintliga lösning. Istället kan du fokusera på att migrera alla dina lösningar till Azure och på att genomföra prestandaoptimeringar som kan krävas av Azure-plattformen. Mer information finns i [Bästa praxis för prestanda i SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Sammanfattning
Den här artikeln har gått igenom SQL Database och SQL Server på virtuella Azure-datorer (VM:ar) och pratat om några vanliga verksamhetsmotivationer som kan påverka ditt beslut. Här följer en sammanfattning av förslag som du kan tänka på:

Välj **Azure SQL Database** om:

* Du utvecklar nya molnbaserade program för att utnyttja de besparingar och den prestandaoptimering som molntjänster medför. Den här metoden ger dig fördelarna med en fullständigt hanterad molntjänst, bidrar till att sänka den initiala tiden till marknad och kan ge långsiktiga kostnadsoptimeringar.
* Du vill låta Microsoft hantera vanliga hanteringsåtgärder på din databas och kräver en bättre tillgänglighets-SLA för dina databaser.

Välj **SQL Server på virtuella Azure-datorer** om:

* Du har befintliga lokala program som du vill migrera eller utöka till molnet, eller om du vill utveckla företagsprogram som är större än 1 TB. Den här lösningen har fördelar som till exempel fullständig kompatibilitet med SQL, stor databaskapacitet, full kontroll över SQL Server och Windows samt säker händelsedirigering nedåt (tunneling) till den lokala infrastrukturen. Lösningen minimerar kostnaderna för utveckling och ändringar av befintliga program.
* Du har befintliga IT-resurser och kan själv hantera korrigeringar, säkerhetskopieringar och hög databastillgänglighet. Observera att vissa automatiska funktioner avsevärt förenklar dessa åtgärder. 

## <a name="next-steps"></a>Nästa steg
* Se [Din första Azure SQL Database](sql-database-get-started.md) för att komma igång med SQL Database.
* Se [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
* Se [Etablera en virtuell SQL Server-dator i Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) för att komma igång med SQL Server på virtuella Azure-datorer.


