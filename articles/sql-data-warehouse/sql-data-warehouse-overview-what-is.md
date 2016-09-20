<properties
   pageTitle="Vad är Azure SQL Data Warehouse? | Microsoft Azure"
   description="En distribuerad databas i företagsklass som kan bearbeta petabytevolymer med relationella och icke-relationella data. Den är branschens första informationslager i molnet som kan växa, krympa och pausa på sekunden."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/23/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# Vad är Azure SQL Data Warehouse?

Azure SQL Data Warehouse är en molnbaserad skalbar databas som kan bearbeta massiva mängder data, relationella såväl som icke-relationella. Det bygger på vår arkitektur med massivt parallell bearbetning (MPP). SQL Data Warehouse kan hantera ditt företags arbetsbelastningar.

SQL Data Warehouse:

- Kombinerar SQL Server-relationsdatabasen med skalbarhetsfunktionerna i Azure-molnet. Du kan öka, minska, pausa eller återuppta databearbetning sekundsnabbt. Du kan spara kostnader genom att skala ut processorkapaciteten vid behov och dra ner på användningen under tider med låg belastning.
- Utnyttjar Azure-plattformen. Den är enkel att distribuera, underhålls sömlöst och är helt feltolerant tack vare automatiska säkerhetskopieringar.
- Kompletterar SQL Server-ekosystemet. Du kan utveckla med välbekanta SQL Server Transact-SQL (T-SQL) och verktyg.

Den här artikeln beskriver de viktigaste funktionerna i SQL Data Warehouse.

## MPP-arkitektur (Massively Parallel Processing)

SQL Data Warehouse är ett distribuerat MPP-databassystem. Genom att dela upp data- och bearbetningsfunktioner på flera noder kan SQL Data Warehouse erbjuda fantastisk skalbarhet – långt över den i ett enskilt system.  I bakgrunden sprider SQL Data Warehouse dina data mellan många SN-baserade (shared-nothing) lagrings- och bearbetningsenheter. Data lagras i lokalt redundant Premium-lagring och länkas till beräkningsnoder för frågekörning. Med den här arkitekturen bygger SQL Data Warehouse på en D&C-modell (”divide and conquer”) för belastningar som körs och komplexa frågor. Begäranden tas emot av kontrollnoden, optimeras och skickas sedan till beräkningsnoder som utför arbetet parallellt.

Genom att kombinera MPP-arkitekturen och Azure-lagringsfunktioner kan SQL Data Warehouse:

- Öka eller minska lagring oberoende av beräkning.
- Öka eller minska beräkning utan att flytta data.
- Pausa beräkningskapacitet och bibehålla alla data.
- Återuppta beräkningskapaciteten på ett ögonblick.

Följande diagram illustrerar arkitekturen i detalj.

![SQL Data Warehouse-arkitektur][1]


**Kontrollnod:** Kontrollnoden hanterar och optimerar frågor. Det är den som är klientdelen som interagerar med alla program och anslutningar. Control-noden i SQL Data Warehouse drivs av SQL Database och det ser ut och känns på samma sätt att ansluta till den. Under ytan samordnar Control-noden all dataflytt och alla beräkningar som krävs för att köra parallella frågor på dina distribuerade data. När du skickar en T-SQL-fråga till SQL Data Warehouse omvandlar Control-noden frågan till separata frågor som körs parallellt på varje Compute-nod.

**Beräkningsnoder:** Beräkningsnoderna utgör kraften bakom SQL Data Warehouse. De är SQL-databaser som lagrar dina data och bearbetar din fråga. När du lägger till data distribuerar SQL Data Warehouse raderna till beräkningsnoderna. Beräkningsnoderna ansvarar också för att köra parallella frågor mot dina data. Efter bearbetning, skickar de resultaten tillbaka till Control-noden. För att slutföra frågan, aggregerar Control-noden resultaten och returnerar slutresultatet.

**Lagring:** Dina data lagras i Azure Blob Storage. När Compute-noder interagerar med dina data, skriver de och läser direkt till och från blobblagring. Eftersom Azure-lagring expanderar helt transparent och utan begränsningar, kan SQL Data Warehouse också göra det. Eftersom beräkning och lagring sker oberoende av varandra, kan SQL Data Warehouse automatiskt skala lagring separat från beräkning och vice versa. Azure Blob Storage är också helt feltolerant och effektiviserar säkerhetskopierings- och återställningsprocessen.

**DMS (Data Movement Service):** DMS-tjänsten flyttar data mellan noderna. DMS ger Compute-noderna åtkomst till de data de behöver för kopplingar och aggregeringar. DMS är inte en Azure-tjänst. Det är en Windows-tjänst som körs samtidigt som SQL Database på alla noderna. Eftersom DMS körs i bakgrunden, kommer du inte direkt att interagera med det. När du tittar på frågeplaner kommer du dock att se att de innehåller vissa DMS-åtgärder eftersom det är nödvändigt att flytta data för att köra varje fråga parallellt.


## Optimerad för arbetsbelastningar i informationslager

MPP-modellen har ett antal prestandaoptimeringar som är särskilt utformade för hantering av informationslager, t.ex.:

- En distribuerad frågeoptimerare och en uppsättning komplex statistik för alla data. Med information om datastorlek och distribution kan tjänsten optimera frågor genom att uppskatta kostnaden för specifika distribuerade frågeåtgärder.

- Avancerade algoritmer och tekniker som integrerats i processen med dataflytt för att effektivt flytta data mellan bearbetningsresurser efter behov för att genomföra frågan. Dessa dataflyttningsåtgärder är inbyggda och alla optimeringar av Data Movement Service sker automatiskt.

- Grupperade **columnstore**-index (kolumnlagringsindex) som standard. Genom att använda kolumnbaserad lagring får SQL Data Warehouse ut upp till i genomsnitt 5 gånger så hög komprimering jämfört med traditionell, radbaserad lagring och upp till minst 10 gånger så höga frågeprestanda. Analytics-frågor som behöver skanna ett stort antal rader fungerar bra på kolumnlagringsindex.


## Förutsägbar och skalbar prestanda

SQL Data Warehouse håller isär lagring och beräkning, vilket gör att de kan skalas oberoende av varandra. SQL Data Warehouse kan enkelt skalas så att du snabbt kan lägga till fler beräkningsresurser. Detta kompletteras av användningen av Azure Blob Storage. Blobbar tillhandahåller inte bara stabil och replikerad lagring, men även en infrastruktur för enkel utvidgning till låg kostnad. Den här kombinationen av skalbar molnlagring och Azure-beräkning gör att du kan betala för frågeprestanda och lagring när det behövs med SQL Data Warehouse. Du kan enkelt ändra beräkningsmängden genom att bara dra ett skjutreglage på Azure-portalen åt höger eller vänster, men du kan också schemalägga ändringar med hjälp av T-SQL och PowerShell.

Förutom att kunna styra beräkningsmängden oberoende av lagringen kan du helt pausa informationslagret med SQL Data Warehouse, vilket innebär att du inte betalar för beräkningar när det inte behövs. Med din lagring på plats, och beräkningar som skickas till huvudpoolen i Azure, kan du spara pengar. Vid behov, kan du återuppta beräkningen och ha dina data och din beräkningskraft tillgänglig för din arbetsbelastning.

## Informationslagerenheter

Allokeringen av resurser till SQL Data Warehouse mäts i informationslagerenheter (DWU, Data Warehouse Unit). DWU är ett mått på underliggande resurser, t.ex. CPU, minne och IOPS, som allokeras till SQL Data Warehouse. Om antalet DWU-enheter ökar, ökar resurserna och prestanda. Mer specifikt ser DWU-enheterna till att:

- Du enkelt kan skala informationslagret, utan att behöva bry dig om underliggande maskinvara eller programvara.

- Du kan förutsäga prestandaförbättringarna för en DWU-nivå innan du ändrar storleken på informationslagret.

- Den underliggande maskin- och programvaran i din instans kan ändras eller flyttas utan att arbetsbelastningens prestanda påverkas.

- Microsoft kan göra justeringar i den underliggande arkitekturen för tjänsten utan att arbetsbelastningens prestanda påverkas.

- Microsoft kan snabbt förbättra prestanda i SQL Data Warehouse på ett sätt som är skalbart och som påverkar systemet jämnt.

DWU-enheter ger ett mått på tre exakta mätvärden med stark korrelation till informationslagrets arbetsbelastningsprestanda. Målet är att följande viktiga arbetsbelastningsmått skalas linjärt med de DWU-enheter som du har valt för ditt informationslager.

**Genomsökning/aggregering:** Det här arbetsbelastningsmåttet tar en standard informationslagerfråga som genomsöker ett stort antal rader och sedan genomför en komplex aggregering. Det här är en I/O- och processorintensiv åtgärd.

**Inläsning:** Det här mätvärdet mäter förmågan att mata in data i tjänsten. Inläsningarna kompletteras med en PolyBase-inläsning av en representativ datauppsättning från Azure Blob Storage. Det här måttet är avsett att framhäva nätverks- och processoraspekter i tjänsten.

**CTAS (Create Table As Select):** CTAS mäter möjligheten att kopiera en tabell. Den här processen läser data från lagring, distribuerar dessa data till enhetens noder och skriver dem till lagringen igen. Det här är en processor-, I/O- och nätverksintensiv åtgärd.

## Pausa och skala på begäran

När du behöver snabbare resultat, ökar du dina DWU:er och betalar för bättre prestanda. När du behöver mindre datorkraft, minskar du dina DWU:er och betalar bara för det du behöver. Du bör överväga att ändra dina DWU-enheter i följande scenarierna:

- När du inte behöver köra frågor, kanske på kvällar eller helger, kan du inaktivera dina frågor. Pausa sedan dina beräkningsresurser för att undvika att betala för DWU-enheter när du inte behöver dem.

- När systemet har låg belastning kan du överväga att minska DWU till en mindre storlek. Du kan fortfarande komma åt data, men till en mycket lägre kostnad.

- När du utför en tung datainläsnings- eller omvandlingsåtgärd kanske du vill skala upp så att dina data blir tillgängliga snabbare.

För att ta reda på ditt optimala DWU-värde kan du testa att skala upp och ned och köra några frågor när du har läst in dina data. Eftersom skalningen är snabb kan du prova med ett antal olika prestandanivåer på en timme eller mindre.  Kom ihåg att SQL Data Warehouse är utformat för att bearbeta stora mängder data och för att verkligen på en uppfattning om dess skalningsmöjligheter, särskilt på den skalningsnivå som vi erbjuder, bör du använda en stor datamängd på runt 1 TB eller mer.


## Bygger på SQL Server

SQL Data Warehouse baseras på SQL Servers relationsdatabasmotor och innehåller flera av de funktioner som du förväntar dig från ett informationslager i företagsklass. Om du redan har erfarenhet av T-SQL är det lätt att överföra din kunskap till SQL Data Warehouse. Oavsett om du har avancerade kunskaper eller bara precis kommit igång, så kommer exemplen i dokumentationen att hjälpa dig på traven. Generellt sett så kan du tänka på sättet som vi byggt de språkliga elementen i SQL Data Warehouse på följande vis:

- SQL Data Warehouse använder T-SQL-syntaxen för många åtgärder. Det finns också stöd för en omfattande uppsättning traditionella SQL-konstruktioner som lagrade procedurer, användardefinierade funktioner, tabellpartitionering, index och sortering.

- SQL Data Warehouse innehåller även ett antal nyare SQL Server-funktioner som grupperade **columnstore**-index, PolyBase-integrering och datagranskning (med hotbedömning).

- Vissa T-SQL-språkelement som är mindre vanliga för arbetsbelastningar i informationslager, eller som är nyare för SQL Server, kanske inte är tillgängliga. Mer information finns i [migreringsdokumentationen][].

Med Transact-SQL och funktionslikheterna mellan SQL Server, SQL Data Warehouse, SQL Database och Analytics Platform System, kan du utveckla en lösning som passar dina databehov. Du kan själv välja var du vill ha dina data, baserat på prestanda-, säkerhets- och skalningskrav och kan sedan överföra dina data vid behov mellan olika system.

## Dataskydd

SQL Data Warehouse lagrar alla data i lokalt redundant Azure Premium-lagring. Flera synkrona kopior av data bevaras i det lokala datacentret för att garantera transparent dataskydd vid lokaliserade fel. Dessutom säkerhetskopierar SQL Data Warehouse automatiskt dina aktiva (inte pausade) databaser med jämna mellanrum med hjälp av Azure Storage-ögonblicksbilder. Mer information om hur du säkerhetskopierar och återställer arbete finns i [Översikt över säkerhetskopiering och återställning][].

## Integrerat med Microsoft-verktyg

SQL Data Warehouse integrerar även många av verktygen som SQL Server-användare är bekanta med. Exempel på dessa är:

**Traditionella SQL Server-verktyg:** SQL Data Warehouse är helt integrerat med SQL Server Analysis Services, Integration Services och Reporting Services.

**Molnbaserade verktyg:** SQL Data Warehouse kan användas tillsammans med ett antal nya verktyg i Azure, inklusive Data Factory, Stream Analytics, Machine Learning och Power BI. En mer komplett lista finns i [Översikt över integrerade verktyg][].

**Verktyg från tredje part:** Ett stort antal leverantörer av tredjepartsverktyg har certifierad integrering av sina verktyg med SQL Data Warehouse. En fullständig lista finns i [SQL Data Warehouse-samarbetspartner][].

## Scenarier för hybriddatakällor

SQL Data Warehouse i kombination med PolyBase ger användare fantastiska möjligheter att flytta data mellan sina ekosystem, vilket öppnar upp för konfigurationer av avancerade hybridscenarier med icke-relationella och lokala datakällor.

Med PolyBase kan du utnyttja data från olika källor med hjälp av välbekanta T-SQL-kommandon. Med PolyBase kan du skicka frågor mot icke-relationella data som finns i Azure Blob Storage precis som om det var en vanlig tabell. Använd Polybase för att fråga icke-relationella data, eller importera dina icke-relationella data till SQL Data Warehouse.

- PolyBase använder sig av externa tabeller för att få åtkomst till icke-relationella data. Tabelldefinitionerna lagras i SQL Data Warehouse och du kan komma åt dem med hjälp av SQL och verktyg på samma sätt som du kommer åt normala relationsdata.

- Polybase är integreringsneutralt. Det visar samma funktioner och egenskaper för alla källor som det stöder. Data läses av Polybase kan vara i en rad olika format, inklusive avgränsade eller ORC-filer.

- PolyBase kan användas för att få åtkomst till blobblagring som också används som lagringsplats för ett HD Insight-kluster. På så sätt kan du komma åt samma data med verktyg för både relationella och icke-relationella data.

## Nästa steg

Nu när du vet lite om SQL Data Warehouse kan du gå vidare och se hur du snabbt [skapar ett SQL Data Warehouse][] och [läsa in exempeldata][]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][] vara till hjälp eftersom du kan stöta på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

- [Bloggar]
- [Funktionsbegäranden]
- [Videoklipp]
- [CAT-teambloggar]
- [Skapa ett supportärende]
- [MSDN-forum]
- [Stack Overflow-forum]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Skapa ett supportärende]: sql-data-warehouse-get-started-create-support-ticket.md
[läsa in exempeldata]: sql-data-warehouse-load-sample-databases.md
[skapar ett SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[migreringsdokumentationen]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse-samarbetspartner]: sql-data-warehouse-partner-business-intelligence.md
[Översikt över integrerade verktyg]: sql-data-warehouse-overview-integrate.md
[Översikt över säkerhetskopiering och återställning]: sql-data-warehouse-restore-database-overview.md
[Azure-ordlistan]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT-teambloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse



<!--HONumber=sep16_HO1-->


