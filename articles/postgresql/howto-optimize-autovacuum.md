---
title: Optimera autovacuum på en Azure Database for PostgreSQL-server
description: Den här artikeln beskrivs hur du kan optimera autovacuum på en Azure Database for PostgreSQL-server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: e1b4bf1f9fa956da7a7b0ca1521439002d1ce76b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993428"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql-server"></a>Optimera autovacuum på en Azure Database for PostgreSQL-server 
Den här artikeln beskriver hur du effektivt optimera autovacuum på en Azure Database for PostgreSQL-server.

## <a name="overview-of-autovacuum"></a>Översikt över autovacuum
PostgreSQL använder multiversion samtidighetskontroll (MVCC) för att tillåta större samtidighet för databasen. Alla uppdateringar som resulterar i en insert och delete och varje delete resulterar i rader som mjuk markerats för borttagning. Mjuk-märkning identifierar döda tupplar som ska rensas senare. Om du vill utföra dessa uppgifter körs PostgreSQL ett Dammsuga jobb.

Ett Dammsuga jobb kan aktiveras manuellt eller automatiskt. Mer döda tupplar finns när databasen påträffar tung uppdatera eller ta bort. Det finns färre döda tupplar när databasen är inaktiv. Du behöver vakuum oftare när databasbelastningen är stor, vilket gör Dammsuga jobb som körs *manuellt* olämplig.

Autovacuum kan konfigureras och fördelar från justering. De standardvärden som PostgreSQL levereras med försök att säkerställa att produkten fungerar på alla typer av enheter. Enheterna omfattar Raspberry bearbetningsinstruktioner. Perfekt konfigurationsvärdena beror på den:
- Totalt antal resurser som är tillgängliga, till exempel SKU och lagringsstorlek.
- Resursanvändning.
- Enskilda objektegenskaper.

## <a name="autovacuum-benefits"></a>Autovacuum fördelar
Om du inte vakuum då och då, kan dead tupplar som ackumuleras leda till:
- Data Bukta ut, till exempel större databaser och tabeller.
- Större icke-optimal index.
- Ökad i/o.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Övervaka överdriven storlek med autovacuum frågor
Följande exempelfråga har utformats för att identifiera antalet inaktiva och aktiva tupplar i en tabell med namnet XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Autovacuum konfigurationer
De konfigurationsparametrar som styr autovacuum baseras på svaren på två viktiga frågor:
- När ska den starta?
- Hur mycket bör den Rensa när den har startat?

Här följer några autovacuum configuration parametrar som du kan uppdatera baserat på föregående frågor, tillsammans med lite vägledning.

Parameter|Beskrivning|Standardvärde
---|---|---
autovacuum_vacuum_threshold|Anger det minsta antalet uppdaterade eller borttagna tupplar som behövs för att utlösa en Dammsuga åtgärd i samma tabell. Standardvärdet är 50 tupplar. Ange den här parametern endast i filen postgresql.conf eller server från kommandoraden. Om du vill åsidosätta inställningen för enskilda tabeller, ändra parametrarna table storage.|50
autovacuum_vacuum_scale_factor|Anger en bråkdel av tabellstorleken på för att lägga till i autovacuum_vacuum_threshold när du bestämmer om du vill utlösa en Dammsuga åtgärd. Standardvärdet är 0,2, vilket är 20 procent av tabellstorleken. Ange den här parametern endast i filen postgresql.conf eller server från kommandoraden. Om du vill åsidosätta inställningen för enskilda tabeller, ändra parametrarna table storage.|5 procent
autovacuum_vacuum_cost_limit|Anger kostnaden gränsvärdet automatisk Dammsuga verksamhet. Om -1 anges som är standard, används reguljära vacuum_cost_limit-värdet. Om det finns fler än en worker, fördelas värdet proportionellt mellan körs autovacuum arbetare. Summan av begränsningarna för varje arbetsroll överstiger inte värdet för den här variabeln. Ange den här parametern endast i filen postgresql.conf eller server från kommandoraden. Om du vill åsidosätta inställningen för enskilda tabeller, ändra parametrarna table storage.|-1
autovacuum_vacuum_cost_delay|Anger kostnaden fördröjningsvärde som används i automatisk Dammsuga åtgärder. Om -1 anges, används reguljära vacuum_cost_delay-värdet. Standardvärdet är 20 millisekunder. Ange den här parametern endast i filen postgresql.conf eller server från kommandoraden. Om du vill åsidosätta inställningen för enskilda tabeller, ändra parametrarna table storage.|20 ms
autovacuum_nap_time|Anger den minsta fördröjningen mellan autovacuum körs på en viss databas. I varje omgång daemon undersöker databasen och utfärdar VAKUUM och analysera kommandon efter behov för tabeller i databasen. Fördröjningen mäts i sekunder och standardvärdet är en minut (1 min). Ange den här parametern endast i filen postgresql.conf eller server från kommandoraden.|15 s
autovacuum_max_workers|Anger det maximala antalet autovacuum processer, än starta autovacuum som kan köras samtidigt. Standardvärdet är tre. Ange den här parametern om du bara början server.|3

Ändra parametrarna table storage för att åsidosätta inställningarna för enskilda tabeller. 

## <a name="autovacuum-cost"></a>Autovacuum kostnad
Här är ”kostnaderna” för att köra en Dammsuga åtgärd:

- Datasidor som vakuumet körs på är låsta.
- Beräkning och minne används när en Dammsuga jobbet körs.

Därför kan inte köra Dammsuga jobb antingen för ofta eller för sällan. En Dammsuga jobbet behöver för att anpassa sig till arbetsbelastningen. Testa alla autovacuum parametern ändringar på grund av nackdelar med var och en.

## <a name="autovacuum-start-trigger"></a>Autovacuum start utlösare
Autovacuum utlöses när antalet döda tupplar överskrider autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Här kan är reltuples en konstant.

Rensning från autovacuum måste hålla jämna steg med databasbelastning. Annars kan du slut på lagringsutrymme och få en allmän långsammare frågor. Amorterad med tiden, den hastighet som rensar en Dammsuga åtgärd döda tupplar ska vara lika med den hastighet med vilken döda tupplar skapas.

Databaser med många uppdateringar och borttagningar har mer döda tupplar och behöver mer diskutrymme. I allmänhet databaser med många uppdaterar och tar bort förmånen från låga värden för autovacuum_vacuum_scale_factor och autovacuum_vacuum_threshold. Låga värden förhindrar långvarig anhopning av döda tupplar. Du kan använda högre värden för båda parametrarna med mindre databaser eftersom behovet av vacuuming är mindre brådskande. Frekventa vacuuming kommer bekostnad av beräkning och minne.

Standard skalfaktor på 20 procent fungerar bra för tabeller med en liten andel av döda tupplar. Det fungerar inte bra för tabeller med en hög andel döda tupplar. Till exempel på en tabell med 20 GB innebär den här skalfaktor 4 GB döda tupplar. På en tabell med 1 TB är 200 GB döda tupplar.

Du kan ange dessa parametrar med PostgreSQL, på den tabell nivå eller instansen. Idag kan du ange dessa parametrar på tabellnivå endast i Azure Database för PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Beräkna kostnaden för autovacuum
Köra autovacuum är ”kostsamma” och det finns parametrar för att kontrollera körtiden för Dammsuga åtgärder. Följande parametrar att beräkna kostnaden för att köra vakuum:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Dammsuga processen läser fysiska sidor och söker efter döda tupplar. Varje sida i shared_buffers anses ha en kostnad av 1 (vacuum_cost_page_hit). Alla andra sidor anses ha en kostnad av 20 (vacuum_cost_page_dirty), om döda tupplar finns eller 10 (vacuum_cost_page_miss), om det finns inga döda tupplar. Dammsuga åtgärden stoppar när processen överskrider autovacuum_vacuum_cost_limit. 

När gränsen har nåtts vilar processen för den tid som anges av parametern autovacuum_vacuum_cost_delay innan den startar igen. Om inte gränsen, startar autovacuum efter värdet som anges av parametern autovacuum_nap_time.

Sammanfattningsvis ska styra parametrarna autovacuum_vacuum_cost_delay och autovacuum_vacuum_cost_limit hur mycket Datarensning tillåts per tidsenhet. Observera att standardvärdena är för låg för de flesta prisnivåer. De optimala värdena för dessa parametrar är priserna nivå-beroende och bör konfigureras i enlighet med detta.

Parametern autovacuum_max_workers anger det maximala antalet autovacuum processer som kan köras samtidigt.

Du kan ange dessa parametrar med PostgreSQL, på den tabell nivå eller instansen. Idag kan du ange dessa parametrar på tabellnivå endast i Azure Database för PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optimera autovacuum per tabell
Du kan konfigurera alla tidigare konfigurationsparametrar per tabell. Här är ett exempel:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum är en synkron process per tabell. Större procentandelen döda tupplar som en tabell har, desto större ”cost” till autovacuum. Du kan dela tabeller som har en hög andel uppdateringar och borttagningar i flera tabeller. Dela upp tabeller hjälper till att parallellisera autovacuum och minska ”cost” för att slutföra autovacuum på en tabell. Du kan också öka antalet parallella autovacuum arbetare så att anställda liberally är schemalagda.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder och finjustera autovacuum finns i följande PostgreSQL-dokumentation:

 - [Kapitel 18, serverkonfiguration](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Kapitel 24, rutinen uppgifter för databasunderhåll](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
