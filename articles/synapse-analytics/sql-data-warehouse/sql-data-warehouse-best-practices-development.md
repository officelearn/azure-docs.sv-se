---
title: Bästa praxis för utveckling
description: Rekommendationer och metodtips som du bör känna till när du utvecklar lösningar för Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9c4f08b143ab4a0d3e780f68f8d5ab823d4eae12
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745360"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Metodtips för utveckling för Synapse SQL-pool

I den här artikeln beskrivs vägledning och metodtips när du utvecklar din SQL-poollösning.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Justera frågeprestanda med nya produktförbättringar

- [Prestandajustering med materialiserade vyer](performance-tuning-materialized-views.md)
- [Prestandajustering med grupperade kolumnlagringsindex](performance-tuning-ordered-cci.md)
- [Prestandajustering med cachelagring av resultatuppsättningar](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Mer information om hur du minskar kostnaderna genom pausning och skalning finns i hantera [beräkningsartikeln.](sql-data-warehouse-manage-compute-overview.md)

## <a name="maintain-statistics"></a>Underhålla statistik

SQL-pool kan konfigureras för att automatiskt identifiera och skapa statistik för kolumner.  De frågeplaner som skapats av optimeraren är bara lika bra som den tillgängliga statistiken.  

Vi rekommenderar att du aktiverar AUTO_CREATE_STATISTICS för dina databaser och håller statistiken uppdaterad dagligen eller efter varje inläsning för att säkerställa att statistik över kolumner som används i dina frågor alltid är uppdaterad.

Om det tar för lång tid att uppdatera all statistik kan du försöka vara mer selektiv om vilka kolumner som kräver frekventa statistikuppdateringar. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag.

> [!TIP]
> Du får mest nytta av att ha uppdaterad statistik om kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Se även [Hantera tabellstatistik](sql-data-warehouse-tables-statistics.md), [SKAPA STATISTIK](sql-data-warehouse-tables-statistics.md)och [UPPDATERA STATISTIK](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller

Tabeller distribueras som standard med resursallokering (Round Robin).  Den här designen gör det enkelt för användare att komma igång med att skapa tabeller utan att behöva bestämma hur deras tabeller ska fördelas.  

Resursallokeringstabeller kan prestera bra för vissa arbetsbelastningar, men i de flesta fall blir prestanda bättre om du väljer en distributionskolumn.  Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  

Om du till exempel har en ordertabell, som distribueras efter order_id, och en transaktionstabell, som också distribueras efter order_id, och du kopplar ordertabellen till transaktionstabellen baserat på order_id, så blir den här frågan en direktfråga, vilket innebär att vi eliminerar dataflyttningsåtgärder.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.  

När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  Artiklarna som följer ger ytterligare information om hur du förbättrar prestanda genom att välja en distributionskolumn och hur du definierar en distribuerad tabell i WITH-satsen i CREATE TABLES-satsen.

Se även [Tabellöversikt,](sql-data-warehouse-tables-overview.md) [Tabelldistribution,](sql-data-warehouse-tables-distribute.md) [Välja tabellfördelning,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [SKAPA TABELL](sql-data-warehouse-tables-overview.md)och SKAPA TABELL [SOM SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Överpartitionera inte

Medan partitionering data kan vara effektivt för att underhålla dina data genom partitionsväxling eller optimera genomsökningar med partition eliminering, med för många partitioner kan sakta ner dina frågor.  

Ofta kanske en strategi för hög granularitetspartitionering som kan fungera bra på SQL Server inte fungerar bra på SQL-pool.  För många partitioner kan också minska effektiviteten i grupperade columnstore-index om varje partition har färre än 1 miljoner rader.  

Tänk på att bakom kulisserna partitionerar SQL-poolen dina data åt dig i 60 databaser, så om du skapar en tabell med 100 partitioner resulterar detta faktiskt i 6000 partitioner.  Alla arbetsbelastningar är olika så det bästa rådet är att experimentera med partitioneringen för att se vad som fungerar bäst med din arbetsbelastning.  

> [!TIP]
> Överväg att använda en lägre granularitet än vad som kan ha fungerat för dig i SQL Server.  Överväg exempelvis att använda veckovisa eller månadsvisa partitioneringar i stället för dagliga.

Se även [Tabellpartitionering](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar

INSERT-, UPDATE- och DELETE-instruktioner körs i en transaktion och när de misslyckas måste de återställas.  Du kan minimera risken för en lång återställning genom att minska transaktionsstorlekarna om det går.  Du kan göra det genom att dela upp INSERT-, UPDATE- och DELETE-instruktioner i flera delar.  

Om du till exempel har en INSERT, som du förväntar dig att ta 1 timme, om möjligt, dela upp INSERT i fyra delar, som var och en körs i 15 minuter.  Utnyttja särskilda minimala loggningsärenden, till exempel CTAS, TRUNKATE, DROP TABLE eller INSERT till tomma tabeller, för att minska återställningsrisken.  

Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  Till exempel kan du, i stället för att köra en DELETE-instruktion för att ta bort alla rader i en tabell där order_date var i oktober 2001, partitionera dina data månadsvis och sedan byta ut partitionen med data mot en tom partition från en annan tabell (se ALTER TABLE-exemplen).  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda DELETE.  Om en CTAS tar lika lång tid är det en mycket säkrare åtgärd att köra eftersom den har minimal transaktionsloggning och kan avbrytas snabbt om det behövs.

Se även [Förstå transaktioner](sql-data-warehouse-develop-transactions.md), [Optimera transaktioner,](sql-data-warehouse-develop-best-practices-transactions.md) [Tabellpartitionering,](sql-data-warehouse-tables-partition.md) [TRUNKERA TABELL](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), ALTER [TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)och Skapa tabell som [select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek

När du definierar din DDL, med hjälp av den minsta datatyp som stöder dina data kommer att förbättra frågeprestanda.  Den här metoden är särskilt viktig för KOLUMNERNA CHAR och VARCHAR.  

Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera också kolumner som VARCHAR när det är allt som krävs i stället för att använda NVARCHAR.

Se även [Tabellöversikt,](sql-data-warehouse-tables-overview.md) [Tabelldatatyper](sql-data-warehouse-tables-data-types.md)och [SKAPA TABELL](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller

Klustrade columnstore-index är ett av de mest effektiva sätten att lagra data i SQL-poolen.  Som standard skapas tabeller i SQL-poolen som Clustered ColumnStore.  

> [!NOTE]
> För att uppnå optimal prestanda för frågor på columnstore-tabeller är det viktigt att ha bra segmentkvalitet.  

När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  

Se [orsakerna till dålig columnstore-indexkvalitet](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) i artikeln [Tabellindex](sql-data-warehouse-tables-index.md) för steg-för-steg-instruktioner om hur du identifierar och förbättrar segmentkvaliteten för klustrade columnstore-tabeller.  

Eftersom högkvalitativa columnstore-segment är viktiga är det en bra idé att använda användar-ID:er som tillhör den medelstora eller stora resursklassen för inläsning av data. Om du använder lägre [informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) innebär du att du vill tilldela en större resursklass till din inläsningsanvändare.

Eftersom columnstore-tabeller i allmänhet inte kommer att skicka data till ett komprimerat columnstore-segment förrän det finns mer än 1 miljon rader per tabell och varje SQL-biljardtabell är uppdelad i 60 tabeller, drar kolumnstore-tabeller i allmänhet inte nytta av en fråga om inte tabellen har mer än 60 miljoner rader.  

För en tabell med mindre än 60 miljoner rader kanske det inte är meningsfullt att ha ett columnstore-index.  Men det skadar inte heller.  

Om du partitionerar data bör du dessutom tänka på att varje partition måste innehålla 1 miljon rader för att kunna dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den ha minst 6 miljarder rader för att dra nytta av ett klusterkolumnerarklagre (60 distributioner *100 partitioner* 1 miljon rader).  

Om din tabell inte innehåller 6 miljarder rader i det här exemplet minskar du antalet partitioner eller överväger att använda en heap-tabell i stället.  Det kan också vara värt att experimentera och se om du kan förbättra prestanda med en heap-tabell med sekundära index i stället för en columnstore-tabell.

> [!TIP]
> När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [Tabellindex](sql-data-warehouse-tables-index.md), [Columnstore index guide](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)och [Återskapa columnstore index](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Nästa steg

Om du inte hittar det du letar efter i den här artikeln kan du prova att använda "Sök efter dokument" till vänster på den här sidan för att söka igenom alla Azure Synapse-dokument.  

[Azure Synapse Forum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) är en plats där du kan ställa frågor till andra användare och till Azure Synapse-produktgruppen.  Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  

Om du föredrar att ställa dina frågor i Stack Overflow finns det även ett [Stack Overflow-forum för Azure SQL Data Warehouse](https://stackoverflow.com/questions/tagged/azure-sqldw).

Använd sidan [Azure Synapse Feedback](https://feedback.azure.com/forums/307516-sql-data-warehouse) för att göra funktionsförfrågningar.  Genom att skicka in dina egna önskemål eller rösta fram andras förfrågningar hjälper du oss att prioritera funktioner.
