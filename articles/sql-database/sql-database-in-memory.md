---
title: Minnesteknik
description: Azure SQL Database In-Memory-teknik förbättrar avsevärt prestanda för transaktions- och analysarbetsbelastningar.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: de60712451d4c2e8a7d931f7a09352f55be05694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73810267"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimera prestanda med hjälp av minnesminnesteknik i SQL Database

Med minnesteknik i Azure SQL Database kan du förbättra prestanda för ditt program och eventuellt minska kostnaderna för databasen. 

## <a name="when-to-use-in-memory-technologies"></a>När ska in-memory-teknik användas

Genom att använda minnesminnestekniker i Azure SQL Database kan du uppnå prestandaförbättringar med olika arbetsbelastningar:

- **Transaktionell** (onlinetransaktionsbearbetning (OLTP)) där de flesta begäranden läser eller uppdaterar mindre uppsättning data (till exempel CRUD-åtgärder).
- **Analytisk** (online analytisk bearbetning (OLAP)) där de flesta frågor har komplexa beräkningar för rapporteringsändamål, med ett visst antal frågor som läser in och lägger till data i befintliga tabeller (så kallad massinläsning) eller ta bort data från tabellerna. 
- **Blandad** (hybridtransaktion/analytisk bearbetning (HTAP)) där både OLTP- och OLAP-frågor körs på samma uppsättning data.

Minnesteknik kan förbättra prestanda för dessa arbetsbelastningar genom att behålla de data som ska bearbetas i minnet, med hjälp av inbyggd kompilering av frågorna eller avancerad bearbetning, till exempel batchbearbetning och SIMD-instruktioner som är tillgängliga på underliggande hårdvara. 

## <a name="overview"></a>Översikt

Azure SQL Database har följande in-memory-tekniker:
- *[OLTP i minnet](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* ökar antalet transaktioner per sekund och minskar svarstiden för transaktionsbearbetning. Scenarier som drar nytta av IN-Memory OLTP är: transaktionsbearbetning med hög dataflöde, till exempel handel och spel, datainmatning från händelser eller IoT-enheter, cachelagring, datainläsning och tillfälliga tabell- och tabellvariablescenarier.
- *Grupperade columnstore-index* minskar ditt lagringsutrymme (upp till 10 gånger) och förbättrar prestanda för rapporterings- och analysfrågor. Du kan använda den med faktatabeller i dina datamars för att få plats med mer data i databasen och förbättra prestanda. Du kan också använda den med historiska data i din operativa databas för att arkivera och kunna fråga upp till 10 gånger mer data.
- *Icke-grupperade columnstore-index* för HTAP hjälper dig att få insikter i realtid i ditt företag genom att fråga den operativa databasen direkt, utan att behöva köra en dyr utvinning, transformering och inläsning (ETL) process och vänta på att informationslagret ska fyllas i. Icke-grupperade columnstore-index möjliggör snabb körning av analysfrågor i OLTP-databasen, samtidigt som påverkan på den operativa arbetsbelastningen minskar.
- *Minnesoptimerade klustrade columnstore-index* för HTAP gör att du kan utföra snabb transaktionsbearbetning och *samtidigt* köra analysfrågor mycket snabbt på samma data.

Både columnstore index och In-Memory OLTP har varit en del av SQL Server-produkten sedan 2012 respektive 2014. Azure SQL Database och SQL Server delar samma implementering av in-memory-tekniker. Framöver släpps nya funktioner för dessa tekniker först i Azure SQL Database innan de släpps i SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Fördelar med minnesbaserad teknik

På grund av den effektivare fråge- och transaktionsbearbetningen hjälper minnesbaserad teknik dig också att minska kostnaderna. Du behöver vanligtvis inte uppgradera databasens prisnivå för att uppnå prestandavinster. I vissa fall kan du till och med minska prisnivån, samtidigt som du ser prestandaförbättringar med in-memory-teknik.

Här är två exempel på hur IN-Memory OLTP bidragit till att avsevärt förbättra prestanda:

- Genom att använda IN-Memory OLTP [kunde Quorum Business Solutions fördubbla sin arbetsbelastning och samtidigt förbättra DTUs med 70%.](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

  - DTU betyder *databastransaktionsenhet*och innehåller ett mått på resursförbrukning.
- Följande video visar betydande förbättringar i resursförbrukning med en exempelarbetsbelastning: [IN-Memory OLTP i Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Mer information finns i blogginlägget: [In-Memory OLTP i Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Minnestekniker är tillgängliga i Azure SQL-databaser på Premium- och affärskritisk nivå och elastiska premiumpooler.

Följande video förklarar potentiella prestandavinster med minnesbaserad teknik i Azure SQL Database. Kom ihåg att prestandavinsten som du ser alltid beror på många faktorer, inklusive arbetsbelastningens och datans art, åtkomstmönstret för databasen och så vidare.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

I den här artikeln beskrivs aspekter av OLTP-index för in-memory och columnstore som är specifika för Azure SQL Database och även exempel:

- Du ser hur dessa tekniker påverkar lagrings- och datastorleksgränserna.
- Du får se hur du hanterar förflyttning av databaser som använder dessa tekniker mellan de olika prisnivåerna.
- Du ser två exempel som illustrerar användningen av IN-Memory OLTP, samt columnstore index i Azure SQL Database.

Mer information finns i:

- [Oltp-översikt och användningsscenarier för minne](https://msdn.microsoft.com/library/mt774593.aspx) (innehåller referenser till fallstudier och information för att komma igång)
- [Dokumentation för IN-Memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Columnstore Indexer Guide](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybridtransaktions-/analytisk bearbetning (HTAP), även känd som [operativ analys i realtid](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>OLTP i minnet

OLTP-teknik i minnet ger extremt snabba dataåtkomståtgärder genom att hålla alla data i minnet. Den använder också specialiserade index, inbyggd kompilering av frågor och spärrfri dataåtkomst för att förbättra prestanda för OLTP-arbetsbelastningen. Det finns två sätt att ordna OLTP-data i minnet:

- **Minnesoptimerat rowstore-format** där varje rad är ett separat minnesobjekt. Detta är ett klassiskt OLTP-format i minnet som är optimerat för högpresterande OLTP-arbetsbelastningar. Det finns två typer av minnesoptimerade tabeller som kan användas i det minnesoptimerade rowstore-formatet:
  - *Varaktiga tabeller* (SCHEMA_AND_DATA) där raderna som placeras i minnet bevaras efter omstart av servern. Den här typen av tabeller fungerar som en traditionell rowstore-tabell med ytterligare fördelar med optimeringar i minnet.
  - *Icke-varaktiga tabeller* (SCHEMA_ONLY) där raderna inte bevaras efter omstart. Den här typen av tabell är utformad för tillfälliga data (till exempel ersättning av temporära tabeller) eller tabeller där du snabbt måste läsa in data innan du flyttar den till någon beständig tabell (så kallade mellanlagringstabeller).
- **Minnesoptimerat columnstore-format** där data är ordnade i ett kolumnformat. Den här strukturen är utformad för HTAP-scenarier där du måste köra analytiska frågor på samma datastruktur där din OLTP-arbetsbelastning körs.

> [!Note]
> IN-Memory OLTP-tekniken är utformad för de datastrukturer som helt kan finnas i minnet. Eftersom minnesdata inte kan avlastas till disk, kontrollerar du att du använder databas som har tillräckligt med minne. Mer information [finns i Datastorlek och lagringstak för OLTP i minnet.](#data-size-and-storage-cap-for-in-memory-oltp)

En snabb primer på In-Memory OLTP: [Quickstart 1: In-Memory OLTP Technologies for Faster T-SQL Performance](https://msdn.microsoft.com/library/mt694156.aspx) (en annan artikel som hjälper dig att komma igång)

Djupgående videor om tekniken:

- [Oltp i Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (som innehåller en demo av prestandafördelar och steg för att återskapa dessa resultat själv)
- [In-Memory OLTP Videos: Vad det är och när / hur man använder den](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Det finns ett programmatiskt sätt att förstå om en viss databas stöder IN-Memory OLTP. Du kan köra följande Transact-SQL-fråga:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Om frågan returnerar **1**stöds IN-Memory OLTP i den här databasen. Följande frågor identifierar alla objekt som måste tas bort innan en databas kan nedgraderas till Standard/Basic:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Datastorlek och lagringstak för OLTP i minnet

IN-Memory OLTP innehåller minnesoptimerade tabeller som används för att lagra användardata. Dessa tabeller krävs för att få plats i minnet. Eftersom du hanterar minne direkt i SQL Database-tjänsten har vi konceptet en kvot för användardata. Den här idén kallas *IN-Memory OLTP-lagring*.

Varje prisnivå för en databas och varje elastisk poolprisnivå innehåller en viss oltp-lagring i minnet. Se [DTU-baserade resursgränser - en databas,](sql-database-dtu-resource-limits-single-databases.md) [DTU-baserade resursgränser - elastiska pooler,](sql-database-dtu-resource-limits-elastic-pools.md)[vCore-baserade resursgränser - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade resursgränser - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Följande objekt räknas in i ditt OLTP-lagringslock för in-memory:

- Aktiva användardatarader i minnesoptimerade tabeller och tabellvariabler. Observera att gamla radversioner inte räknas mot locket.
- Index på minnesoptimerade tabeller.
- Drift overhead för ALTER TABLE operationer.

Om du träffar taket visas ett fel utanför kvoten och du kan inte längre infoga eller uppdatera data. Om du vill minska det här felet tar du bort data eller ökar prisnivån för databasen eller poolen.

Mer information om hur du övervakar användning av OLTP-lagring i minnet och konfigurerar aviseringar när du nästan träffar locket finns [i Övervaka minneslagring](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Om elastiska pooler

Med elastiska pooler delas OLTP-lagringen i minnet mellan alla databaser i poolen. Därför kan användningen i en databas potentiellt påverka andra databaser. Två mildrande åtgärder för detta är:

- Konfigurera `Max-eDTU` en `MaxvCore` eller för databaser som är lägre än antalet eDTU- eller vCore-enheter för poolen som helhet. Det här maxvärdet begränsar användningen av OLTP-lagring i minnet, i alla databaser i poolen, till den storlek som motsvarar antalet eDTU.This maximum caps the In-Memory OLTP storage utilization, in any database in the pool, to the size that corresponds to the eDTU count.
- Konfigurera `Min-eDTU` en `MinvCore` eller så pass är mer stor än 0. Det här minimivärdet garanterar att varje databas i poolen har den mängd tillgängligt `Min-eDTU` OLTP-lagring i minnet som motsvarar den konfigurerade eller `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Ändra tjänstnivåer för databaser som använder OLTP-tekniker i minnet

Du kan alltid uppgradera databasen eller instansen till en högre nivå, till exempel från allmänt syfte till affärskritiskt (eller Standard till Premium). De tillgängliga funktionerna och resurserna ökar bara.

Men nedgradering av nivån kan påverka databasen negativt. Effekten är särskilt tydlig när du nedgraderar från affärskritisk till allmänt syfte (eller Premium till Standard eller Basic) när databasen innehåller IN-Memory OLTP-objekt. Minnesoptimerade tabeller är inte tillgängliga efter nedgraderingen (även om de förblir synliga). Samma överväganden gäller när du sänker prisnivån för en elastisk pool eller flyttar en databas med minnesbaserad teknik till en elastisk standardpool eller Grundläggande pool.

> [!Important]
> OLTP i minnet stöds inte på nivån Allmänt ändamål, Standard eller Basic. Därför är det inte möjligt att flytta en databas som har några OLTP-objekt i minnet till standard- eller basic-nivån.

Innan du nedgraderar databasen till Standard/Basic tar du bort alla minnesoptimerade tabeller och tabelltyper samt alla inbyggda kompilerade T-SQL-moduler. 

*Skalningsresurser på affärskritisk nivå*: Data i minnesoptimerade tabeller måste passa in i OLTP-lagringen i minnet som är associerad med nivån för databasen eller hanterad instans, eller så är den tillgänglig i den elastiska poolen. Om du försöker skala ned nivån eller flytta databasen till en pool som inte har tillräckligt med oltp-lagring i minnet misslyckas åtgärden.

## <a name="in-memory-columnstore"></a>I minnet columnstore

I minnet columnstore-teknik gör att du kan lagra och fråga en stor mängd data i tabellerna. Columnstore-teknik använder kolumnbaserat datalagringsformat och batchfrågebearbetning för att uppnå upp till 10 gånger frågeprestandan i OLAP-arbetsbelastningar över traditionell radorienterad lagring. Du kan också uppnå vinster upp till 10 gånger datakomprimeringen över den okomprimerade datastorleken.
Det finns två typer av columnstore-modeller som du kan använda för att ordna dina data:

- **Klustrade columnstore** där alla data i tabellen är ordnade i columnar-format. I den här modellen placeras alla rader i tabellen i kolumnformat som komprimerar data mycket och gör att du kan köra snabba analytiska frågor och rapporter i tabellen. Beroende på vilken typ av data du har kan storleken på dina data minskas 10x-100x. Klustrade columnstore-modell möjliggör också snabb inmatning av stora mängder data (massinläsning) eftersom stora grupper med data som är större än 100 000 rader komprimeras innan de lagras på disken. Den här modellen är ett bra val för de klassiska scenarierna för informationslager. 
- **Icke-grupperade columnstore** där data lagras i traditionell rowstore-tabell och det finns ett index i columnstore-formatet som används för analysfrågorna. Den här modellen möjliggör HTAP (Hybrid Transactional-Analytic Processing): möjligheten att köra högpresterande realtidsanalys på en transaktionsarbetsbelastning. OLTP-frågor körs i rowstore-tabellen som är optimerad för åtkomst till en liten uppsättning rader, medan OLAP-frågor körs på columnstore-index som är bättre val för genomsökningar och analyser. Azure SQL Database Query optimizer väljer dynamiskt rowstore eller columnstore format baserat på frågan. Icke-grupperade columnstore-index minskar inte storleken på data eftersom den ursprungliga datauppsättningen sparas i den ursprungliga radstore-tabellen utan några ändringar. Storleken på ytterligare columnstore-index bör dock vara i storleksordning som är mindre än motsvarande B-trädindex.

> [!Note]
> I minnet columnstore-teknik behåller endast de data som behövs för bearbetning i minnet, medan de data som inte får plats i minnet lagras på disk. Därför kan mängden data i columnstore-strukturer i minnet överstiga mängden tillgängligt minne. 

Djupgående video om tekniken:

- [Columnstore Index: In-Memory Analytics Videos från Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Datastorlek och lagring för columnstore-index

Columnstore-index krävs inte för att få plats i minnet. Därför är det enda taket för storleken på indexen den maximala totala databasstorleken, vilket dokumenteras i den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellartiklar.](sql-database-service-tiers-vcore.md)

När du använder klustrade columnstore-index används columnar-komprimering för bastabelllagringen. Den här komprimeringen kan avsevärt minska lagringsavtrycket för dina användardata, vilket innebär att du kan få plats med mer data i databasen. Och komprimeringen kan ökas ytterligare med [columnar arkivkomprimering](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Mängden komprimering som du kan uppnå beror på vilken typ av data, men 10 gånger komprimeringen är inte ovanligt.

Om du till exempel har en databas med en maximal storlek på 1 terabyte (TB) och du uppnår 10 gånger komprimeringen med hjälp av columnstore-index, kan du få plats med totalt 10 TB användardata i databasen.

När du använder icke-grupperade columnstore-index lagras bastabellen fortfarande i det traditionella rowstore-formatet. Lagringsbesparingarna är därför inte lika stora som med klustrade columnstore-index. Men om du ersätter ett antal traditionella icke-grupperade index med ett enda columnstore-index kan du fortfarande se en övergripande besparing i lagringsavtrycket för tabellen.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Ändra tjänstnivåer för databaser som innehåller Columnstore-index

*Det* kanske inte är möjligt att nedgradera en enda databas till Basic eller Standard om målnivån är lägre än S3. Columnstore-index stöds endast på prisnivån Affärskritiskt/Premium och på standardnivån, S3 och högre och inte på basic-nivån. När du nedgraderar databasen till en nivå eller nivå som inte stöds blir ditt columnstore-index inte tillgängligt. Systemet underhåller ditt columnstore-index, men det utnyttjar aldrig indexet. Om du senare uppgraderar tillbaka till en nivå eller nivå som stöds är ditt columnstore-index omedelbart redo att utnyttjas igen.

Om du har ett **grupperat** columnstore-index blir hela tabellen otillgänglig efter nedgraderingen. Därför rekommenderar vi att du släpper alla *klustrade* columnstore-index innan du nedgraderar databasen till en nivå eller nivå som inte stöds.

> [!Note]
> Hanterad instans stöder ColumnStore-index på alla nivåer.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Nästa steg

- [Snabbstart 1: OLTP-tekniker i minnet för snabbare T-SQL-prestanda](https://msdn.microsoft.com/library/mt694156.aspx)
- [Använda IN-Memory OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)
- [Övervaka OLTP-lagring](sql-database-in-memory-oltp-monitoring.md) i minnet för OLTP i minnet
- [Prova minnesfunktioner i Azure SQL Database](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Ytterligare resurser

### <a name="deeper-information"></a>Djupare information

- [Lär dig hur Quorum fördubblar nyckeldatabasens arbetsbelastning samtidigt som du sänker DTU med 70 % med IN-Memory OLTP i SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [OLTP i Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Lär dig mer om OLTP i minnet](https://msdn.microsoft.com/library/dn133186.aspx)
- [Läs mer om columnstore-index](https://msdn.microsoft.com/library/gg492088.aspx)
- [Läs mer om operativ analys i realtid](https://msdn.microsoft.com/library/dn817827.aspx)
- Se [Vanliga arbetsbelastningsmönster och migreringsöverväganden](https://msdn.microsoft.com/library/dn673538.aspx) (som beskriver arbetsbelastningsmönster där OLTP i minnet ofta ger betydande prestandavinster)

### <a name="application-design"></a>Programdesign

- [OLTP (optimering i minnet)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Använda IN-Memory OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Verktyg

- [Azure-portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
