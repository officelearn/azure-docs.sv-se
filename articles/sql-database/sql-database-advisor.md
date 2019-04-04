---
title: Prestandarekommendationer – Azure SQL Database | Microsoft Docs
description: Azure SQL Database innehåller rekommendationer för SQL-databaser som kan förbättra aktuella frågeprestanda.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: d09adbfa7cb2782d710ef3116cbd7bc68ee247b7
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903327"
---
# <a name="performance-recommendations-for-sql-database"></a>Prestandarekommendationer för SQL-databas

Azure SQL Database lärdomar och tips med ditt program. Det ger anpassade rekommendationer som hjälper dig att maximera prestanda i dina SQL-databaser. SQL Database utvärderar kontinuerligt och analyserar användningshistorik för dina SQL-databaser. Rekommendationer som tillhandahålls är baserade på databasen unika arbetsbelastningmönster och hjälp oss att förbättra prestanda.

> [!TIP]
> [Automatisk justering](sql-database-automatic-tuning.md) är den rekommenderade metoden för att justera några av de vanligaste prestandaproblemen för databasen. [Fråga Prestandainsikter](sql-database-query-performance.md) är den rekommenderade metoden för grundläggande Azure SQL Database prestandaövervakningsbehov. [Azure SQL-analys](../azure-monitor/insights/azure-sql.md) är den rekommenderade metoden för avancerad övervakning av databasprestanda i skala, med inbyggd intelligens för prestandafelsökning av automatiserade.
>

## <a name="create-index-recommendations"></a>Skapa indexrekommendationer
SQL Database övervakar de frågor som körs kontinuerligt och identifierar index som kan förbättra prestanda. När det finns tillräckligt med säker på att ett visst index saknas, en ny **skapa index** rekommendation skapas.

 Azure SQL Database bygger förtroende genom att uppskatta prestandaökning indexet skulle ta genom tid. Beroende på den uppskattade prestandaökningen kategoriseras rekommendationer som hög, medel eller låg. 

Index som skapas med hjälp av rekommendationer är alltid flaggade som automatiskt skapade index. Du kan se vilka index är automatiskt skapade genom att titta på vyn sys.indexes. Automatiskt skapade index blockera inte ALTER/Byt namn på kommandon. 

Om du försöker ta bort kolumnen som har ett automatiskt skapade index över den skickar kommandot. Det automatiskt skapade indexet släpps med kommandot också. Vanliga index Blockera kommandot ALTER/Byt namn på kolumner som ska indexeras.

När indexrekommendation skapa används, jämför Azure SQL Database prestanda för frågor med baslinje-prestanda. Om det nya indexet bättre prestanda, rekommendationen flaggas som slutförd och rapporten är tillgänglig. Om indexet inte förbättra prestanda, har det återställas automatiskt. SQL Database använder den här processen för att se till att rekommendationer förbättras databasens prestanda.

Alla **skapa index** rekommendation har en backoff-princip som inte tillåter Tillämpandet av rekommendationen om resursanvändningen för en databas eller en pool är hög. Backoff-principen har i kontot CPU, Data-i/o, logg-i/o och tillgängligt lagringsutrymme. 

Om CPU, Data-i/o och logg-i/o är högre än 80% i de föregående 30 minuterna, uppskjuten indexrekommendation skapa. Om tillgängligt lagringsutrymme kommer att vara under 10% när indexet har skapats, hamnar rekommendationen i ett feltillstånd. Om du efter ett par dagar, automatisk justering fortfarande anser att indexet skulle vara fördelaktigt, startar processen igen. 

Den här processen upprepas tills det finns tillräckligt med ledigt lagringsutrymme för att skapa ett index eller tills indexet inte ses som fördelaktigt längre.

## <a name="drop-index-recommendations"></a>Ta bort indexrekommendationer
Förutom identifierar index som saknas, analyserar kontinuerligt prestanda för befintliga index i SQL-databas. Om ett index inte används, rekommenderar Azure SQL Database släppa den. Släppa ett index rekommenderas i två fall:
* Indexet är en dubblett av ett annat index (samma indexerade och ingår kolumn, partition schemat och filter).
* Indexet har inte använts för en längre period (93 dagar).

Ta bort indexet rekommendationer också gå igenom verifiering efter implementering. Om prestandan förbättras är i rapporten tillgänglig. Om prestandaförsämringar är rekommendationen återställd.


## <a name="parameterize-queries-recommendations"></a>Parameterisera frågorna-rekommendationer
*Parameterisera frågorna* rekommendationer visas när du har en eller flera frågor som är ständigt som kompileras men slutet in med samma körning frågeplanen. Det här tillståndet skapar en möjlighet att tillämpa Tvingad parameterisering. Framtvingad parameterisering kan i sin tur frågeplaner för att cachelagras och återanvändas i framtiden, vilket förbättrar prestanda och minskar resursanvändningen. 

Varje fråga som utfärdas mot SQL Server från början måste kompileras om du vill generera en Körningsplan. Varje genererad plan har lagts till plancachen. Efterföljande körningar av samma fråga kan återanvända den här planen från cachen, vilket eliminerar behovet av ytterligare kompilering. 

Frågor med icke-parameteriserat värden kan leda till prestanda försämras eftersom körningsplanen är kompileras varje gång de icke-parameteriserat värdena är olika. I många fall kan generera samma frågor med olika parametervärden samma körningsplaner. De här planerna läggs dock fortfarande separat till plancachen. 

Processen för att kompilera körningsplaner använder databasresurser, ökar varaktigheten för frågan och flödar över plancachen. Dessa händelser i sin tur leda till planer på att tas bort från cachen. Den här SQL Server-beteende kan ändras genom att ange alternativet Tvingad parameterisering på databasen. 

För att beräkna effekten av den här rekommendationen, får du en jämförelse mellan faktiska CPU-användning och beräknade processoranvändningen (som om rekommendationen har tillämpats). Den här rekommendationen kan hjälpa dig få CPU-besparingar. Det kan också hjälpa dig minska frågevaraktigheten och omkostnader för plancachen, vilket innebär att flera av planerna kan vara kvar i cacheminnet och återanvändas. Du kan använda den här rekommendationen snabbt genom att välja den **tillämpa** kommando. 

När du har installerat den här rekommendationen kan Tvingad parameterisering inom några minuter i databasen. Den startar övervakningsprocessen som varar i ungefär 24 timmar. Du kan se verifieringsrapporten efter denna period. Den här rapporten visar CPU-användningen för din databas 24 timmar innan och efter rekommendationen har tillämpats. SQL Database Advisor har en säkerhetsmekanism som återställs automatiskt tillämpad rekommendationen om prestanda regression har identifierats.

## <a name="fix-schema-issues-recommendations-preview"></a>Åtgärda problem med rekommendationer för schemat (förhandsversion)

> [!IMPORTANT]
> Microsoft för närvarande avvecklar ”åtgärda schemaproblemet” rekommendationer. Vi rekommenderar att du använder [smarta insikter](sql-database-intelligent-insights.md) att övervaka din databas prestandaproblem, inklusive problem med databasscheman som lyder under ”åtgärda schemaproblemet”-rekommendationer.
> 

**Fixa schemaproblem** rekommendationer visas när SQL Database-tjänsten upptäcker avvikelser i antalet schema-relaterade SQL fel som händer på din SQL-databas. Den här rekommendationen visas normalt när databasen påträffar flera schema-relaterade fel (Ogiltigt kolumnnamn ogiltigt objektnamn och så vidare) inom en timme.

”Schemaproblem” är en klass av syntaxfel i SQL Server. De inträffar när definitionen av SQL-frågan och definitionen av databasschemat inte är justerade. Exempelvis kan kanske en av de kolumner som förväntas av frågan saknas i måltabellen eller vice versa. 

Rekommendationen ”åtgärda schemaproblemet” visas när Azure SQL Database-tjänsten upptäcker avvikelser i antalet schema-relaterade SQL fel som händer på din SQL-databas. I följande tabell visas de fel som är relaterade till problem med databasscheman:

| SQL-felkod | Meddelande |
| --- | --- |
| 201 |Proceduren eller funktionen ”*'förväntar sig parametern'*', vilken inte tillhandahölls. |
| 207 |Ogiltigt kolumnnamn ' *'. |
| 208 |Ogiltigt objektnamn ' *'. |
| 213 |Kolumnnamnet eller antalet angivna värden stämmer inte överens tabelldefinitionen. |
| 2812 |Det gick inte att hitta lagrade proceduren ' *'. |
| 8144 |Proceduren eller funktionen * har för många argument. |

## <a name="custom-applications"></a>Anpassade program

Utvecklare kan överväga att utveckla anpassade program med hjälp av prestandarekommendationer för Azure SQL Database. Alla rekommendationer visas i portalen för en databas kan nås via [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API.

## <a name="next-steps"></a>Nästa steg
Övervaka dina rekommendationer och fortsätta att använda dem för att förbättra prestanda. Databasarbetsbelastningar är dynamisk och ändras kontinuerligt. SQL Database Advisor fortsätter att övervaka och rekommendationer som kan förbättra databasens prestanda. 

* Läs mer om automatisk justering av databasindex och frågeplaner för körning [automatisk justering i Azure SQL Database](sql-database-automatic-tuning.md).
* Mer information om hur du övervakar automatiskt databasprestanda med automatiserade diagnostik och analys av rotorsaker av prestandaproblem finns i [smarta insikter för Azure SQL](sql-database-intelligent-insights.md).
*  Mer information om hur du använder prestandarekommendationer i Azure-portalen finns i [prestandarekommendationer i Azure-portalen](sql-database-advisor-portal.md).
* Se [fråga Prestandainsikter](sql-database-query-performance.md) Lär dig mer om och visa prestandapåverkan från återställning av dina viktigaste frågor.


