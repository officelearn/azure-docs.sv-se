---
title: Azure SQL Database minnesinterna tekniker | Microsoft Docs
description: Azure SQL Database minnesinterna tekniker förbättra prestanda för transaktionell och analytiska arbetsbelastningar.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 235d6174153e32b40885811350d967af5b98ecc4
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478373"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimera prestanda med hjälp av minnesinterna tekniker i SQL-databas

Minnesinterna tekniker i Azure SQL Database kan du förbättra programmets prestanda och minska kostnaden för din databas. Du kan åstadkomma prestandaförbättringar med olika arbetsbelastningar med hjälp av minnesinterna tekniker i Azure SQL Database:

- **Transaktionell** (online transaktionsbearbetning (OLTP)) där de flesta av begäranden läsa eller uppdatera mindre uppsättning data (till exempel CRUD-åtgärder).
- **Analytiska** (online analytical processing (OLAP)) där de flesta av frågorna som har komplexa beräkningar för rapportering teständamål, med ett visst antal frågor som kan läsa in och Lägg till data i de befintliga tabellerna (så kallade massinläsning) eller ta bort den data från tabeller. 
- **Blandade** (hybrid transaktion för analytisk bearbetning (HTAP)) där både OLTP och OLAP-frågor körs på samma uppsättning data.

Minnesinterna tekniker kan förbättra prestandan för dessa arbetsbelastningar genom att lagra de data som ska bearbetas i minnet, med hjälp av inbyggda kompilering av frågorna eller avancerad bearbetning av sådana som batch-bearbetning och SIMD instruktioner som är tillgängliga på den underliggande maskinvara.

Azure SQL Database har följande minnesinterna tekniker:
- *[In-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)*  ökar antalet transaktioner per sekund och minskar svarstiden för transaktionsbearbetning. Scenarier som har nytta av In-Memory OLTP är: högt dataflöde transaktionsbearbetning, till exempel handel och spel, datainmatning från händelser eller IoT-enheter, cachelagring, datainläsning, och tillfällig tabell och tabellen variabeln scenarier.
- *Klustrade kolumnlagringsindex* och minska fotavtryck för lagring (upp till 10 gånger) och förbättra prestanda för rapporterings- och analysfrågor. Du kan använda med faktatabeller data marts och passar mer data i databasen som du kan förbättra prestanda. Du kan också använda den med historiska data i databasen att arkivera och kunna skicka frågor upp till 10 gånger mer data.
- *Icke-grupperat columnstore-index* för HTAP hjälper dig att få insikter i realtid om din verksamhet genom att fråga den använda databasen direkt, utan att behöva köra en dyr extrahera, transformera och läsa in (ETL) bearbeta och vänta tills den informationslager fyllas i. Icke-grupperat columnstore-index Tillåt snabb körning av analysfrågor på OLTP-databasen samtidigt som det minskar påverkan på arbetsbelastningen.
- *Minnesoptimerade klustrade kolumnlagringsindex* för HTAP kan du utföra snabb transaktionsbearbetning och *samtidigt* köra analysfrågor mycket snabbt på samma data.

Både columnstore-index och In-Memory OLTP har varit en del av SQL Server-produkt sedan 2012 och 2014 respektive. Azure SQL Database och SQL Server kan du dela samma implementeringen av minnesinterna tekniker. Framöver kommer släpps nya funktioner för dessa tekniker i Azure SQL Database först innan de blir tillgängliga i SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Fördelarna med minnesintern teknik

På grund av effektivare frågan och bearbetning av transaktioner hjälpa minnesinterna tekniker även dig att minska kostnaderna. Vanligtvis måste inte du uppgradera prisnivån för databasen för att uppnå prestandavinster. I vissa fall går du kanske även kan minska prisnivån fortfarande se prestandaförbättringar med minnesinterna tekniker.

Här följer två exempel på hur In-Memory OLTP har hjälpt till att avsevärt förbättra prestanda:

- Med hjälp av In-Memory OLTP [kvorum affärslösningar kunde dubbelklicka arbetsbelastningen samtidigt förbättra dtu: er med 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - DTU innebär *database-transaktionsenhet*, och innehåller ett mått på resursförbrukning.
- Följande videoklipp visar viktig förbättringar i resursförbrukning med ett exempel på arbetsbelastning: [Minnesintern OLTP i Azure SQL Database-videon](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Mer information finns i blogginlägget: [Minnesintern OLTP i Azure SQL Database-blogginlägget](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Minnesinterna tekniker är tillgängliga i Premium och affärskritisk nivå Azure SQL-databaser och elastiska Premium-pooler.

Följande videoklipp beskriver potentiella prestandafördelar med minnesinterna tekniker i Azure SQL Database. Kom ihåg att prestandaökning som du ser alltid är beroende av många faktorer, bland annat typen av arbetsbelastning och data, åtkomstmönster för av databasen, och så vidare.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Den här artikeln beskrivs olika aspekter av In-Memory OLTP och columnstore-index som är specifika för Azure SQL Database och dessutom ingår exempel:

- Effekten av dessa tekniker visas på storleksgränser för storage och data.
- Du lär dig att hantera flödet av databaser som använder dessa tekniker mellan olika prisnivåer.
- Du ser två exempel som illustrerar användningen av In-Memory OLTP, samt columnstore-index i Azure SQL Database.

Mer information finns i:

- [InMemory-OLTP-översikt och Användningsscenarier](https://msdn.microsoft.com/library/mt774593.aspx) (innehåller referenser till kundfallstudier och information för att komma igång)
- [Dokumentation för Minnesintern OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Guide för Kolumnlagringsindex](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybrid transaktionella/analytisk bearbetning (HTAP), även kallat [operativa analys i realtid](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>Minnesintern OLTP

Minnesintern OLTP-teknik ger mycket snabba åtgärder för dataåtkomst genom att behålla alla data i minnet. Den använder också specialiserade index, interna kompileringen av frågor och oreglerad dataåtkomst kan förbättra prestanda vid OLTP-arbetsbelastning. Det finns två sätt att organisera dina In-Memory OLTP-data:

- **Minnesoptimerade rowstore** format där varje rad är ett minnesutrymme-objekt. Det här är ett klassiska InMemory-OLTP-format som är optimerat för OLTP-arbetsbelastningar med höga prestanda. Det finns två typer av minnesoptimerade tabeller som kan användas i minnesoptimerade rowstore-format:
  - *Hållbar tabeller* (SCHEMA_AND_DATA) där de rader som placeras i minnet bevaras när servern startas om. Den här typen av tabeller fungerar som en traditionell rowstore-tabell med de ytterligare fördelarna med InMemory-optimeringar.
  - *Icke-beständiga tabeller* (SCEMA_ONLY) där raderna är inte bevaras efter omstart. Den här typen av tabellen har utformats för tillfälliga data (till exempel byte av temporära tabeller), eller tabeller där du behöver för att snabbt läsa in data innan du flyttar den till vissa beständiga tabell (så kallade mellanlagringstabellerna).
- **Minnesoptimerade columnstore** format där data ordnas i ett kolumnformat. Den här strukturen är utformad för HTAP scenarier där du behöver köra analytiska frågor på samma datastruktur där din OLTP-arbetsbelastning körs.

> [!Note]
> In-Memory OLTP-tekniken utformats för datastrukturer som helt kan finnas i minnet. Eftersom det går inte att den avlastas minnesinterna data på disk, se till att använder du databasen som har tillräckligt med minne. Se [Data och gränsen för In-Memory OLTP](#data-size-and-storage-cap-for-in-memory-oltp) för mer information.

En snabb genomgång på In-Memory OLTP: [Snabbstart 1: InMemory-OLTP-tekniker för snabbare prestanda med T-SQL](https://msdn.microsoft.com/library/mt694156.aspx) (en annan artikel som hjälper dig att komma igång)

Djupgående videor om tekniker:

- [In-Memory OLTP i Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (som innehåller en demonstration av prestandafördelarna och steg för att återskapa de här resultaten själv)
- [In-Memory OLTP-videor: Vad det är och när/hur ska använda det.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Det finns ett programmässiga sätt att förstå om en viss databas har stöd för In-Memory OLTP. Du kan köra följande Transact-SQL-fråga:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Om frågan returnerar **1**, In-Memory OLTP stöds i den här databasen. Följande frågor identifiera alla objekt som måste tas bort innan en databas kan nedgraderas till Standard/enkel:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Data och gränsen för In-Memory OLTP

In-Memory OLTP innehåller minnesoptimerade tabeller som används för lagring av användardata. Dessa tabeller krävs för att få plats i minnet. Eftersom du hantera minne direkt i SQL Database-tjänsten har vi konceptet med en kvot för användardata. Den här idén kallas *InMemory-OLTP-lagring*.

Varje databas som stöds enda prisnivå och varje elastisk pool prisnivån innehåller en viss mängd InMemory-OLTP-lagring. Se [DTU-baserade resursbegränsningar - enkel databas](sql-database-dtu-resource-limits-single-databases.md), [DTU-baserade resursbegränsningar - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md),[vCore-baserade resursbegränsningar - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Följande objekt tillgodoräknas din begränsning för In-Memory OLTP-lagring:

- Aktiv användare datarader i minnesoptimerade tabeller och tabellvariabler. Observera att den gamla raden versioner inte räknas mot gränsen.
- Index för minnesoptimerade tabeller.
- Arbetet med ALTER TABLE-åtgärderna.

Om du har nått gränsen får du en out kvotfel och du inte längre kunna infoga eller uppdatera data. Ta bort data för att lösa det här felet, eller öka prisnivån för databasen eller pool.

Mer information om övervaka lagringsanvändningen för In-Memory OLTP och konfigurera aviseringar när du nästan har nått gränsen finns [övervaka Minnesintern lagring](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Om elastiska pooler

Med elastiska pooler delas InMemory-OLTP-lagring mellan alla databaser i poolen. Därför kan användning i en databas potentiellt påverka andra databaser. Två åtgärder för det här är:

- Konfigurera en `Max-eDTU` eller `MaxvCore` för databaser som är lägre än antalet edtu: er eller vCore för poolen som helhet. Den här högsta gränsen caps InMemory-OLTP-lagringsanvändning, i alla databaser i poolen, till den storlek som motsvarar antalet edtu: er.
- Konfigurera en `Min-eDTU` eller `MinvCore` som är större än 0. Med detta minsta garanterar att varje databas i poolen har tillgängliga InMemory-OLTP-lagringsutrymme som motsvarar den konfigurerade `Min-eDTU` eller `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Ändra tjänstnivåerna för databaser som använder Minnesintern OLTP-tekniker

Du kan alltid uppgradera din databas eller en instans till en högre nivå, till exempel från generell användning till affärskritisk (eller Standard till Premium). De tillgängliga funktionerna och resurserna bara öka.

Men nedgradera nivån kan negativt påverka din databas. Påverkan är särskilt tydligt när du Nedgradera från affärskritisk till generell användning (eller Premium till Standard eller Basic) när databasen innehåller In-Memory OLTP-objekt. Minnesoptimerade tabeller är inte tillgängliga när nedgraderingen (även om de fortfarande är synlig). Samma gäller när du minskar prisnivån för en elastisk pool, eller flytta en databas med minnesinterna tekniker i en Standard eller Basic-databaspool.

> [!Important]
> In-Memory OLTP stöds inte i nivån generell användning, Standard eller Basic. Därför kan det inte går att flytta en databas som har några In-Memory OLTP-objekt till nivån Standard eller Basic.

Ta bort alla minnesoptimerade tabeller och tabelltyper, samt alla internt kompilerade T-SQL-moduler innan du börjar nedgradera databasen till Standard/enkel. 

*Skala ned resurserna i nivån affärskritisk*: Data i minnesoptimerade tabeller måste rymmas inom den minnesinterna OLTP-lagring som är associerad med nivån av databasen eller hanterad instans, eller om den är tillgänglig i den elastiska poolen. Om du försöker skala ned nivån eller flytta databasen till en pool som inte har tillräckligt med tillgängliga InMemory-OLTP-lagring, åtgärden misslyckas.

## <a name="in-memory-columnstore"></a>Minnesinterna kolumnlagring

Minnesinterna columnstore-tekniken så att du kan lagra och fråga efter en stor mängd data i tabellerna. Columnstore-tekniken använder kolumnbaserad lagring dataformat och batch frågebearbetning för att uppnå få upp till 10 gånger frågeprestanda de OLAP-arbetsbelastningar med traditionell radorienterad lagring. Du kan också få får upp till 10 gånger datakomprimering över storleken på okomprimerade data.
Det finns två typer av columnstore-modeller som du kan använda för att ordna data:

- **Grupperade** där alla data i tabellen är ordnade i ett kolumnformat. I den här modellen placeras alla rader i tabellen i kolumnformat som mycket data komprimeras och kan du köra analytiska frågor och rapporter i tabellen. Beroende på typen av dina data, storleken på dina data vara sämre 10 x-100 x. Grupperade columnstore-modellen kan också snabbt inmatning av stora mängder data (massinläsning) sedan stora batchar av data som är större än 100K rader komprimeras innan de lagras på disk. Den här modellen är ett bra val för de klassiska data warehouse-scenarierna. 
- **Icke-klustrade columnstore** där data lagras i traditionella rowstore tabellen och det finns ett index i kolumnlagringsformatet som används för analytiska frågor. Den här modellen kan Hybrid transaktion analytisk bearbetning (HTAP): möjligheten att köra högpresterande realtidsanalys på transaktionsbelastning. OLTP-frågor körs på rowstore-tabell som är optimerad för att komma åt en liten uppsättning rader, medan OLAP-frågor körs på columnstore-index som är bättre för genomsökningar och analyser. Azure SQL Database-frågeoptimerare väljer dynamiskt rowstore eller columnstore-format som baseras på frågan. Icke-grupperade columnstore-index minska inte storleken på data eftersom ursprungliga datauppsättningen sparas i den ursprungliga rowstore-tabellen utan ändringar. Storleken på ytterligare columnstore-index bör dock i storleksordning som är mindre än den motsvarande B-trädindex.

> [!Note]
> Minnesinterna columnstore-tekniken behåller endast data som krävs för bearbetning i minnet, medan lagringen av data som inte får plats i minnet på disken. Mängden data i minnesinterna columnstore strukturer kan därför överskrider mängden tillgängligt minne. 

Djupgående video om tekniken:

- [Columnstore-Index: Minnesintern analys videor från Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Datastorlek och lagring för columnstore-index

Columnstore-index behöver inte ryms i minnet. Därför endast fästpunkten på storleken på index som är den högsta totala databasstorleken, som dokumenteras i den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) artiklar.

När du använder grupperade columnstore-index används kolumnbaserad komprimering för lagringen som bastabellen. Den här komprimeringen kan avsevärt minska storleken för lagring av användardata, vilket innebär att du får plats mer data i databasen. Och komprimeringen kan förlängas med [kolumnbaserad arkivering komprimering](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Mängden komprimering som du kan uppnå beror på vilken typ av data, men 10 gånger komprimering är inte ovanligt.

Om du har en databas med en maximal storlek på 1 terabyte (TB) och du kan få 10 gånger komprimering med hjälp av columnstore-index, kan du till exempel passa totalt 10 TB användardata i databasen.

När du använder icke-grupperat columnstore-index, lagras fortfarande bastabellen i traditionella rowstore-format. Lagringsbesparingar är därför inte lika stor som med grupperade columnstore-index. Om du ersätter ett antal traditionella icke-grupperade index med en enda columnstore-index, kan du dock fortfarande se en övergripande besparingar i lagringsutrymme storleken för tabellen.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Ändra tjänstnivåerna för databaser som innehåller Columnstore-index

*Lägre enkel databas till Basic eller Standard* kanske inte möjligt om mål-nivån är lägre än S3. Columnstore-index stöds endast på Business kritisk/Premium-prisnivån på Standard-nivån S3 och ovan och inte på Basic-nivån. När du börjar nedgradera din databas till en nivå som inte stöds eller nivå otillgänglig columnstore-index. Systemet behålls columnstore-index, men den använder aldrig indexet. Om du senare uppgradera till en nivå som stöds eller nivå, är columnstore-index klar att användas igen.

Om du har en **klustrade** columnstore-indexet, hela tabellen blir otillgänglig när nedgraderingen. Därför rekommenderar vi att du ta bort alla *klustrade* columnstore indexerar innan du börjar nedgradera din databas till en nivå som inte stöds eller nivå.

> [!Note]
> Hanterad instans har stöd för ColumnStore-index på alla nivåer.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Nästa steg

- [Snabbstart 1: InMemory-OLTP-tekniker för snabbare prestanda för T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Använda Minnesintern OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)
- [Övervaka Minnesintern OLTP-lagring](sql-database-in-memory-oltp-monitoring.md) för In-Memory OLTP
- [Testa InMemory-funktionerna i Azure SQL Database](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Ytterligare resurser

### <a name="deeper-information"></a>Mer detaljerad information

- [Lär dig hur kvorum fördubblar viktiga databasarbetsbelastningen samtidigt som du minskar DTU med 70% med In-Memory OLTP i SQL-databas](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Minnesintern OLTP i Azure SQL Database-blogginlägget](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Lär dig mer om Minnesintern OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Lär dig mer om columnstore-index](https://msdn.microsoft.com/library/gg492088.aspx)
- [Lär dig mer om operativa analys i realtid](https://msdn.microsoft.com/library/dn817827.aspx)
- Se [vanliga mönster för arbetsbelastningen och överväganden vid migrering](https://msdn.microsoft.com/library/dn673538.aspx) (som beskriver arbetsbelastningmönster där In-Memory OLTP ofta ger betydande prestandavinster)

### <a name="application-design"></a>Programmets design

- [Minnesintern OLTP (minnesoptimering)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Använda Minnesintern OLTP i ett befintligt Azure SQL-program](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Verktyg

- [Azure Portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
