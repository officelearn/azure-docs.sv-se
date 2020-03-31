---
title: Prestandarekommendationer för databasrådgivare för enstaka och poolade databaser
description: Azure SQL Database innehåller rekommendationer för enskilda och poolade databaser som kan förbättra frågeprestanda i Azure SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: bd7473813722fd413947535413b98d493058634a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214142"
---
# <a name="database-advisor-performance-recommendations-for-single-and-pooled-databases"></a>Prestandarekommendationer för databasrådgivare för enstaka och poolade databaser

Azure SQL Database lär sig och anpassar sig med ditt program. För enstaka och poolade databaser har SQL Database ett antal databasrådgivare som ger anpassade rekommendationer som gör att du kan maximera prestanda. Dessa databasrådgivare utvärderar och analyserar kontinuerligt användningshistoriken och ger rekommendationer baserat på arbetsbelastningsmönster som hjälper till att förbättra prestanda.

## <a name="performance-overview"></a>Prestandaöversikt

Prestandaöversikt ger en sammanfattning av databasens prestanda och hjälper dig med prestandajustering och felsökning.

![Prestandaöversikt för Azure SQL Database](./media/sql-database-performance/performance-overview-annotated.png)

- Panelen **Rekommendationer** innehåller en uppdelning av justeringsrekommendationer för databasen (de tre översta rekommendationerna visas om det finns fler). Om du klickar på den här panelen går du till **[Alternativ för prestandarekommendation](sql-database-advisor-portal.md#viewing-recommendations)**.
- Panelen **Justeringsaktivitet** innehåller en sammanfattning av de pågående och slutförda justeringsåtgärderna för databasen, vilket ger dig en snabb visning av historiken för justeringsaktivitet. Om du klickar på den här panelen går du till databasens fullständiga inställningshistorikvy.
- Panelen **Automatisk justering** visar **[databasens konfiguration](sql-database-automatic-tuning-enable.md)** för automatisk justering (justeringsalternativ som tillämpas automatiskt på databasen). Om du klickar på den här panelen öppnas dialogrutan för automatiseringskonfiguration.
- Panelen **Databasfrågor** visar sammanfattningen av frågeprestanda för databasen (övergripande DTU-användning och de vanligaste resurskrävande frågorna). Om du klickar på den här panelen går du till **[Query Performance Insight](sql-database-query-performance.md)**.

## <a name="performance-recommendation-options"></a>Alternativ för prestandarekommendation

Alternativ för prestandarekommendation som är tillgängliga för enskilda och poolade databaser i Azure SQL Database är:

| Rekommendation om prestanda | Stöd för en enda databas och poolad databas | Stöd för instansdatabas |
| :----------------------------- | ----- | ----- |
| **Skapa indexrekommendationer** - Rekommenderar att du skapar index som kan förbättra prestanda för din arbetsbelastning. | Ja | Inga |
| **Släpp indexrekommendationer** - Rekommenderar borttagning av redundanta och duplicerade index dagligen, med undantag för unika index och index som inte användes under en längre tid (>90 dagar). Observera att det här alternativet inte är kompatibelt med program som använder partitionsväxling och indextips. Det går inte att släppa oanvända index för premium- och affärskritiska tjänstnivåer. | Ja | Inga |
| **Parameterisera frågerekommendationer (förhandsversion)** - Rekommenderar tvingad parameterisering i fall när du har en eller flera frågor som ständigt kompileras om men som slutar med samma frågekörningsplan. | Ja | Inga |
| **Korrigering av schemaproblemrekommendationer (förhandsversion)** – Rekommendationer för schemakorrigering visas när SQL Database-tjänsten märker en avvikelse i antalet schemarelaterade SQL-fel som händer i SQL-databasen. Microsoft är för närvarande inaktuella "Fix schema problem" rekommendationer. | Ja | Inga |

![Prestandarekommendationer för Azure SQL Database](./media/sql-database-performance/performance-recommendations-annotated.png)

Om du vill tillämpa prestandarekommendationer läser du [tillämpa rekommendationer](sql-database-advisor-portal.md#applying-recommendations). Mer om du vill visa status för rekommendationer finns i [Övervakningsåtgärder](sql-database-advisor-portal.md#monitoring-operations).

Du kan också hitta fullständig historik över justeringsåtgärder som har tillämpats tidigare.

## <a name="create-index-recommendations"></a>Skapa indexrekommendationer

SQL Database övervakar kontinuerligt de frågor som körs och identifierar de index som kan förbättra prestanda. När det finns tillräckligt med förtroende för att ett visst index saknas skapas en ny **create index-rekommendation.**

Azure SQL Database skapar förtroende genom att uppskatta prestandaökningen som indexet skulle ge genom tiden. Beroende på den uppskattade prestandaökningen kategoriseras rekommendationerna som höga, medelstora eller låga.

Index som skapas med hjälp av rekommendationer flaggas alltid som automatiskt skapade index. Du kan se vilka index som skapas automatiskt genom att titta på [vyn sys.indexes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Automatiskt skapade index blockerar inte ALTER/RENAME-kommandon.

Om du försöker släppa kolumnen som har ett automatiskt skapat index över den, skickas kommandot. Det automatiskt skapade indexet tas också bort med kommandot. Vanliga index blockerar kommandot ALTER/RENAME i kolumner som indexeras.

När rekommendationen skapa index tillämpas jämför Azure SQL Database prestanda för frågorna med baslinjeprestanda. Om det nya indexet förbättrade prestanda flaggas rekommendationen som lyckad och effektrapporten är tillgänglig. Om indexet inte förbättrade prestanda återställs det automatiskt. SQL Database använder den här processen för att säkerställa att rekommendationer förbättrar databasens prestanda.

Alla skapa indexrekommendation har en back-off-princip som inte tillåter att tillämpa rekommendationen om resursanvändningen för en databas eller pool är hög. **create index** Back-off-principen tar hänsyn till CPU, Data IO, Log IO och tillgänglig lagring.

Om CPU, data-IO eller log IO är högre än 80 % under de föregående 30 minuterna skjuts create indexrekommendationen upp. Om det tillgängliga lagringsutrymmet kommer att vara lägre än 10 % efter att indexet har skapats hamnar rekommendationen i ett feltillstånd. Om, efter ett par dagar, automatisk justering fortfarande tror att indexet skulle vara fördelaktigt, startar processen igen.

Den här processen upprepas tills det finns tillräckligt med tillgängligt lagringsutrymme för att skapa ett index, eller tills indexet inte längre ses som fördelaktigt.

## <a name="drop-index-recommendations"></a>Släpp indexrekommendationer

Förutom att identifiera saknade index analyserar SQL Database kontinuerligt prestanda för befintliga index. Om ett index inte används rekommenderar Azure SQL Database att det ska tas bort. Att släppa ett index rekommenderas i två fall:

- Indexet är en dubblett av ett annat index (samma indexerade och inkluderade kolumn, partitionsschema och filter).
- Indexet har inte använts under en längre period (93 dagar).

Släpp index rekommendationer går också igenom verifieringen efter genomförandet. Om prestanda förbättras är effektrapporten tillgänglig. Om prestanda försämras återställs rekommendationen.

## <a name="parameterize-queries-recommendations-preview"></a>Rekommendationer för parameterisera frågor (förhandsgranskning)

*Parameterize frågor* rekommendationer visas när du har en eller flera frågor som ständigt kompileras om men slutar med samma frågekörningsplan. Det här villkoret skapar en möjlighet att tillämpa tvingad parameterisering. Tvingad parameterisering gör det i sin tur möjligt att cachelagra och återanvändas i framtiden, vilket förbättrar prestanda och minskar resursanvändningen.

Varje fråga som utfärdas mot SQL Server måste först kompileras för att generera en körningsplan. Varje genererad plan läggs till i plancachen. Efterföljande körningar av samma fråga kan återanvända den här planen från cacheminnet, vilket eliminerar behovet av ytterligare kompilering.

Frågor med icke-parameteriserade värden kan leda till prestandaomkostnader eftersom körningsplanen kompileras om varje gång de icke-parameteriserade värdena är olika. I många fall genererar samma frågor med olika parametervärden samma körningsplaner. Dessa planer läggs dock fortfarande separat till i plancachen.

Processen för omkompilering av körningsplaner använder databasresurser, ökar frågevaraktighetstiden och flödar över plancachen. Dessa händelser orsakar i sin tur planer som ska vräkas från cacheminnet. Det här SQL Server-beteendet kan ändras genom att ange alternativet för påtvingad parameterisering i databasen.

För att hjälpa dig att uppskatta effekten av den här rekommendationen får du en jämförelse mellan den faktiska CPU-användningen och den beräknade CPU-användningen (som om rekommendationen tillämpades). Den här rekommendationen kan hjälpa dig att få CPU-besparingar. Det kan också hjälpa dig att minska frågevaraktighet och omkostnader för plancachen, vilket innebär att fler av planerna kan stanna kvar i cacheminnet och återanvändas. Du kan snabbt använda den här rekommendationen genom att välja kommandot **Verkställ.**

När du har tillämpat den här rekommendationen aktiveras tvingad parameterisering inom några minuter i databasen. Det startar övervakningsprocessen, som varar i cirka 24 timmar. Efter den här perioden kan du se valideringsrapporten. Den här rapporten visar cpu-användningen av databasen 24 timmar före och efter att rekommendationen har tillämpats. SQL Database Advisor har en säkerhetsmekanism som automatiskt återställer den tillämpade rekommendationen om prestandaregression har upptäckts.

## <a name="fix-schema-issues-recommendations-preview"></a>Rekommendationer för åtgärda schemaproblem (förhandsversion)

> [!IMPORTANT]
> Microsoft är för närvarande inaktuella "Fix schema problem" rekommendationer. Vi rekommenderar att du använder [Intelligent Insights](sql-database-intelligent-insights.md) för att övervaka dina prestandaproblem i databasen, inklusive schemaproblem som rekommendationerna för "Åtgärda schemaproblem" som tidigare omfattades.

**Korrigeringsschemaproblemrekommendationer** visas när SQL Database-tjänsten märker en avvikelse i antalet schemarelaterade SQL-fel som händer i SQL-databasen. Den här rekommendationen visas vanligtvis när databasen stöter på flera schemarelaterade fel (ogiltigt kolumnnamn, ogiltigt objektnamn och så vidare) inom en timme.

"Schemaproblem" är en klass med syntaxfel i SQL Server. De uppstår när definitionen av SQL-frågan och definitionen av databasschemat inte är justerade. En av de kolumner som förväntas av frågan kan till exempel saknas i måltabellen eller tvärtom.

Rekommendationen "Åtgärda schemaproblem" visas när Azure SQL Database-tjänsten märker en avvikelse i antalet schemarelaterade SQL-fel som händer i DIN SQL-databas. I följande tabell visas de fel som är relaterade till schemaproblem:

| SQL-felkod | Meddelande |
| --- | --- |
| 201 |Förfarande eller funktion *' ' förväntar sig parameter '*', som inte har levererats. |
| 207 |Ogiltigt kolumnnamn '*'. |
| 208 |Ogiltigt objektnamn '*'. |
| 213 |Kolumnnamn eller antal angivna värden matchar inte tabelldefinitionen. |
| 2812 |Det gick inte att hitta den lagrade proceduren *'. |
| 8144 |Procedur eller funktion * har för många argument angivna. |

## <a name="custom-applications"></a>Anpassade program

Utvecklare kan överväga att utveckla anpassade program med hjälp av prestandarekommendationer för Azure SQL Database. Alla rekommendationer som anges i portalen för en databas kan nås via [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API.

## <a name="next-steps"></a>Nästa steg

- Mer information om automatisk justering av databasindex och frågekörningsplaner finns i Automatisk justering av [Azure SQL Database](sql-database-automatic-tuning.md).
- Mer information om hur du automatiskt övervakar databasprestanda med automatiserad diagnostik och grundorsaksanalys av prestandaproblem finns i [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
- Se [Frågeprestandastatistik](sql-database-query-performance.md) om du vill veta mer om och visa prestandapåverkan från dina vanligaste frågor.
