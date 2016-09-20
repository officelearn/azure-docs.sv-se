<properties
    pageTitle="SQL (PaaS) Database kontra SQL Server i molnet på VM:ar (IaaS) | Microsoft Azure"
    description="Läs mer om vilket molnbaserat SQL Server-alternativ som passar ditt program: Azure SQL (PaaS) Database eller SQL Server i molnet på Azure Virtual Machines."
    services="sql-database, virtual-machines"
    keywords="SQL Server-moln, SQL Server i molnet, PaaS-databas, moln-SQL Server, DBaaS"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/20/2016"
    ms.author="carlrab"/>

# Välj ett molnbaserat SQL Server-alternativ: Azure SQL (PaaS) Database eller SQL Server på virtuella Azure-datorer (IaaS)

Azure har två alternativ för att hantera SQL Server-arbetsbelastningar i Microsoft Azure:

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): en SQL-databas inbyggd i molnet, kallas även för en plattform som tjänst-databas (PaaS) eller en databas som en tjänst (DBaaS) som är optimerad för apputveckling för programvara som en tjänst (SaaS). Den är kompatibel med det stora flertalet av SQL Server-funktionerna. Mer information om PaaS finns i [Vad är PaaS](https://azure.microsoft.com/overview/what-is-paas/). Du hittar en video här: [SAIIK: PaaS mot IaaS – navigera i beslutsträdet: Azure SQL DB mot SQL Server i en virtuell dator](https://channel9.msdn.com/Series/SAIIK-SQL-Server-on-Azure-IaaS-Implementation-Kit/SAIIK-PaaS-vs-IaaS).
* [SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server som är installerad och ligger i molnet på virtuella Windows Server-datorer (VM:ar) och körs i Azure, vilket också kallas för infrastruktur som en tjänst (IaaS).

Lär dig hur varje alternativ passar in i Microsofts dataplattform och få hjälp med att matcha rätt alternativ för dina verksamhetsbehov. Oavsett om du prioriterar kostnadsbesparingar eller en minimering av administration över allt annat, kan den här artikeln hjälpa dig att välja det angreppssätt som ger bäst resultat för de verksamhetskrav som är viktigast för dig.


## Microsofts dataplattform

En av de första sakerna man ska förstå när det gäller Azure kontra lokala SQL Server-databaser, är att det är möjligt att använda allt. Microsofts dataplattform utnyttjar SQL Server-teknologi och gör den tillgänglig på fysiska, lokala datorer, i privata molnmiljöer, tredjeparts privata molnmiljöer och det offentliga molnet. Det gör att du kan uppfylla unika och skilda verksamhetsbehov genom en kombination av lokala och molnbelägna distributioner, samtidigt som du kan använda samma uppsättningar med serverprodukter, utvecklingsverktyg och expertis i alla dessa miljöer.

   ![SQL Server-molnalternativ: SQL-server på IaaS, eller SaaS-SQL-databas i molnet.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Som diagrammet visar, särskiljs de olika alternativen efter den administrativa nivån du har över infrastrukturen (på x-axeln) och efter den kostnadseffektivitet som kan uppnås av konsolidering på databasnivå och automatisering (på y-axeln).

När du skapar ett program, finns det fyra grundläggande alternativ för att hantera SQL Serverdelen av programmet:

- SQL Server på icke-virtualiserade fysiska datorer
- SQL Server på lokala virtualiserade datorer (privat moln)
- SQL Server på virtuella Azure-datorer (offentligt Microsoft-moln)
- Azure SQL Database (offentligt Microsoft-moln)

I följande avsnitt kommer vi att gå igenom SQL Server i det offentliga Microsoft-molnet: Azure SQL Database och SQL Server på virtuella Azure-datorer. Dessutom kommer vi att gå igenom vanliga verksamhetsmotiv för att fastställa vilket alternativ som är bäst för ditt fall.

## En närmare titt på Azure SQL Database och SQL Server på virtuella Azure-datorer

**Azure SQL Database** är en relationell databas som en tjänst (DBaaS) som ligger i Azure-molnet och faller inom branschkategorierna *Programvara som en tjänst (SaaS)* och *Plattform som en tjänst (PaaS)*. [SQL Database](sql-database-technical-overview.md) bygger på standardiserad maskinvara och programvara som ägs, finns hos och hanteras av Microsoft. Med SQL Database, kan du utveckla direkt i tjänsten med hjälp av inbyggda funktioner. När du använder SQL Database, betalar du per användning med alternativ att skala upp eller ut för mer kraft utan avbrott.

**SQL Server på virtuella Azure-datorer (VM:ar)** faller inom branschkategorin *Infrastruktur som en tjänst (IaaS)* och låter dig köra SQL Server på en virtuell dator i molnet. På ett liknande sätt som SQL Database så är den byggd på standardiserad maskinvara som ägs, finns hos och hanteras av Microsoft. När du använder SQL Server på en VM så kan du antingen använda din egen SQL Server-licens, eller använda dig av en förlicensierad SQL Server-avbildning från Azure-portalen.

Generellt sett är de här två SQL-alternativen optimerade för olika syften:

- **SQL Database** är optimerad för att minska övergripande kostnader till ett minimum, för etablering och hantering av många databaser. Det minskar löpande administrativa kostnader eftersom du inte behöver hantera några virtuella datorer, operativsystem eller någon databasprogramvara. Det inkluderar uppgraderingar, hög tillgänglighet och [säkerhetskopieringar](sql-database-automated-backups.md). Generellt sett kan Azure SQL Database kraftigt öka antalet databaser som kan hanteras av en enskild IT- eller utvecklingsresurs.
- **SQL Server som körs på virtuella Azure-datorer** är optimerad för att utöka befintliga lokala SQL Server-program till molnet i ett hybridscenario eller vid distribution av ett befintligt program till Azure i ett migrerings- eller dev/test-scenario. Ett exempel på ett hybridscenario är att ha sekundära databasrepliker i Azure med hjälp av [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md). Med SQL Server på virtuella Azure-datorer, har du fullständiga administrativa rättigheter för en dedikerad instans av SQL Server och en molnbaserad VM. Det är det perfekta valet när en organisation redan har IT-resurser tillgängliga för att underhålla de virtuella datorerna. Med SQL Server på VM:ar, kan du skapa ett höganpassat system som kan hantera ditt programs specifika krav på prestanda och tillgänglighet.

Följande tabell sammanfattar de huvudsakliga egenskaperna för SQL Database och SQL Server på virtuella Azure-datorer:

|       | SQL Database | SQL Server på en virtuell Azure-dator|
| -------------- | ------------ | ---------------------- |
| **Bäst för:** | Nya molnbaserade program med tidsbegränsningar på utveckling och marknadsföring. | Befintliga program som kräver snabb migrering till molnet med minimala ändringar. Snabba utvecklings- och test-scenarier där du inte vill köpa lokal SQL Server-maskinvara som inte är för produktion. |
|| Program som kräver inbyggd hög tillgänglighet, haveriberedskap och uppgraderingsmekanismer. | Program som inte kräver inbyggd hög tillgänglighet, haveriberedskap och uppgraderingsmekanismer. |
||Team som inte vill hantera de underliggande operativsystemen och konfigurationsinställningarna.| Om du behöver en anpassad IT-miljö med fullständiga administrativa rättigheter.|
||Databaser för upp till 1 TB eller större databaser som kan vara [vågrätt eller lodrätt partitionerade](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) med ett skalbart mönster.|Databaser som är större än 1 TB som inte kan [partitioneras vågrätt eller lodrätt](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling).|
||[Skapa programvara som en tjänst (SaaS)-program](sql-database-design-patterns-multi-tenancy-saas-applications.md).| Skapa hybridprogram|
|||||
|**Resurser:**|Du vill inte anställa IT-resurser för support och underhåll av underliggande den infrastrukturen utan vill fokusera på programskiktet.|Du har IT-resurser för support och underhåll.|
|**Totalkostnad för ägarskap:**|Eliminerar kostnaderna för maskinvara och reducerar de administrativa kostnaderna.|Eliminerar maskinvarukostnaderna.|
|**Verksamhetskontinuitet:**|Utöver inbyggt feltolerant infrastruktur, erbjuder Azure SQL Database även funktioner som [automatiserad säkerhetskopiering](sql-database-automated-backups.md), [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore), [geo-återställning](sql-database-recovery-using-backups.md#geo-restore) och [aktiv geo-replikering](sql-database-active-geo-replication.md) för att öka verksamhetskontinuiteten. Mer information finns i [översikt över verksamhetskontinuitet i SQL Database](sql-database-business-continuity.md).|SQL Server på virtuella Azure-datorer låter dig ställa in en lösning med hög tillgänglighet och haveriberedskap för de specifika behoven för din databas. Du kan därmed få ett system som är höggradigt optimerat för just ditt program. Du kan själv testa och köra felväxling vid behov. Mer information finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|**Hybridmoln:**|Ditt lokala program har åtkomst till data i Azure SQL Database.|Med SQL Server på virtuella Azure-datorer kan du ha program som kör delvis i molnet och delvis lokalt. Du kan till exempel utöka ditt lokala nätverk och Active Directory-domän till molnet via [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Dessutom kan du kan lagra lokala datafiler i Azure Storage med [SQL Server-datafiler i Azure](http://msdn.microsoft.com/library/dn385720.aspx). Mer information finns i [Introduktion till SQL Server 2014 Hybridmoln](http://msdn.microsoft.com/library/dn606154.aspx).|
||Stöder [Transaktionsreplikering för SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) som prenumerant.|Stöder [Transaktionsreplikering för SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), haveriberedskap och [AlwaysOn-repliker i virtuella Azure-datorer](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|||||
|||||

## Verksamhetsmotivering för att välja Azure SQL Database eller SQL Server på virtuella Azure-datorer

### Kostnad

Oavsett om du är en startup med dålig kassa, eller ett team i ett etablerat företag som har en begränsad budget. Budgetbegränsningar är vanligtvis den främsta motivationsfaktorn när man bestämmer var man ska ha sina databaser. I det här avsnittet kommer vi först gå igenom de grundläggande koncepten med debitering och licensiering i Azure för de här två relationella databasalternativen: SQL Database och SQL Server på virtuella Azure-datorer. Sedan tar vi oss en titt på hur man kan beräkna den totala kostnaden.

#### Debitering och licensiering

**SQL-databas** säljs till kunder som en tjänst, inte med en licens, medan [licensiering av SQL Server på virtuella datorer i Azure](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) antingen kräver SQL Server-licensiering ”per minut” eller en egen licens via Software Assurance.

För nuvarande finns **SQL Database** tillgänglig i flera servicenivåer, där alla debiteras timvis till en fast kostnad som baseras på den servicenivå och prestandanivå du valt. Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/). Servicenivåerna Basic, Standard och Premium är skapade för att leverera förutsägbar prestanda med flera prestandanivåer som matchar ditt programs högsta krav. Du kan byta mellan servicenivåer och prestandanivåer för att matcha ditt programs ändrade behov för genomströmning. Om din databas har stora transaktionsvolymer och behöver stödja flera samtidiga användare, rekommenderar vi premiumnivån. För den senaste informationen om de servicenivåer som stöds för tillfället, kan du se [Azure SQL Database Servicenivåer](sql-database-service-tiers.md). Du kan också skapa [elastiska databaspooler](sql-database-elastic-pool.md) för att dela prestandaresurser mellan databasinstanser.

Med **SQL Database** så konfigureras, korrigeras och uppgraderas databasens programvara automatiskt av Microsoft, vilket minskar dina administrationskostnader. Dessutom gör dess [inbyggda säkerhetskopierings](sql-database-automated-backups.md)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser.

Med **SQL Server på virtuella Azure-datorer** kan du antingen använda den SQL Server-avbildning som plattformen erbjuder (som inkluderar en licens) eller använda din egen SQL Server-licens. När du använder de avbildningar som Azure erbjuder, beror driftskostnaderna på VM-storleken såväl som vilken utgåva av SQL Server som du väljer. Oavsett VM-storlek eller SQL Server-utgåva, betalar du licensieringskostnader per minut för SQL Server och Windows Server, tillsammans med kostnaden för Azure Storage för VM-diskarna. Betalningsalternativet per minut låter dig använda SQL Server så länge du behöver utan att köpa ytterligare SQL Server-licenser. Om du använder din egen SQL Server-licens i Azure, debiteras du bara för Windows Server och lagringskostnader. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### Beräkna den totala programkostnaden

När du börjar använda en molnplattform, inkluderar kostnaden för att köra ditt program först och främst utvecklings- och administrationskostnader samt tjänstekostnaderna som den offentliga molnplattformen kräver.

Här följer en detaljerad kostnadskalkyl för ditt program på SQL Database och SQL Server på virtuella Azure-datorer:

**Med Azure SQL Database:**

*Totalkostnad för programmet = kraftigt minimerade administrationskostnader + kostnader för programvaruutveckling + SQL Database-tjänstekostnader*

**När du använder SQL Server på virtuella Azure-datorer:**

*Totalkostnad för programmet = minimerade kostnader för programvaruutveckling/modifiering + administrationskostnader + licensieringskostnader för SQL Server och Windows Server + kostnader för Azure Storage*

Mer information om priser finns i följande resurser:

- [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
- [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) för [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) och för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Det finns vissa funktioner på SQL Server som inte är tillämpliga eller tillgängliga med SQL Databaser. Se [SQL Database allmänna begränsningar och riktlinjer](sql-database-general-limitations.md) och [SQL Database Transact-SQL-information](sql-database-transact-sql-information.md) för mer information. Om du flyttar en befintlig SQL Server-lösning till molnet, kan du kolla [migrera en SQL Server-databas till Azure SQL Database](sql-database-cloud-migrate.md). När du migrerar ett befintligt, lokalt SQL Server-program till SQL Database, bör du överväga att uppdatera programmet för att dra nytta av alla möjligheter som molntjänster erbjuder. Du kan exempelvis överväga att använda [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/) eller [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) som värd för ditt programlager för att öka kostnadsfördelar.

### Administration

För många företag handlar beslutet att övergå till en molntjänst lika mycket om att minska administrativ komplexitet som kostnad. Med **SQL Database** administrerar Microsoft den underliggande maskinvaran och replikerar automatiskt alla data för att ge dig hög tillgänglighet, samt konfigurerar och uppgraderar databasprogramvaran, hanterar belastningsutjämning och genomför transparent växling vid serverfel. Du kan fortsätta att administrera din databas, men du behöver inte längre hantera databasmotorn, operativsystemet för servern eller maskinvaran.  Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet.

Det kanske dock är så att du har expertisen och viljan att bibehålla kontrollen över databasplatsen ända ner på disknivå. Med **SQL Server på virtuella Azure-datorer** har du fullständig kontroll över operativsystemet och konfiguration för SQL Server-instansen. Med en VM, är det upp till dig att avgöra när uppgraderingar/uppdateringar av operativsystem och databas-programvara sker och när ytterligare programvara som antivirus och säkerhetskopieringsverktyg ska installeras. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer.  Azure låter dig exempelvis ändra storleken på en VM efter behov. Mer information finns i [Storlekar för virtuella Azure-datorer och Azure-molntjänster](../virtual-machines/virtual-machines-linux-sizes.md).

### Serviceavtal (SLA)

För många IT-avdelningar är det av högsta vikt att uppfylla skyldigheterna på upptid enligt serviceavtalet (SLA). I det här avsnittet tittar vi på de SLA som är tillämpliga för varje databasalternativ.

För **SQL Database**-servicenivåerna Basic, Standard och Premium ger Microsoft en SLA med 99,99 % tillgänglighet. Den senaste informationen finns i [Serviceavtalet](https://azure.microsoft.com/support/legal/sla/sql-database/). Den senaste informationen om servicenivåer för SQL Database och de planer för verksamhetskontinuitet som stöds finns i [servicenivåer](sql-database-service-tiers.md).

För **SQL Server som kör på virtuella Azure-datorer** erbjuder Microsoft en tillgänglighets-SLA på 99,95 %, vilket bara täcker den virtuella datorn. Det här SLA:t omfattar inte de processer som körs på den virtuella datorn (till exempel SQL Server) som kräver att du är värd för minst två VM-instanser i en tillgänglighetsuppsättning. Den senaste informationen finns på [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För hög tillgänglighet (HA) för databaser inom VM:ar, bör du konfigurera en av de alternativ för hög tillgänglighet som stöds i SQL Server, som [AlwaysOn-tillgänglighetsgrupper](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

### <a name="market"></a>Tid till marknad

**SQL Database** är den rätta lösningen för molnutformade program där utvecklarproduktivitet och en snabb tid till marknad är avgörande. Med sin programmässiga DBA-lika funktionalitet, är det perfekt för molnarkitekter och utveckare, eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen. Du kan till exempel använda [REST-API:n](http://msdn.microsoft.com/library/azure/dn505719.aspx) och [PowerShell-Cmdletar](http://msdn.microsoft.com/library/azure/dn546726.aspx) för att automatisera och hantera administrativa åtgärder för tusentals databaser. Funktioner som [elastiska databaspooler](sql-database-elastic-pool.md) låter dig fokusera på programlagret och leverera din lösning till marknaden snabbare.

**SQL Server som kör på virtuella Azure-datorer** är perfekt om dina befintliga eller nya program kräver åtkomst till och kontroll av alla funktioner i en SQL Server-instans. Det kan också passa om du vill migrera befintliga lokala program och databaser till Azure som de är. Eftersom du inte behöver ändra presentationen, programmet eller datalagren, kan du spara in tid och budget på att omstrukturera din befintliga lösning. Istället kan du fokusera på att migrera alla dina lösningar till Azure och på att genomföra prestandaoptimeringar som kan krävas av Azure-plattformen. Mer information finns i [Bästa praxis för prestanda i SQL Server på Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-performance.md).

## Sammanfattning

Den här artikeln har gått igenom SQL Database och SQL Server på virtuella Azure-datorer (VM:ar) och pratat om några vanliga verksamhetsmotivationer som kan påverka ditt beslut. Här följer en sammanfattning av förslag som du kan tänka på:

Välj **Azure SQL Database** om:

- Du skapar nya molnbaserade program eller vill migrera din befintliga SQL Server-lösning för att kunna dra nytta av de besparingar och prestandaoptimeringar som molntjänster kan erbjuda. Den här metoden ger dig fördelarna med en fullständigt hanterad molntjänst, hjälper att sänka den initiala tid till marknaden och kan ge långsiktiga kostnadsoptimeringar.

- Du vill låta Microsoft hantera vanliga hanteringsåtgärder på din databas och kräver en bättre tillgänglighets-SLA för dina databaser.



Välj **SQL Server på virtuella Azure-datorer** om:

- Du har befintliga lokala program och vill sluta underhålla din egen maskinvara, eller om du överväger hybridlösningar. Den här metoden låter dig komma åt en hög databaskapacitet snabbare, samtidigt som det förenklar anslutning till dina lokala program via en säker tunnel.

- Du har befintliga IT-resurser, behöver fullständiga administrativa rättigheter på din SQL Server och kräver full kompatibilitet med lokal SQL Server. Den här metoden låter dig minimera utvecklings- eller ändringskostnader för befintliga program och ger dig flexibiliteten att köra de flesta program. Dessutom ger den dig fullständig kontroll över din VM, operativsystemet och databaskonfigurationen.


## Nästa steg
- Se [SQL Database-självstudier: Skapa en SQL-databas på några minuter med hjälp av Azure-portalen](sql-database-get-started.md) för att komma igång med SQL Database.
- Se [Priser för SQL Database] (https://azure.microsoft.com/pricing/details/sql-database/).
- Se [Etablera en virtuell SQL Server-dator i Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) för att komma igång med SQL Server på virtuella Azure-datorer.
- Se [Utbildningsvägen för SQL Server på en virtuell dator i Azure](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).



<!--HONumber=sep16_HO1-->


