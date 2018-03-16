---
title: Rekommendationer - Azure SQL Database | Microsoft Docs
description: "Azure SQL-databasen innehåller rekommendationer för SQL-databaser som kan förbättra aktuella frågeprestanda."
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 09/20/2017
ms.author: sstein
ms.openlocfilehash: 47e93d270aa3596430ab5892b2bd9323fba1d12e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="performance-recommendations-for-sql-database"></a>Rekommendationer för SQL-databas

Azure SQL Database lär sig och anpassar sig med ditt program. Den här anpassade rekommendationer att maximera prestandan för SQL-databaser. SQL-databasen kontinuerligt utvärderar och analyserar historik för användning av SQL-databaser. De rekommendationer som tillhandahålls baseras på databas-unikt arbetsbelastning mönster och förbättra prestanda.

> [!TIP]
> [Automatisk justering](sql-database-automatic-tuning.md) är den rekommenderade metoden för prestandajustering. [Intelligent insikter](sql-database-intelligent-insights.md) är den rekommenderade metoden för övervakning av prestanda. 
>

## <a name="create-index-recommendations"></a>Skapa index-rekommendationer
SQL-databas övervakar de frågor som körs kontinuerligt och identifierar de index som kan förbättra prestanda. När det finns tillräckligt med förtroende att ett visst index saknas, en ny **skapa index** rekommendation skapas.

 Azure SQL Database bygger förtroende genom att uppskatta prestandafördelar indexet skulle ta över tiden. Beroende på den uppskattade prestandafördelar kategoriseras rekommendationer som hög, medel eller låg. 

Index som skapas med hjälp av rekommendationer flaggas alltid som skapats automatiskt index. Du kan se vilka index har skapats automatiskt genom att titta på vyn sys.indexes. Index som skapats automatiskt blockera inte ALTER/Byt namn på kommandon. 

Om du försöker ta bort kolumnen som har skapats automatiskt index över den skickar kommandot. Automatiskt skapade indexet har släppts med kommandot samt. Vanliga index Blockera kommandot ALTER/Byt namn på kolumner som indexeras.

När indexrekommendationen skapa används, jämför Azure SQL Database prestanda för frågorna med en baslinje-prestanda. Om det nya indexet bättre prestanda, rekommendationen har flaggats som slutförd och påverkan rapporten är tillgänglig. Om indexet inte förbättras prestanda, har den återställas automatiskt. SQL-databasen använder den här processen för att se till att rekommendationer förbättras databasens prestanda.

Alla **skapa index** rekommendation har en tillbaka av princip som tillåter inte att tillämpa rekommendationen om resursanvändningen för en databas eller poolen är hög. Principen inte hänsyn till kontot CPU, Data-i/o, logg-i/o och tillgängligt lagringsutrymme. 

Om CPU-, Data-i/o- eller logg-i/o är högre än 80% under de föregående 30 minuterna, skjuts indexrekommendationen skapa. Om tillgängligt lagringsutrymme ska vara under 10% när indexet har skapats kan försätts rekommendationen i ett feltillstånd. Om du efter ett par dagar automatisk justering fortfarande anser att indexet är lämplig, startar processen igen. 

Den här processen upprepas tills det finns tillräckligt med lagringsutrymme för att skapa ett index eller tills index inte är längre visas som bra.

## <a name="drop-index-recommendations"></a>DROP index-rekommendationer
Förutom identifiering index som saknas, analyserar SQL-databasen kontinuerligt prestanda för katastrofåterställning. Om ett index inte används rekommenderar Azure SQL Database släpper den. Du bör släppa ett index i två fall:
* Indexet är en dubblett av ett annat index (samma indexerade och ingår kolumn, partition schemat och filter).
* Indexet har inte använts under långa perioder (93 dagar).

DROP index-rekommendationer kan också gå igenom verifieringen efter implementering. Om prestanda förbättras är påverkan rapporten tillgänglig. Om prestandaförsämringar har rekommendationen återställts.


## <a name="parameterize-queries-recommendations"></a>Parameterstyra frågor rekommendationer
*Parameterstyra frågor* rekommendationer visas när du har en eller flera frågor som är kontinuerligt som kompileras men slut in med samma körning frågeplanen. Det här villkoret skapar en möjlighet att tillämpa framtvingad parameterization. Framtvingad parameterization kan i sin tur frågeplaner cachelagras och återanvändas i framtiden, vilket förbättrar prestanda och minskar Resursanvändning. 

Varje fråga som utfärdas mot SQL Server från början måste kompileras för att generera en åtgärdsplan. Varje genererade plan har lagts till i cacheminnet för planen. Efterföljande körningar av samma fråga kan återanvända den här planen från cache, vilket eliminerar behovet av ytterligare sammanställning. 

Frågor med icke-parameteriserat värden kan leda till prestanda försämras eftersom körningsplanen är kompileras varje gång de icke-parameteriserat värdena är olika. I många fall kan generera samma frågor med olika parametervärden samma körning planer. Dessa planer läggs dock fortfarande separat till plancachen. 

Processen att kompilera körning planer använder databasresurser ökar frågan varaktighet och plancachen är full. Dessa händelser i sin tur orsaka planer för att ta bort från cachen. Den här SQL Server-beteende kan ändras genom att ange alternativet framtvingad parameterization i databasen. 

För att hjälpa dig att beräkna effekten av den här rekommendationen finns med en jämförelse mellan faktiska CPU-användningen och planerade CPU-användning (som om rekommendationen har tillämpats). Denna rekommendation kan hjälpa dig få CPU besparingar. Det kan också hjälpa dig minska frågan varaktighet och omkostnader för plancache, vilket innebär att flera av planerna kan hålla i cacheminnet och återanvändas. Du kan använda den här rekommendationen snabbt genom att välja den **tillämpa** kommando. 

När du har installerat den här rekommendationen kan framtvingad parameterization inom minuter på din databas. Den startar övervakningsprocessen som varar i cirka 24 timmar. Du kan se verifieringsrapporten för denna period. Den här rapporten visar CPU-användningen för din databas 24 timmar innan och efter rekommendationen har tillämpats. SQL Database Advisor har en säkerhetsmekanism som återställs automatiskt tillämpade rekommendationen om prestanda regression har upptäckts.

## <a name="fix-schema-issues-recommendations-preview"></a>Åtgärda schemat problem rekommendationer (förhandsgranskning)

> [!IMPORTANT]
> Microsoft för närvarande sluta ”åtgärda problemet schema” rekommendationer. Vi rekommenderar att du använder [Intelligent insikter](sql-database-intelligent-insights.md) att övervaka din databas prestandaproblem, inklusive schemat frågor som ”åtgärda problemet schema” rekommendationer tidigare omfattas.
> 

**Åtgärda problem med schemat** rekommendationer visas när SQL Database-tjänsten meddelanden avvikelser i antal schema-relaterade SQL-fel som händer på din SQL-databas. Denna rekommendation visas vanligen när databasen påträffar flera schema-relaterade fel (Ogiltigt kolumnnamn ogiltigt objektnamn och så vidare) inom en timme.

”Schema” problem är en typ av syntaxfel i SQL Server. De inträffar när definitionen av SQL-frågan och definitionen av databasschemat inte är justerad. Till exempel kanske en av de kolumner som förväntas av frågan saknas i måltabellen eller vice versa. 

Rekommendationen ”åtgärda problemet schema” visas när Azure SQL Database-tjänsten meddelanden avvikelser i antal schema-relaterade SQL-fel som händer på din SQL-databas. I följande tabell visas de fel som är relaterade till problem med schemat:

| SQL-felkod | Meddelande |
| --- | --- |
| 201 |Proceduren eller funktionen '*'förväntar parametern'*', som inte har angetts. |
| 207 |Ogiltigt kolumnnamn ' *'. |
| 208 |Ogiltigt objektnamn ' *'. |
| 213 |Kolumnnamnet eller antalet angivna värden stämmer inte med tabelldefinitionen. |
| 2812 |Det gick inte att hitta lagrade proceduren ' *'. |
| 8144 |Proceduren eller funktionen * har för många argument. |

## <a name="next-steps"></a>Nästa steg
Övervaka dina rekommendationer och fortsätta att använda dem för att förbättra prestanda. Databasarbetsbelastningar är dynamiska och ändra kontinuerligt. SQL Database Advisor fortsätter att övervaka och ger rekommendationer som kan förbättra dina databasprestanda. 

* Läs mer om automatisk inställning av databasen och frågeplaner för körning av [Azure SQL Database automatisk justering](sql-database-automatic-tuning.md).
* Mer information om hur du övervakar automatiskt databasprestanda med automatisk diagnostik- och Rotorsaksanalys av prestandaproblem finns [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
*  Mer information om hur du använder rekommendationer i Azure portal finns [rekommendationer i Azure portal](sql-database-advisor-portal.md).
* Se [insikter i frågeprestanda](sql-database-query-performance.md) Lär dig mer om och visa påverkan på prestandan för din de vanligaste frågorna.


