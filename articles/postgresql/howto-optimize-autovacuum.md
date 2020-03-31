---
title: Optimera autovacuum - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du kan optimera autovacuum på en Azure-databas för PostgreSQL - Single Server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1917bd6744e100db54fe959292e29486f8a1784b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770194"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Optimera automatiskt på en Azure-databas för PostgreSQL - Single Server
I den här artikeln beskrivs hur du effektivt optimerar automatiskt på en Azure-databas för PostgreSQL-server.

## <a name="overview-of-autovacuum"></a>Översikt över autovacuum
PostgreSQL använder multiversion samtidighetskontroll (MVCC) för att möjliggöra större databas samtidighet. Varje uppdatering resulterar i en infoga och ta bort, och varje borttagning resulterar i rader som är mjuka markerade för borttagning. Mjuk-märkning identifierar döda tupplar som kommer att rensas senare. För att utföra dessa uppgifter driver PostgreSQL ett vakuumjobb.

Ett vakuumjobb kan utlösas manuellt eller automatiskt. Det finns fler döda tupplar när databasen får stora uppdaterings- eller borttagningsåtgärder. Det finns färre döda tupplar när databasen är inaktiv. Du måste dammsuga oftare när databasbelastningen är tung, vilket gör att köra vakuumjobb *manuellt* obekväma.

Autovacuum kan konfigureras och drar nytta av trimning. Standardvärdena som PostgreSQL levereras med försöker säkerställa att produkten fungerar på alla typer av enheter. Dessa enheter inkluderar Raspberry Pis. De idealiska konfigurationsvärdena beror på:
- Totalt antal tillgängliga resurser, till exempel SKU och lagringsstorlek.
- Resursanvändning.
- Individuella objektegenskaper.

## <a name="autovacuum-benefits"></a>Autovacuum fördelar
Om du inte dammsuger då och då kan de döda tupplar som ackumuleras resultera i:
- Data svälla, till exempel större databaser och tabeller.
- Större suboptimala index.
- Ökad I/O.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Övervaka svälla med autovacuum frågor
Följande exempelfråga är utformad för att identifiera antalet döda och levande tupplar i en tabell med namnet XYZ:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Konfigurationer för automatiskvavacuum
Konfigurationsparametrarna som styr autovacuum baseras på svar på två nyckelfrågor:
- När ska det börja?
- Hur mycket ska det rent efter det börjar?

Här är några konfigurationsparametrar för automatiska inställningar som du kan uppdatera baserat på föregående frågor, tillsammans med lite vägledning.

Parameter|Beskrivning|Standardvärde
---|---|---
autovacuum_vacuum_threshold|Anger det minsta antalet uppdaterade eller borttagna tupplar som behövs för att utlösa en vakuumåtgärd i en tabell. Standard är 50 tupplar. Ange den här parametern endast i filen postgresql.conf eller på serverkommandoraden. Om du vill åsidosätta inställningen för enskilda tabeller ändrar du parametrarna för tabelllagring.|50
autovacuum_vacuum_scale_factor|Anger en bråkdel av tabellstorleken som ska läggas till autovacuum_vacuum_threshold när du bestämmer om en vakuumåtgärd ska utlösas. Standardvärdet är 0,2, vilket är 20 procent av tabellstorleken. Ange den här parametern endast i filen postgresql.conf eller på serverkommandoraden. Om du vill åsidosätta inställningen för enskilda tabeller ändrar du parametrarna för tabelllagring.|5 procent
autovacuum_vacuum_cost_limit|Anger kostnadsgränsvärdet som används vid automatiska vakuumoperationer. Om -1 anges, vilket är standardvärdet, används det vanliga vacuum_cost_limit värdet. Om det finns fler än en arbetare fördelas värdet proportionellt mellan de arbetare som körs automatiskt. Summan av gränserna för varje arbetare överskrider inte värdet för den här variabeln. Ange den här parametern endast i filen postgresql.conf eller på serverkommandoraden. Om du vill åsidosätta inställningen för enskilda tabeller ändrar du parametrarna för tabelllagring.|-1
autovacuum_vacuum_cost_delay|Anger kostnadsfördröjningsvärdet som används vid automatiska vakuumoperationer. Om -1 anges används det vanliga vacuum_cost_delay värdet. Standardvärdet är 20 millisekunder. Ange den här parametern endast i filen postgresql.conf eller på serverkommandoraden. Om du vill åsidosätta inställningen för enskilda tabeller ändrar du parametrarna för tabelllagring.|20 ms
autovacuum_nap_time|Anger minsta fördröjning mellan autovacuum körs på en viss databas. I varje runda undersöker demonen databasen och utfärdar vakuum- och analyskommandon efter behov för tabeller i databasen. Fördröjningen mäts i sekunder och standardvärdet är en minut (1 min). Ange den här parametern endast i filen postgresql.conf eller på serverkommandoraden.|15 s
autovacuum_max_workers|Anger det maximala antalet autovacuumprocesser, förutom autovacuum-startprogrammet, som kan köras samtidigt. Standardvärdet är tre. Ange den här parametern endast vid serverstart.|3

Om du vill åsidosätta inställningarna för enskilda tabeller ändrar du parametrarna för tabelllagring. 

## <a name="autovacuum-cost"></a>Autovacuum kostnad
Här är "kostnaderna" för att köra en vakuumoperation:

- Datasidorna som vakuumet körs på är låsta.
- Beräkning och minne används när ett vakuumjobb körs.

Som ett resultat, kör inte vakuum jobb antingen för ofta eller för sällan. Ett vakuumjobb måste anpassas till arbetsbelastningen. Testa alla autovacuum-parameterändringar på grund av kompromisserna för var och en.

## <a name="autovacuum-start-trigger"></a>Starta utlösare för automatisk start
Autovacuum utlöses när antalet döda tupplar överstiger autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Här är reltuples en konstant.

Rensning från autovacuum måste hålla jämna steg med databasbelastningen. Annars kan lagringsutrymmet ta och uppleva en allmän avmattning i frågor. Amorteras över tiden, den hastighet med vilken en vakuumoperation rensar upp döda tupplar bör vara lika med den hastighet med vilken döda tupplar skapas.

Databaser med många uppdateringar och borttagningar har fler döda tupplar och behöver mer utrymme. I allmänhet drar databaser med många uppdateringar och borttagningar nytta av låga värden för autovacuum_vacuum_scale_factor och autovacuum_vacuum_threshold. De låga värdena förhindrar långvarig ansamling av döda tupplar. Du kan använda högre värden för båda parametrarna med mindre databaser eftersom behovet av dammsugning är mindre akut. Frekvent dammsugning sker på bekostnad av beräkning och minne.

Standardskalafaktorn på 20 procent fungerar bra på tabeller med en låg andel döda tupplar. Det fungerar inte bra på bord med en hög andel döda tupplar. I en tabell på 20 GB översätter den här skalfaktorn till 4 GB döda tupplar. På ett 1 TB-bord är det 200 GB döda tupplar.

Med PostgreSQL kan du ställa in dessa parametrar på tabellnivå eller instansnivå. Idag kan du bara ange dessa parametrar på tabellnivå i Azure Database för PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Uppskatta kostnaden för autovacuum
Köra autovacuum är "kostsamt", och det finns parametrar för att kontrollera körningen av vakuumoperationer. Följande parametrar hjälper till att uppskatta kostnaden för att köra vakuum:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Vakuumprocessen läser fysiska sidor och kontrollerar döda tupplar. Varje sida i shared_buffers anses ha en kostnad på 1 (vacuum_cost_page_hit). Alla andra sidor anses ha en kostnad på 20 (vacuum_cost_page_dirty), om döda tupplar finns, eller 10 (vacuum_cost_page_miss), om inga döda tupplar finns. Vakuumfunktionen avbryts när processen överskrider autovacuum_vacuum_cost_limit. 

När gränsen har uppnåtts förs den process som anges av parametern autovacuum_vacuum_cost_delay innan den startas igen. Om gränsen inte nås startar autovacuum efter det värde som anges av parametern autovacuum_nap_time.

Sammanfattningsvis styr parametrarna autovacuum_vacuum_cost_delay och autovacuum_vacuum_cost_limit hur mycket datarensning som tillåts per tidsenhet. Observera att standardvärdena är för låga för de flesta prisnivåer. De optimala värdena för dessa parametrar är prisnivåberoende och bör konfigureras därefter.

Parametern autovacuum_max_workers bestämmer det maximala antalet autovacuumprocesser som kan köras samtidigt.

Med PostgreSQL kan du ställa in dessa parametrar på tabellnivå eller instansnivå. Idag kan du bara ange dessa parametrar på tabellnivå i Azure Database för PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optimera autovacuum per tabell
Du kan konfigurera alla tidigare konfigurationsparametrar per tabell. Här är ett exempel:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum är en synkron process per tabell. Ju större andel döda tupplar som en tabell har, desto högre "kostnad" för autovacuum. Du kan dela upp tabeller som har en hög uppdateringshastighet och tar bort i flera tabeller. Dela tabeller hjälper till att parallellisera autovacuum och minska "kostnaden" för att slutföra autovacuum på en tabell. Du kan också öka antalet parallella autovacuum arbetare för att säkerställa att arbetstagare är frikostigt schemalagda.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder och justerar autovacuum finns i följande PostgreSQL-dokumentation:

 - [Kapitel 18, Serverkonfiguration](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [Kapitel 24, Rutinmässiga databasunderhållsuppgifter](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
