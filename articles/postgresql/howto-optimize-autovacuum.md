---
title: Optimera autovacuum i Azure Database for PostgreSQL-server
description: Den här artikeln beskrivs hur du kan optimera autovacuum i Azure Database for PostgreSQL-server.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 91f24bf90f3e1a8a0c383a5820c6816748090807
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629259"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>Optimera autovacuum på Azure Database for PostgreSQL-server 
Den här artikeln beskriver hur du optimerar effektivt autovacuum på Azure Database för PostgreSQL.

## <a name="overview-of-autovacuum"></a>Översikt över autovacuum
PostgreSQL använder MVCC för att tillåta större samtidighet för databasen. Alla uppdateringar som resulterar i en insert och delete och varje delete resulterar i raderna som mjuk markerats för borttagning. Mjuk-märkning resulterar i identifiering av döda tupplar som ska rensas senare. PostgreSQL uppnår detta genom att köra ett Dammsuga jobb.

Ett Dammsuga jobb kan aktiveras manuellt eller automatiskt. Mer döda tupplar ska finnas när databasen har stor uppdatering eller ta bort åtgärder och färre vid inaktivitet.  Behovet av att köra vakuum oftare är större när databasen är hårt belastad; Gör Dammsuga jobb som körs **manuellt** olämplig.

Autovacuum kan konfigureras och fördelar från justering. De standardvärden som PostgreSQL levereras med försöker se till att produkten fungerar på alla typer av enheter, inklusive Raspberry bearbetningsinstruktioner och perfekt konfigurationsvärdena beror på ett antal faktorer:
- Totalt antal resurser som är tillgängliga - SKU och lagringsstorlek.
- Resursanvändning.
- Enskilda objektegenskaper.

## <a name="autovacuum-benefits"></a>Autovacuum fördelar
Om du inte kör vakuum då och då, kan dead tupplar som ackumuleras leda till:
- Data Bukta ut - större databaser och tabeller.
- Större icke-optimal index.
- Ökad i/o.

## <a name="monitoring-bloat-with-autovacuum-queries"></a>Övervaka överdriven storlek med autovacuum frågor
Följande exempelfråga är utformad för att identifiera antalet inaktiva och aktiva tupplar i en tabell med namnet ”XYZ” ”: Välj %{relname/, n_dead_tup, n_live_tup, (n_dead_tup / n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_ tabeller var %{relname/ = ”XYZ” order som n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Autovacuum konfigurationer
De konfigurationsparametrar som styr autovacuum kretsar kring två viktiga frågor:
- När ska den starta?
- Hur mycket bör den Rensa när den har startat?

Nedan finns några av de autovacuum konfigurationsparametrar som du kan uppdatera baserat på dessa frågor, tillsammans med lite vägledning:
Parameter|Beskrivning|Standardvärde
---|---|---
autovacuum_vacuum_threshold|Anger det minsta antalet uppdaterade eller borttagna tupplar som behövs för att utlösa VAKUUM i samma tabell. Standardvärdet är 50 tupplar. Den här parametern kan bara anges i filen postgresql.conf eller server från kommandoraden. Inställningen kan åsidosättas för enskilda tabeller genom att ändra tabellen storage parametrar.|50
autovacuum_vacuum_scale_factor|Anger en bråkdel av tabellstorleken på för att lägga till i autovacuum_vacuum_threshold när du bestämmer om du vill utlösa VAKUUM. Standardvärdet är 0,2 (20 procent av tabellstorleken). Den här parametern kan bara anges i filen postgresql.conf eller server från kommandoraden. Inställningen kan åsidosättas för enskilda tabeller genom att ändra tabellen storage parametrar.|5 procent
autovacuum_vacuum_cost_limit|Anger kostnaden gränsvärdet som ska användas i den automatiska VAKUUM åtgärder. Om -1 anges (vilket är standardinställningen) används reguljära vacuum_cost_limit värdet. Värdet fördelas proportionellt mellan de pågående autovacuum arbetare om det finns fler än en worker. Summan av begränsningarna för varje arbetsroll överstiger inte värdet för den här variabeln. Den här parametern kan bara anges i filen postgresql.conf eller server från kommandoraden. Inställningen kan åsidosättas för enskilda tabeller genom att ändra tabellen storage parametrar.|-1
autovacuum_vacuum_cost_delay|Anger Fördröjningsvärdet kostnaden som ska användas i den automatiska VAKUUM åtgärder. Om -1 anges, används reguljära vacuum_cost_delay värdet. Standardvärdet är 20 millisekunder. Den här parametern kan bara anges i filen postgresql.conf eller server från kommandoraden. Inställningen kan åsidosättas för enskilda tabeller genom att ändra tabellen storage parametrar.|20 ms
autovacuum_nap_time|Anger den minsta fördröjningen mellan autovacuum körs på en viss databas. I varje omgång daemon undersöker databasen och utfärdar VAKUUM och analysera kommandon efter behov för tabeller i databasen. Fördröjningen mäts i sekunder och standardvärdet är en minut (1 min). Den här parametern kan bara anges i filen postgresql.conf eller server från kommandoraden.|15 s
autovacuum_max_workers|Anger det maximala antalet autovacuum processer (andra än autovacuum starta) som körs samtidigt. Standardvärdet är tre. Den här parametern kan bara ställas in på servern start.|3
Konfigurationerna som ovan kan uppdateras med hjälp av Azure portal eller Azure CLI.

## <a name="autovacuum-cost"></a>Autovacuum kostnad
Nedan visas ”kostnaderna” för att köra en Dammsuga åtgärd:
- Ett lås för de data som sidor vakuum körs på.
- Beräkning och minne används när vakuum körs.

Detta innebär att vakuum bör inte köra antingen för ofta eller för sällan måste vara anpassningsbar på arbetsbelastningen. Vi rekommenderar att du testar alla autovacuum parametern ändringar på grund av nackdelar med var och en.

## <a name="autovacuum-start-trigger"></a>Autovacuum start utlösare
Autovacuum utlöses när antalet döda tupplar överskrider autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples reltuples här är en konstant.

Rensning från autovacuum måste hålla jämna steg med databasbelastning, annars du slut på lagringsutrymme och få en allmän långsammare frågor. Amorteras över tid, frekvensen med vilken vakuum rensar dead tupplar ska vara lika med den hastighet med vilken döda tupplar skapas.

Databaser med många uppdateringar/borttagningar har mer döda tupplar och behöver mer diskutrymme. I allmänhet databaser med många uppdateringar/borttagningar förmånen från låga värden för autovacuum_vacuum_scale_factor och låga värden för autovacuum_vacuum_threshold att förhindra långvarig anhopning av döda tupplar. Du kan använda högre värden för båda parametrarna med mindre databaser eftersom behovet av vakuum är mindre brådskande. Påminnelse att frekventa vacuuming kommer bekostnad av beräkning och minne.

Standard skalfaktor på 20 procent fungerar bra för tabeller med en låg procent döda tupplar, men inte på tabeller med en hög procent av döda tupplar. En 20 GB-tabell som detta översätts till 4 GB döda tupplar och för en 1 TB-tabell är det till exempel 200 GB döda tupplar.

Du kan ange dessa parametrar med PostgreSQL, på den tabell nivå eller instansen. Idag kan du ange dessa parametrar på tabellnivå endast i Azure Database för PostgreSQL.

## <a name="estimating-the-cost-of-autovacuum"></a>Beräkna kostnaden för autovacuum
Köra autovacuum är ”kostsamma” och det finns parametrar för att kontrollera körtiden för Dammsuga åtgärder. Följande parametrar att beräkna kostnaden för att köra vakuum:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Dammsuga processen läser fysiska sidor och söker efter döda tupplar. Varje sida i shared_buffers anses ha en kostnad av 1 (vacuum_cost_page_hit), anses alla andra sidor ha en kostnad av 20 (vacuum_cost_page_dirty) om döda tupplar finns eller 10 (vacuum_cost_page_miss) om det finns inga döda tupplar. Dammsuga åtgärden stoppar när processen överskrider autovacuum_vacuum_cost_limit.  

När gränsen har nåtts vilar processen för den tid som anges av parametern autovacuum_vacuum_cost_delay innan startas igen. Om inte gränsen, startar autovacuum efter värdet som anges av parametern autovacuum_nap_time.

Sammanfattningsvis ska styra parametrarna autovacuum_vacuum_cost_delay och autovacuum_vacuum_cost_limit hur mycket Datarensning tillåts per tidsenhet. Observera att standardvärdena är för låg för de flesta prisnivåer. De optimala värdena för dessa parametrar är priserna nivå-beroende och bör konfigureras i enlighet med detta.

Parametern autovacuum_max_workers anger det maximala antalet autovacuum processer som kan köras samtidigt.

Du kan ange dessa parametrar med PostgreSQL, på den tabell nivå eller instansen. Idag kan du ange dessa parametrar på tabellnivå endast i Azure Database för PostgreSQL.

## <a name="optimizing-autovacuum-per-table"></a>Optimera autovacuum per tabell
Alla ovanstående konfigurationsparametrar kan konfigureras per tabell, till exempel:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum är en per tabell synkron processen. Större procent av döda tupplar en tabell har högre ”cost” till autovacuum.  Dela upp tabeller som har en hög andel uppdateringar/borttagningar i flera tabeller hjälper till att parallellisera autovacuum och minska ”cost” för att slutföra autovacuum på en tabell. Du kan också öka antalet parallella autovacuum arbetare så arbetare liberally är schemalagda.

## <a name="next-steps"></a>Nästa steg
Granska följande PostgreSQL-Hämtningsinformation mer information om använda och justera autovacuum:
 - PostgreSQL-dokumentation – [kapitel 18, serverkonfiguration](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - PostgreSQL-dokumentation – [kapitel 24, vanliga uppgifter för databasunderhåll](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
