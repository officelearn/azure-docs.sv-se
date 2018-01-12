---
title: Rekommendationer - Azure SQL Database | Microsoft Docs
description: "Azure SQL-databasen innehåller rekommendationer för SQL-databaser som kan förbättra aktuella frågeprestanda."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: sstein
ms.openlocfilehash: ea1069d4ec29ad66562a6798a8b13998d0d2ef89
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="performance-recommendations"></a>Prestandarekommendationer

Azure SQL Database lär sig och anpassar sig med ditt program och ger anpassade rekommendationer så att du maximera prestandan för SQL-databaser. Prestandan utvärderas kontinuerligt genom att analysera din användningshistorik för SQL-databas. De rekommendationer som tillhandahålls baserat på ett mönster för databasen unika arbetsbelastning och förbättra prestandan.

> [!TIP]
> [Automatisk justering](sql-database-automatic-tuning.md) är det rekommenderade sättet att prestandajustering. [Intelligent insikter](sql-database-intelligent-insights.md) är det rekommenderade sättet att övervaka prestanda. 
>

## <a name="create-index-recommendations"></a>Skapa index-rekommendationer
Azure SQL Database övervakar frågor som körs kontinuerligt och identifierar de index som kan förbättra prestanda. När tillräckligt med förtroende skapas som ett visst index saknas, en ny **skapa index** rekommendation kommer att skapas. Azure SQL Database bygger förtroende genom att uppskatta prestandafördelar indexet skulle ta över tiden. Beroende på den uppskattade prestandafördelar kategoriseras rekommendationer som hög, medel eller låg. 

Index som skapats med hjälp av rekommendationer flaggas alltid som auto_created index. Du kan se vilka index är auto_created genom att titta på sys.indexes vyn. Automatiskt skapade index blockera inte ALTER/Byt namn på kommandon. Om du försöker ta bort kolumnen som har ett automatiskt skapat index över den kommandot skickar och den automatiskt skapade index släpps med kommandot samt. Vanliga index skulle blockera kommandot ALTER/Byt namn på kolumner som indexeras.

När indexrekommendationen skapa används, kommer Azure SQL Database jämföra prestanda för frågorna med en baslinje-prestanda. Om nytt index föras förbättringar av prestanda, rekommendation flaggas lyckas och rapporten blir tillgängliga. Om indexet inte sätta fördelarna, kommer den att återställas automatiskt. Det här sättet Azure SQL Database ser till att använda rekommendationer endast förbättras databasens prestanda.

Alla **skapa index** rekommendation har en tillbaka av princip som inte tillåter att tillämpa rekommendationen om resursanvändningen för en databas eller poolen är hög. Tillbaka av principen tas till kontot CPU, Data-i/o, logg-i/o och tillgängligt lagringsutrymme. Om CPU-, Data-i/o- eller logg-i/o är högre än 80% i de senaste 30 minuterna skapa ska index skjutas upp. Om tillgängligt lagringsutrymme kan vara under 10% när indexet har skapats kan att rekommendation försättas i feltillstånd. Om efter några dagar automatisk justering fortfarande anser index är lämplig startar processen igen. Den här processen upprepas tills det finns tillräckligt med lagringsutrymme för att skapa ett index eller index visas inte längre som bra.

## <a name="drop-index-recommendations"></a>DROP index-rekommendationer
Förutom identifiering av ett index som saknas, analyserar Azure SQL Database kontinuerligt prestanda för katastrofåterställning. Om inte index används rekommenderar Azure SQL Database släpper den. Du bör släppa ett index i två fall:
* Index är en dubblett av ett annat index (samma indexerade och ingår kolumn, partition schemat och filter)
* Index används inte för långa perioder (93 dagar)

DROP index-rekommendationer kan också gå igenom verifieringen efter implementering. Om prestanda förbättras blir rapporten påverkan tillgänglig. Om prestandaförsämring upptäcks kommer rekommendation att återställas.


## <a name="parameterize-queries-recommendations"></a>Parameterstyra frågor rekommendationer
**Parameterstyra frågor** rekommendationer visas när du har en eller flera frågor som är kontinuerligt som kompileras men slut in med samma körning frågeplanen. Det här villkoret öppnas en möjlighet att tillämpa framtvingad parameterisering, vilket innebär att frågeplaner cachelagras och återanvändas i framtida förbättrar prestandan och minska Resursanvändning. 

Varje fråga som ursprungligen utfärdade mot SQL Server måste kompileras för att generera en åtgärdsplan. Varje genererade plan har lagts till i cacheminnet för planen och efterföljande körningar av samma fråga kan återanvända den här planen från cache, vilket eliminerar behovet av ytterligare sammanställning. 

Program som skickar frågor, bland annat icke-parameteriserat värden, kan leda till en prestanda försämras där för varje sådan fråga med olika parametervärden körningsplanen kompileras igen. I många fall samma frågor med annan värden generera samma körning planer, men dessa planer är fortfarande separat lagts till i cacheminnet för planen. Kompilera körning planer använda databasresurser ökar frågan varaktighet och overflow plancachen orsakar planer för att ta bort från cachen. Det här beteendet av SQL Server kan ändras genom att ange alternativet framtvingad parameterization i databasen. 

För att hjälpa dig att beräkna effekten av den här rekommendationen finns med en jämförelse mellan faktiska CPU-användningen och planerade CPU-användning (som om rekommendationen har tillämpats). Förutom besparing av CPU minskar din fråga varaktighet för den tid som ägnats åt kompilering. Också är mycket mindre omkostnader på plancache, vilket gör att merparten av planerna att stanna kvar i cacheminnet och återanvändas. Du kan använda den här rekommendationen snabbt och enkelt genom att klicka på den **tillämpa** kommando. 

När du använder den här rekommendationen aktiveras framtvingad parameterization inom minuter på din databas som startar övervakningsprocessen som varar cirka 24 timmar. Efter den här tiden kommer du kunna se verifieringsrapporten som visar CPU-användningen för din databas 24 timmar innan och efter rekommendationen har tillämpats. SQL Database Advisor har en säkerhetsmekanism som återställs automatiskt tillämpade rekommendationen om en prestanda regression har upptäckts.

## <a name="fix-schema-issues-recommendations-preview"></a>Åtgärda schemat problem rekommendationer (förhandsgranskning)

> [!IMPORTANT]
> Microsoft håller på att sluta ”åtgärda problemet schema” rekommendationer. Du bör börja använda [Intelligent insikter](sql-database-intelligent-insights.md) för automatisk övervakning av ditt problem med databasprestanda som innehåller schema problem som tidigare ”åtgärda problemet schema” rekommendationer som omfattas.
> 

**Åtgärda problem med schemat** rekommendationer visas när SQL Database-tjänsten meddelanden avvikelser i antalet schema-relaterade SQL-fel som händer på din Azure SQL-databas. Denna rekommendation visas vanligen när databasen påträffar flera schema-relaterade fel (Ogiltigt kolumnnamn, Ogiltigt objektnamn osv.) inom en timme.

”Schema” problem är en typ av syntaxfel i SQL Server som inträffar när definitionen av SQL-frågan och definitionen av databasschemat inte är justerade. Till exempel en av kolumnerna som förväntades av frågan kan saknas i måltabellen eller tvärtom. 

”Åtgärda problemet schema” rekommendation visas när Azure SQL Database-tjänsten meddelanden avvikelser i antalet schema-relaterade SQL-fel som händer på din Azure SQL-databas. I följande tabell visas de fel som är relaterade till problem med schemat:

| SQL-felkod | Meddelande |
| --- | --- |
| 201 |Proceduren eller funktionen '*'förväntar parametern'*', som inte har angetts. |
| 207 |Ogiltigt kolumnnamn ' *'. |
| 208 |Ogiltigt objektnamn ' *'. |
| 213 |Kolumnnamnet eller antalet angivna värden stämmer inte med tabelldefinitionen. |
| 2812 |Det gick inte att hitta lagrade proceduren ' *'. |
| 8144 |Proceduren eller funktionen * har för många argument. |

## <a name="next-steps"></a>Nästa steg
Övervaka dina rekommendationer och fortsätta att använda dem för att förbättra prestanda. Databasarbetsbelastningar är dynamiska och ändra kontinuerligt. SQL Database advisor fortsätter att övervaka och ger rekommendationer som kan förbättra dina databasprestanda. 

* Se [Azure SQL Database automatisk justering](sql-database-automatic-tuning.md) för automatisk justering av databasen och frågeplaner för körning.
* Se [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) automatiskt övervaka databasprestanda med automatisk diagnostik- och rot orsaka analys av prestandaproblem.
* Se [rekommendationer i Azure portal](sql-database-advisor-portal.md) stegvisa instruktioner för hur du använder rekommendationer i Azure-portalen.
* Se [insikter i frågeprestanda](sql-database-query-performance.md) Lär dig mer om och visa påverkan på prestandan för din de vanligaste frågorna.


