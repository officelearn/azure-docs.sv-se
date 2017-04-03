---
title: "Vad är Azure SQL Data Warehouse? | Microsoft Docs"
description: "En distribuerad databas i företagsklass som kan bearbeta petabytevolymer med relationella och icke-relationella data. Den är branschens första informationslager i molnet som kan växa, krympa och pausa på sekunden."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 2/28/2017
ms.author: jrj;mausher;kevin;barbkess;elbutter
translationtype: Human Translation
ms.sourcegitcommit: bf73ad830226626ddf41cc4ae80e714abf8bcfc2
ms.openlocfilehash: 19e87c61493bd4620120b39a533e9e4b64517538
ms.lasthandoff: 03/01/2017


---
# <a name="what-is-azure-sql-data-warehouse"></a>Vad är Azure SQL Data Warehouse?
Azure SQL Data Warehouse är en molnbaserad, skalbar, relationell databas med Massivt Parallell Bearbetning (MPP) som kan bearbeta massiva mängder data. 

SQL Data Warehouse:

* Kombinerar SQL Server-relationsdatabasen med skalbarhetsfunktionerna i Azure-molnet. 
* Frikopplar lagring från beräkning.
* Aktiverar ökning, minskning, pausande eller återupptagande av beräkning. 
* Integreras i Azure-plattformen.
* Använder SQL Server Transact-SQL (T-SQL) och verktyg.
* Uppfyller olika juridiska och affärssäkerhetskrav som SOC och ISO.

Den här artikeln beskriver de viktigaste funktionerna i SQL Data Warehouse.

## <a name="massively-parallel-processing-architecture"></a>MPP-arkitektur (Massively Parallel Processing)
SQL Data Warehouse är ett distribuerat MPP-databassystem. I bakgrunden sprider SQL Data Warehouse dina data mellan många SN-baserade (shared-nothing) lagrings- och bearbetningsenheter. Data lagras i ett lokalt redundant Premium-lagringslager ovanpå, på vilket dynamiskt länkade Compute-noder kör frågor. SQL Data Warehouse bygger på en D&C-modell (”divide and conquer”) för belastningar som körs och komplexa frågor. Begäranden tas emot av kontrollnoden, optimeras för distribution och skickas sedan till Compute-noder som utför arbetet parallellt.

Med fristående lagringsutrymme och databearbetning kan SQL Data Warehouse:

* Öka eller minska lagringsstorlek oberoende av beräkning.
* Öka eller minska beräkningskapacitet utan att flytta data.
* Pausa beräkningskapaciteten, lämna data intakta och betala endast för lagring.
* Återuppta beräkningskapacitet under driftstimmar.

Följande diagram illustrerar arkitekturen i detalj.

![SQL Data Warehouse-arkitektur][1]

**Kontrollnod:** Kontrollnoden hanterar och optimerar frågor. Det är den som är klientdelen som interagerar med alla program och anslutningar. Control-noden i SQL Data Warehouse drivs av SQL Database och det ser ut och känns på samma sätt att ansluta till den. Under ytan samordnar Control-noden all dataflytt och alla beräkningar som krävs för att köra parallella frågor på dina distribuerade data. När du skickar en T-SQL-fråga till SQL Data Warehouse omvandlar Control-noden frågan till separata frågor som körs parallellt på varje Compute-nod.

**Beräkningsnoder:** Beräkningsnoderna utgör kraften bakom SQL Data Warehouse. De är SQL-databaser som lagrar dina data och bearbetar din fråga. När du lägger till data distribuerar SQL Data Warehouse raderna till beräkningsnoderna. Beräkningsnoderna ansvarar också för att köra parallella frågor mot dina data. Efter bearbetning, skickar de resultaten tillbaka till Control-noden. För att slutföra frågan, aggregerar Control-noden resultaten och returnerar slutresultatet.

**Lagring:** Dina data lagras i Azure Blob Storage. När Compute-noder interagerar med dina data, skriver de och läser direkt till och från blobblagring. Eftersom Azure Storage expanderar helt transparent och i stor omfattning, kan SQL Data Warehouse också göra det. Eftersom beräkning och lagring sker oberoende av varandra, kan SQL Data Warehouse automatiskt skala lagring separat från beräkning och vice versa. Azure Blob Storage är också helt feltolerant och effektiviserar säkerhetskopierings- och återställningsprocessen.

**DMS (Data Movement Service):** DMS-tjänsten flyttar data mellan noderna. DMS ger Compute-noderna åtkomst till de data de behöver för kopplingar och aggregeringar. DMS är inte en Azure-tjänst. Det är en Windows-tjänst som körs samtidigt som SQL Database på alla noderna. DMS är en bakgrundsprocess som inte går att interagera med direkt. När du tittar på frågeplaner kommer du dock att se när DMS-åtgärder sker eftersom det är nödvändigt att flytta data för att köra varje fråga parallellt.

## <a name="optimized-for-data-warehouse-workloads"></a>Optimerad för arbetsbelastningar i informationslager
MPP-modellen har ett antal prestandaoptimeringar som är särskilt utformade för hantering av informationslager, t.ex.:

* En distribuerad frågeoptimerare och en uppsättning komplex statistik för alla data. Med information om datastorlek och distribution kan tjänsten optimera frågor genom att uppskatta kostnaden för specifika distribuerade frågeåtgärder.
* Avancerade algoritmer och tekniker som integrerats i processen med dataflytt för att effektivt flytta data mellan bearbetningsresurser efter behov för att genomföra frågan. Dessa dataflyttningsåtgärder är inbyggda och alla optimeringar av Data Movement Service sker automatiskt.
* Grupperade **columnstore**-index (kolumnlagringsindex) som standard. Genom att använda kolumnbaserad lagring får SQL Data Warehouse ut upp till i genomsnitt 5 gånger så hög komprimering jämfört med traditionell, radbaserad lagring och upp till minst 10 gånger så höga frågeprestanda. Analytics-frågor som behöver skanna ett stort antal rader fungerar bättre på kolumnlagringsindex.


## <a name="predictable-and-scalable-performance-with-data-warehouse-units"></a>Förutsägbara och skalbara prestanda med informationslagerenheter
SQL Data Warehouse är byggt med liknande teknik som SQL Database, vilket innebär att experter kan förvänta sig konsekventa och förutsägbara prestanda för analytiska frågor. Användarna kan förvänta sig att se prestanda skalas linjärt när de lägger till eller tar bort Compute-noder. Allokeringen av resurser till SQL Data Warehouse mäts i informationslagerenheter (DWU, Data Warehouse Unit). DWU är ett mått på underliggande resurser, t.ex. CPU, minne och IOPS, som allokeras till SQL Data Warehouse. Om antalet DWU-enheter ökar, ökar resurserna och prestanda. Mer specifikt ser DWU-enheterna till att:

* Du kan skala informationslagret utan att behöva bry dig om underliggande maskinvara eller programvara.
* Du kan förutsäga prestandaförbättringar för en DWU-nivå innan du ändrar beräkningen av informationslagret.
* Den underliggande maskin- och programvaran i din instans kan ändras eller flyttas utan att arbetsbelastningens prestanda påverkas.
* Microsoft kan förbättra den underliggande arkitekturen för tjänsten utan att arbetsbelastningens prestanda påverkas.
* Microsoft kan snabbt förbättra prestanda i SQL Data Warehouse på ett sätt som är skalbart och som påverkar systemet jämnt.

DWU-enheter ger ett mått på tre mätvärden med stark korrelation till informationslagrets arbetsbelastningsprestanda. Följande nyckelmått för arbetsbelastningen skalas linjärt med DWU-enheterna.

**Genomsökning/aggregering:** En standardinformationslagerfråga som genomsöker ett stort antal rader och sedan genomför en komplex aggregering. Det här är en I/O- och processorintensiv åtgärd.

**Inläsning:** Förmågan att mata in data i tjänsten. Inläsningar utförs bäst med PolyBase från Azure Storage Blob eller Azure Data Lake. Det här måttet är avsett att framhäva nätverks- och processoraspekter i tjänsten.

**CTAS (Create Table As Select):** CTAS mäter möjligheten att kopiera en tabell. Den här processen läser data från lagring, distribuerar dessa data till enhetens noder och skriver dem till lagringen igen. Det här är en processor-, I/O- och nätverksintensiv åtgärd.

## <a name="built-on-sql-server"></a>Bygger på SQL Server
SQL Data Warehouse baseras på SQL Servers relationsdatabasmotor och innehåller flera av de funktioner som du förväntar dig från ett informationslager i företagsklass. Om du redan har erfarenhet av T-SQL är det lätt att överföra din kunskap till SQL Data Warehouse. Oavsett om du har avancerade kunskaper eller bara precis kommit igång, så kommer exemplen i dokumentationen att hjälpa dig på traven. Generellt sett så kan du tänka på sättet som vi byggt de språkliga elementen i SQL Data Warehouse på följande vis:

* SQL Data Warehouse använder T-SQL-syntaxen för många åtgärder. Det finns också stöd för en omfattande uppsättning traditionella SQL-konstruktioner som lagrade procedurer, användardefinierade funktioner, tabellpartitionering, index och sortering.
* SQL Data Warehouse innehåller även ett antal nyare SQL Server-funktioner som grupperade **columnstore**-index, PolyBase-integrering och datagranskning (med hotbedömning).
* Vissa T-SQL-språkelement som är mindre vanliga för arbetsbelastningar i informationslager, eller som är nyare för SQL Server, kanske inte är tillgängliga. Mer information finns i [migreringsdokumentationen][Migration documentation].

Med Transact-SQL och funktionslikheterna mellan SQL Server, SQL Data Warehouse, SQL Database och Analytics Platform System, kan du utveckla en lösning som passar dina databehov. Du kan själv välja var du vill ha dina data, baserat på prestanda-, säkerhets- och skalningskrav och kan sedan överföra dina data vid behov mellan olika system.

## <a name="data-protection"></a>Dataskydd
SQL Data Warehouse lagrar alla data i lokalt redundant Azure Premium-lagring. Flera synkrona kopior av data bevaras i det lokala datacentret för att garantera transparent dataskydd vid lokaliserade fel. Dessutom säkerhetskopierar SQL Data Warehouse automatiskt dina aktiva (inte pausade) databaser med jämna mellanrum med hjälp av Azure Storage-ögonblicksbilder. Mer information om hur du säkerhetskopierar och återställer arbete finns i [Översikt över säkerhetskopiering och återställning][Backup and restore overview].

## <a name="integrated-with-microsoft-tools"></a>Integrerat med Microsoft-verktyg
SQL Data Warehouse integrerar även många av verktygen som SQL Server-användare är bekanta med. Dessa verktyg innefattar:

**Traditionella SQL Server-verktyg:** SQL Data Warehouse är helt integrerat med SQL Server Analysis Services, Integration Services och Reporting Services.

**Molnbaserade verktyg:** SQL Data Warehouse kan integreras tillsammans med ett antal tjänster i Azure, inklusive Data Factory, Stream Analytics, Machine Learning och Power BI. En mer komplett lista finns i [Översikt över integrerade verktyg][Integrated tools overview].

**Verktyg från tredje part:** Många leverantörer av tredjepartsverktyg har certifierad integrering av sina verktyg med SQL Data Warehouse. En fullständig lista finns i [SQL Data Warehouse-samarbetspartner][SQL Data Warehouse solution partners].

## <a name="hybrid-data-sources-scenarios"></a>Scenarier för hybriddatakällor
Med PolyBase kan du utnyttja data från olika källor med hjälp av välbekanta T-SQL-kommandon. Med PolyBase kan du skicka frågor mot icke-relationella data som finns i Azure Blob Storage precis som om det var en vanlig tabell. Använd Polybase för att fråga icke-relationella data, eller importera dina icke-relationella data till SQL Data Warehouse.

* PolyBase använder sig av externa tabeller för att få åtkomst till icke-relationella data. Tabelldefinitionerna lagras i SQL Data Warehouse och du kan komma åt dem med hjälp av SQL och verktyg på samma sätt som du kommer åt normala relationsdata.
* Polybase är integreringsneutralt. Det visar samma funktioner och egenskaper för alla källor som det stöder. Data läses av Polybase kan vara i en rad olika format, inklusive avgränsade eller ORC-filer.
* PolyBase kan användas för att få åtkomst till Blob Storage som också används som lagringsplats för ett HDInsight-kluster. På så sätt kan du komma åt samma data med verktyg för både relationella och icke-relationella data.

## <a name="sla"></a>SLA
SQL Data Warehouse erbjuder ett servicenivåavtal (SLA) på produktnivå som en del av serviceavtalet för Microsoft Online Services. Mer information finns på sidan om [SLA för SQL Data Warehouse][SLA for SQL Data Warehouse]. SLA-information om övriga produkter finns på Azure-sidan med [serviceavtal]. Du kan även hämta dem på sidan med [volymlicensiering][Volume Licensing]. 

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse kan du gå vidare och se hur du snabbt [skapar ett SQL Data Warehouse][create a SQL Data Warehouse] och [läsa in exempeldata][load sample data]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Skapa ett supportärende]
* [MSDN-forum]
* [Stack Overflow-forum]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Skapa ett supportärende]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Kundernas framgångsberättelser]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[serviceavtal]: https://azure.microsoft.com/en-us/support/legal/sla/

